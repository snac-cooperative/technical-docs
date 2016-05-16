
### How to integrate the workflow engine

The workflow engine (WFE) communicates with the application via functions. There are two kinds of functions
that the WFE requires: test and workers. The WFE has internal functions: exit, jump, return. 


The functions do not have arguments, which is by design, to simplify implementation, and use. From a feature
standpoint, allowing arguments to to functions would enable the possibility of large classes of bugs in the
workflows, but would not especially enhance the scope of workflow features. Also, as soon as variables and
arguments are added to a workflow, it is no longer proveable.

A set of functions integrates the WFE with existing APIs. Integrated APIs need to expose boolean test
functions that in order for the WFE to test extant attributes of the application. Tests should be side-effect
free. (As regards tests, it is best to say that attributes of the application are being tested, and avoid
saying "the state of the application" and only refer to "state" as a property of the workflow engine. The WFE
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

| state   | test       | function           | next-state |
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


