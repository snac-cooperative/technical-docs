
#### Introduction

This work flow engine is a lightweight request routing tool. In our application it encapsulates our business
processes at a high level. Using a work flow allows us to view the business logic of our applications in a
single table without analyzing application source code. A work flow table is a tabular representation of the
higher level feaures of the application's flow chart. The work flow table is easier to read, easier to
maintain, and more compact than a flow chart.

Architecturally, the work flow engine lives inside web middle-ware. Its function in the middle-ware is to
handle calling the proper high level functions. We have two instances of the work flow engine, because we
separate web UI based workflow from fundamental business (policy, server) issues, rather than conflating the
two.

The work flow engine is an innovation, and it is simple in implementation while being powerful and subtle. The
entire work flow engine is only 2 pages of code. The original concept was developed by Kelton Flinn for use in
the Island of Kesmai as the core of the in-game artificial intelligence (AI). The AI was the brains of
non-player characters, computer run people and creatures: shop keepers, monsters, animals, dragons, and so
on. Kelton's genius was amply demonstrated by players who swore that there was a true AI in the game. In
reality none of the critter brains had more than 35 states.

The relevance to SNAC is that various roles have certain behaviors that depended on interaction with the
environment. The rules for a SNAC editor are different than a SNAC contributor. An editor at NYPL may have
different abilities and behavior than an editor at CDL. Perhaps there is one comprehensive "editor" work flow
with nuances of behavior based on affiations and SNAC privileges. Alternatively, there may be several work
flows which we call "editors", but which vary dramatically in behavior. The work flow engine gives us the
flexibility to determine what is often called "business logic", or "business processes" without rewriting any
of the SNAC application.

Microsoft and some enterprise software providers have work flow solutions. Microsoft Windows Workflow
Foundation (WF) is much larger and more complex than the entire SNAC application. WF supports work flow as a
grand concept across large organizations with dozens of software engineers and hundreds of stakeholders. The
power of WF comes at a price. It is very complex which means that it is hard to write, and hard to
understand. Complexity results in bugs. The work flow engine in SNAC is intentionally more focused. This
results in a more legible work flow which is immune to most classes of bugs. Even better, the SNAC work flows
can be statically analyzed and verified.

https://en.wikipedia.org/wiki/Windows_Workflow_Foundation

Traditional web applications should all use this work flow technology, but often do not. Small web
applications (a maximum of 5 web pages) often use "page controllers" where each page handles its own logic,
and connections between pages are implicit in the links. Larger sites use a "front controller" which is a
single point of control. Both methods rely on work flow expressed in program source code, often many pages of
it.

SNAC generally follows the front controller architecture. However, our work flow is handled by the work flow
engine which deals with the application decision making logic in the front controller. Business process
decisions are handled in the server-side front controller, and we have separate workflow limited browser and
web UI. Web HTTP requests go to the web UI controller, where they are processed and sent to the server
controller. REST calls are also processed and sent to the same sever controller. Thus interactions with the
server internals always follow consistent business and policy workflow.

It is important to remember that nearly all aspects of the current application design involve lightweight
solutions to typical problems. Rather than a comprehensive framework, we have chosen to use a select set of
off the shelf software modules to construct a framework suitable to our needs.


#### Requirements

The workflow engine encapsulates only management. It assumes other code deeper in the application will do the
real work. The work flow is written down in a 4 column state table. Workflow is testable by stepping through
the state table manually, and testing is enhanced by a small test frame web application. Workflow is also
testable via computational methods that will validate that the states will (likely) reach an exit, and that
all states are reachable.

The 4 columns are: starting state, boolean transition test, transition function to run, next state. There are
3 pseudo-functions: jump, return, wait. The jump will push the current state onto an internal stack and jump
to a specified new state. The return pops the stack and returns to that state where it immediately transitions
to the next state. The wait is exit since it causes workflow to stop.

Workflow always begins with a default starting state. From a starting node, the boolean transition test is
run. If true, the transition will occur which runs the transition function. If false, the next state of the
same name will run its boolean transition test, and so on. In the case of a true test, the function is run,
the workflow transitions to the next state, and the process repeats until hitting a wait function.

To accomodate multiple boolean transition tests, there can be multiple rows with the same starting state
name. These are tested in the order they occur in the state table. If none of the transition tests are true,
the machine halts with an error. This possibility is revealed during testing. By convention, no transition
test is true, thus any starting state may (and probably must) have a default catch-all. In keeping with
business rules this answers the workflow question "What happens at this step if everything goes wrong?"


### Implementation

The work flow boolean tests and transition functions are symbolic names of actual functions in the SNAC
application. Some of these functions are referred to via function references. Others are anonymous
functions. An internal associative list (hash) has the symbolic name as key, and the function reference as
value.

The number and scope of functions is as extensive as necessary for the desired level of granularity in the
work flow. Viewed as a flow chart, any part of the work flow involving a decision requires an associated
boolean function. Any part of the process that requires a function will have a symbolic name of that function
in the work flow table, and the related function in the application. An application the size of SNAC probably
has 50 or more boolean test functions, and at least that number task functions.
