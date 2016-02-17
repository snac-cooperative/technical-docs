# Data Validation Engine

## Original (Draft) Discussion
- The data validation engine applies a written system of rules to the incoming data.  The rules must be written in a human-readble form, such that non-technical individuals are able to write and understand the rules.  A rule-writing guide must be supplied to give hints and help for writing rules properly.  The engine will be pluggable and written as an MVC application.  The model will read the user-written rules (stored in a postgres database or flat-file system, depending on the model) and apply them to any input given on the view.  The initial view will be a JSON API, which accepts individual fields and returns the input with either validation suggestions or valid flags.
- rule based system abstracted out of the code
- rules are data
- change the rules, not the actual application code
- rules for broad classes of data type, or granular rules for individual fields
- probably used this to untaint data as well (remove things that are potential security problems)
- send all data through this API
- every rule includes a message describing what when wrong and suggesting fixes
- rules potentially editable by non-programmers
- rules are based on co-op data policies, which implies a data policy document, or the rules **can** be the policy documentation.
