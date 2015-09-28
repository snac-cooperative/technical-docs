# User Interface Requirements

## Web Application

Some aspects of the web app aren't yet clear, so there are details to be worked out, and some large-ish
concepts to clarify. I'm guessing we will agree on most things, and one of us or the other will just concede
on stuff where we don't agree.

Requirements:

- expose an http accessible API that is viable for `wget` or `curl`, browser `<form>`, and Ajax calls.
- Supported input format depends on the complexity of the requested operation.
- Public functions require no authentication. Everything else must include authentication data.
- Sandbox functionality to for training and testing, which doesn't modify actual SNAC data



### Web application output via template

A well known, easy, powerful method of creating presntation output is to use an template module. Templating
separates business logic from presentation logic, thus following an MVC model. Our business logic is our work
flow and related function calls. Presentation is our UI, and the work flow engine has no idea that a UI exists,
let alone how to create it. Curiously, the presentation logic knows how to create the presentation rendering,
but has no idea what it does or what it interacts with. This is another example of strong separation of
concerns.

A simple hello world text template with a single variable world = "world" would be:

```
Hello [% world %]!
```

Or a simple HTML version:

```
<html><body>Hello [% world %]!</body></html>
```

That example is based on the Template Tookit http://www.template-toolkit.org/ for which there is a Perl
module, and a Python module. Template modules are fairly common, so I'm almost certain we will have several to
choose from in PHP.

Choosing our own select software modules, including a template module, is better than being locked into a
large, cumbersome web framework. In general, web frameworks have issues:

- difficult to work with

- no useful functionality that isn't more easily found in another software module

- the often break MVC

- generally make debugging nearly impossible

We can do much better by selecting a few modules to create a lightweight quasi-framework that is perfectly matched to our
needs.

Once the internal API completes its work, we will have output data. Output data is passed to a rendering
layer that relies on the template module. The only code that knows anything about rendering is the rendering
layer. To all the non-rendering code, there is only "output data" which does conform to a standard structure
(almost certainly an output data object). The rendering layer takes the output object, and the requested format
of the output (text, html, pdf, xml, etc.) to create the output. Happily, "rendering" is generally a single
function call. We create a template object, call its "render" method with two arguments:

1. template file name,

2. the output data object.

Default behavior is to write the output to stdout, but the render method can also
return the output in a variable so we can create an http download.

Templates are human created static files containing placeholders. The template engine fills in the placeholders with
values from relevant parts of the output data. Clearly, the output data object and the template must share a
object/property naming convention. The template engine functionality has single value fields, looping over
input lists, and if statement branching based on input. But that's pretty much it. No work is done in the
template that is not directly concerned with filling in placeholders, not even formatting (in the sense of
rounding numbers, capitalizing strings, or adding html tags). Templates are valid documents of the output
type, except in rare cases. The attached template is well-formed XML.

The web app needs a file download output option as well as output to stdout.

### Watching records

Users may "watch" an identity constellation. If a constellation is being watched, and that constellation is part of an description (merged or
single) then the watch will apply to the results of human edits, regardless of which part of the description
was modified. It is possible for someone to wish to track a biogHist, but that biogHist could be completely
removed in lieu of an improved and updated description. We will not track individual elements in CPF.

The watcher should have the ability to disable their watch. After each edit, all
watchers will get a notification. The watch does not apply to any single field, but to the entire description, and therefore also to future descriptions which result from merging.

When an identity constellation is split, the watch propagates to both resulting records.  The user will be informed of the change, and then may choose to disable one of the watchers.

### Ability to Open/Close the Site during Maintenance


If the web application has a "closed for maintenance" feature, this feature would be available to web admins,
even though it is the Linux sysadmins who will do the maintenance. A common major failure of web applications
is the assumption that the product is always up.  This creates havoc when the site simply fails to load due to
an outage, planned or otherwise. With a little work we should be able to have an orderly "site is closed" web
page and status message for planned outages. We might be able to failover to some kind of system status
message. This is a low priority feature since downtime is probably only a few hours per year.  At the same
time, if it isn't too difficult to implement, it sets our project apart from the majority who either ignore
the problem, or let their help desk folks spend an hour apologizing to customers.

When the product is closed, web admins should be able to login (assuming login is possible).

comment: Do we want an architecture where the login is essentially a separate product so that we can have a
"lobby" and other front end features that continue to work even when the backend is down for maintenance?

Most sites simply return a server error or site not available (404) when the site is down for whatever
reason. We can avoid this a couple of ways. The simplest is to use some Apache server features and a few
simple scripts so that users see a nice message when the site is down for maintenance. This very simple
approach requires little or no change to our software architecture. The more elegant approach is to use one of
several system architectures that  keep a small system front end always running.
