
#### Introduction

This work flow engine is a lightweight request routing tool. In our application it encapsulates our business
processes at a high level. Architecturally, it lives inside web middle-ware. Its function in the middle-ware
is to handle calling the proper high level functions. We have two workflow engines, because we separate web
UI based workflow from fundamental business (policy) issues, rather than conflating the two problems.

Small web applications that will always be small (a maximum of 5 web pages) often use "page controllers" where
each page handles its own logic, and connections between pages are implicit in the links. Larger sites use a
"front controller" which is a single point of control.

The the workflow engine handles the application decision making logic in the front controller. Business
process decisions are handled in the server-side front controller, and we have separate workflow limited
browser and UI. Web http requests go to the browser controller, where they are normalized for the server
controller. REST calls are also normalized and sent to the same sever controller. Thus interactions with the
server internals always follow consistent business and policy workflow.

It is important to remember that nearly all aspects of the current application design involve lightweight
solutions to typical problems. Rather than a comprehensive framework, we have chosen to use a select set of
off the shelf software modules to construct a framework suitable to our needs.


#### Requirements

The workflow engine encapsulates only decision making. It assumes other code deeper in the application will do
the real work. The decisions are written down in a 4 column state table. Workflow is testable by stepping
through the state table manually. Workflow is also testable via computational methods that will validate that
the states will reach an exit, and that all states are reachable.

The 4 columns are: starting state, boolean transition test, transition function to run, next state. There are
3 pseudo-functions: jump, return, wait. The jump will push the current state onto an internal stack and jump
to a new state. The return pops the stack and returns to that state where it immediately transitions to the
next state. The wait might be called exit since it causes workflow to stop. 

Workflow always begins with a default starting state. From a starting node, the boolean transition test is
run. If true, the transition will occur. If false, the next state of the same name will run boolean transition
test. If a transition function exists, it will be run (eval'd). The workflow transitions to the next state,
and the process repeats until the wait function.

To accomodate multiple boolean transition tests, there can be multiple rows with the same starting state
name. These are tested in the order they occur in the state table. If none of the transition tests are true,
the machine halts with an error. This possibility is revealed during testing. By convention, no transition
test is true, thus any starting state may (and probably should) have a default catch-all. In keeping with
business rules this answers the workflow question "What happens at this step if everything goes wrong?"


#### Implementation as thought problem

Implementation can be handled several ways which may help you think (extrapolate) how the
system works.

In the first mode, the workflow engine state table's functions are eval'd as literal function calls. For every
function that the state table calls for a given state transition, the function must exist in the system. A
string "unlock_record()" when eval'd will run the function unlock_record. The workflow engine doesn't know
what exactly goes on inside that function, but it does "know" that it will unlock the current record. 

Creation of the workflow involves a shared understanding between the programmer writing the workflow, and the
programmer creating the system code.
