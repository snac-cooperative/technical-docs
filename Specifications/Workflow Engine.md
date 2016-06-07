
### How to integrate the workflow engine

The workflow engine (WFE) communicates with the application via functions. There are two kinds of functions
that the WFE requires: tests and workers. The WFE has internal functions: exit, jump, return. 


The functions do not have arguments by design for two reasons: to simplify implementation, and simplify
use. From a feature standpoint, allowing arguments to to functions would enable the possibility of large
classes of bugs in the workflows, but would not especially enhance the scope of workflow features. Also, as
soon as variables and arguments are added to a workflow, it is less ameniable to static analysis.

Test and worker functions integrate the WFE with existing APIs. Integrated APIs need to expose boolean test
functions that in order for the WFE to test extant attributes of the application. Tests should be side-effect
free. (As regards tests, it is best to say that attributes of the application are being tested, and avoid
saying "the state of the application" and reserver the word "state" as a property of the workflow engine. The WFE
has state, and the application has state, but the contexts are totally separate.) Workers are functions that
get work done. Workers do things, and often change application attributes as an intentional side effect.

There are three internal functions: exit, jump, return. The internal exit halts the WFE. Remember that each
time the WFE is run it starts with the start state and runs through until hitting an exit. The internal jump
pushes the current state onto an internal stack, then goes to the new state. An internal return pops the
stack, goes to that state, and continues. Note that "goes to" means go to the first state of the given
name. If the jump occured at the 3rd "foo" state, return nonetheless returns to the 1st "foo" state.

Which leads us to multiple lines for some states. This state table is a multi-edge table. Each state has
multiple links to other states. These are run in the order the appear in the table. An empty test is true. An
empty worker is a no-op. The state table is sanity checked to confirm that every state has a final true
transition.


### A simple example

Below is a flow chart in lovely ascii graphics


```
+-------------------+
|                   |
|   user submits    +---------+
|   data            |         |
|                   |         |
+-------------------+         v
                         +----+-----+
                         |          |
                         |          +----------------------+
        +----------------+ empty    |                      |
        |    Yes         | data?    |        No            |
        |                |          |                      |
        |                +----------+                      |
        |                                                  v
        |                                         +--------+-------+
        |                                         |                |
        |                                         |                |
        v                                         |   save data    |
+-------+--------+                                |                |
|                |                                |                |
|   empty data   |                                |                |
|   message to   |                                +--------+-------+
|   user         |                                         |
|                |                                         |
|                |                                         v
+----------------+                                 +-------+-------+
                                                   |               |
                                                   |               |
                                                   |  confirming   |
                                                   |  message      |
                                                   |  to user      |
                                                   |               |
                                                   +---------------+
```


#### The corresponding state table is:

| state   | test       | worker             | next-state |
|---------+------------+--------------------+------------|
| start   | data-empty |                    | warn       |
| start   |            | save-data          | confirm    |
| warn    |            | data-empty-message | done       |
| confirm |            | confirm-message    | done       |
| done    |            | exit               |            |


#### From the state table we glean that there is one test:

```
data-empty
```

This test returns true if the submitted data is empty. There are two likely places to put this test function:

1) In a thin-API existing only to integrate the workflow with the API

2) In the API being integrated with the workflow.


#### There are three workers:

```
save-data
data-empty-message
confirm-message
```

Worker save-data saves "the data", whatever that entails. Worker data-empty-message puts an "empty data"
message into the user message that will eventually be shown to the user in the UI. Worker confirm-message puts
a "saved data" confirmation message into the user message.


### Server workflow

Server workflow is currently fairly simple. It is anticipated that some of these worker functions will become
several states, and will require relevant tests.


| state | test                          | worker                | next-state |
|-------+-------------------------------+-----------------------+------------|
| start | command-vocabulary            | search-vocabulary     | done       |
| start | command-reconcile             | reconcile             | done       |
| start | command-start-session         | start-session         | done       |
| start | command-end-session           | end-session           | done       |
| start | command-user-info             | user-info             | done       |
| start | command-insert-constellation  | insert-constellation  | done       |
| start | command-update-constellation  | update-constellation  | done       |
| start | command-unlock-constellation  | unlock-constellation  | done       |
| start | command-publish-constellation | publish-constellation | done       |
| start | command-recently-published    | recently-published    | done       |
| start | command-read                  | read-constellation    | done       |
| start | command-edit                  | edit-constellation    | done       |
| start |                               | unknown-command       | done       |
| done  |                               | exit                  |            |


### Possible server workflow

This is a slight expansion of the current server workflow. This moves authentication from a hard-coded
function into the WFE. Next we add a new function that updates this users notifications by calling into the
yet-to-be-created notification system. It ping pending tasks, and updates notices that show up on the
dashboard. Having gotting these initial tasks out of the way, commands can be processed.

This shows a more detailed view of publishing. Anyone watching this SNAC record gets an update. We also
notifiy the manager of this user. Finally we publish.

Unlocking also has more detailed steps. Start by unlocking, and immediately re-locking to the reviewer. Notify
the reviewer, and update notifications for the user. 


| state         | test                          | worker                | next-state    |
|---------------+-------------------------------+-----------------------+---------------|
| start         | confirm-authentication        |                       | init          |
| start         |                               | authentication-error  | done          |
| init          |                               | update-notification   | docmd         |
|               |                               |                       |               |
| docmd         | command-vocabulary            | search-vocabulary     | done          |
| docmd         | command-reconcile             | reconcile             | done          |
| docmd         | command-start-session         | start-session         | done          |
| docmd         | command-end-session           | end-session           | done          |
| docmd         | command-user-info             | user-info             | done          |
| docmd         | command-insert-constellation  | insert-constellation  | done          |
| docmd         | command-update-constellation  | update-constellation  | done          |
| docmd         | command-unlock-constellation  |                       | unlock-tasks  |
| docmd         | command-publish-constellation |                       | publish-tasks |
| docmd         | command-recently-published    | recently-published    | done          |
| docmd         | command-read                  | read-constellation    | done          |
| docmd         | command-edit                  | edit-constellation    | done          |
| docmd         |                               | unknown-command       | done          |
|               |                               |                       |               |
| publish-tasks |                               | notify-watchers       | pt2           |
| pt2           |                               | notify-manager        | pt3           |
| pt3           |                               | publish-constellation | done          |
|               |                               |                       |               |
| unlock-tasks  |                               | unlock-constellation  | unlock2       |
| unlock2       |                               | lock-to-reviewer      | unlock3       |
| unlock3       |                               | notify-reviewer       | unlock4       |
| unlock4       |                               | update-notification   | done          |
|               |                               |                       |               |
| done          |                               | exit                  |               |




### WebUI workflow

When command-login2 is true, the resulting worker might better be split into several states.

Here command-unlock is split into tests and workers: unlock-constellation, if-error, redirect-dashboard,
return-json. Alternatively, these 4 functions might all be inside a single worker.


| state  | test                 | worker                                                     | next-state |
|--------+----------------------+------------------------------------------------------------+------------|
| start  | empty-session        | init-and-home                                              | done       |
| start  |                      | non-empty-start-snac-session                               | do-cmd     |
| do-cmd | command-login        | destroy-session-and-redirect-home                          | done       |
| do-cmd | command-login2       | login-start-snac-session-serialize-user-redirect-dashboard | done       |
| do-cmd | command-logout       | logout-destroy-session-and-redirect-index                  | done       |
| do-cmd | command-edit         | display-edit-page                                          | done       |
| do-cmd | command-new          | display-new-edit-page                                      | done       |
| do-cmd | command-view         | display-view-page                                          | done       |
| do-cmd | command-preview      | display-preview-page                                       | done       |
| do-cmd | command-dashboard    | display-dashboard                                          | done       |
| do-cmd | command-profile      | display-profile                                            | done       |
| do-cmd | command-save         | save-constellation-return-json                             | done       |
| do-cmd | command-save-unlock  | save-unlock-return-json                                    | done       |
| do-cmd | command-unlock       | unlock-constellation                                       | unlock     |
| do-cmd | command-save-publish | save-publish-return-json                                   | done       |
| do-cmd | command-publish      | publish-return-json                                        | done       |
| do-cmd | command-vocabulary   | vocabulary-search-return-json                              | done       |
| do-cmd | command-search       | name-search-return-json                                    | done       |
| do-cmd |                      | display-landing-page                                       | done       |
| unlock | if-error             | redirect-dashboard                                         | done       |
| unlock |                      | return-json                                                | done       |
| done   |                      | exit                                                       |            |



