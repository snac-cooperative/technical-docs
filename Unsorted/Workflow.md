Internal flow:

1. validate the inputs.

1. Somehow slice and dice the CGI params of the REST call into an abstracted request we can pass to the
internal API. I suppose that the external and internal APIs are very similar, but we almost certainly need
some level of symbolic reference aka abstraction. Each REST call has its requisite data. Some data is as
simple as a record id, and some will be fairly interesting json data structures.

1. The web app API does the tasks specified by the REST request and the work flow engine's directions.

  1. Every http request must go through the work flow engine so that the work flow is validated and managed.

  1. Every web app has a work flow, but people mostly just cobble that together with a bunch of implied
    functionality using conditionals and side-effect-full function calls. In our code, the internal API is
    100% work flow agnostic.

  1. I can explain this in more detail, but it makes a huge improvement in the structure of the application.

1. Create the output data object if it wasn't created by the functions doing the work.

1. Pass the output data to a rendering function (or module) to be rendered into the appropriate output format:
html, text, xml, etc. and sent to stdout, or returned as an http file download. JSON probably doesn't need to
be rendered since JSON is "data" and not "presentation".

The work flow engine relies on functions that read application data and return booleans so that the
work flow engine can detect the application's relevant state. I guess that sounds confusing because the work
flow engine has state, and the application has state. Those two types of state are vastly different and only
related to each other in that the work flow engine can detect the application's state. The internal API of the
web app has no idea that the work flow engine even exists. And the work flow engine knows what work needs to
be done, but has no idea how it will be done. This is a very lovely separation of concerns.
