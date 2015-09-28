# Software Development Process

Development on the SNAC web application should use agile development practices, with the shortest-possible-but-reasonable sprint size possible.  See [scrum documentation](http://scrummethodology.com/scrum-sprint/) for more detailed information about agile development methods.  Test-driven development should also be employed to automate testing and interconnect testing with the development process.

The git version control system should be used as the repository for code in the application.  It allows distributed editing with highly-configurable branching of development, a "blame" system that allows viewing which developer added a specific line of code, and is cross-platform.  It is also supported by [gitlab](http://gitlab.iath.virginia.edu), which should be used for internal development timelines, milestones, bug- and issue-tracking, and project management.  Final versions of the repositories may then be pushed to the public-facing [github](https://github.com/snac-cooperative) repositories.


## General Discussion Notes

Choices for programming languages, operating system, databases, version
control, and various related tools and practices are based on extensive
experience of the developer community, and a complex set of requirements
for the coding process. Current best practices are agile development
using practices that allow programmers wide leeway for implementation
while still keeping the processes manageable.

Test-driven development ideally means automated testing, with careful
attention to regression testing. It takes some extra time up front to
write the tests. Each test is small, and corresponds to small sections
of code where both code and text can be quickly created. In this way,
the software is kept in a working state with only brief downtimes during
feature creation or bug fixes. Large programs are made up of
intentionally small functions each of which is tested by a small
automated test.

Regression testing refers to verifying that old bugs do not reappear.
Every bug fix has a corresponding test, even if the function in question
did not originally have a test for the bug. Each new bug needs a new
test. Bugs frequently reappear, especially in complex sections of code.

Source code version control is vital to both development process, and to
the release process. During development, frequent small changes are
checked-in to the version control, along with a meaningful comment. The
history of the code can be tracked. This occasionally helps to
understand how bugs come into existence. In the Git system, the history
command is “blame”, a bit of programmer dark humor where the history is
used to know who to blame for a bug (or any undesirable feature).

Moving code into Quality Assurance (QA) and then into the production
environment are both integral with source code management. Many version
control systems allow tagging a release with a name. The collected
source code files are marked as a named (virtual) collection, and can be
used to update a QA area. Human testing and review happens in QA. After
QA we have release. Depending on the nature of the system release can be
quite complex with many parties needing to be notified, and coordination
across groups of developers, sysadmin, managers, support staff, and
customers. Agile development tends towards small, seamless releases on a
frequent (weekly or monthly) basis where communication is primarily via
update of electronic documentation. The process needs to assure that
fixes and new features are documented. The system must have tools to see
the current version of the system with its change log, as well as
comparing that to previous releases. All of these are integrated with
change management.

Bug reporting and feature requests fall (broadly speaking) into the
category of change management. Typically a small group of senior
developers and stakeholders review the bug/feature tracking system to
assign priorities, clarify, and investigate. There are good
off-the-shelf systems for tracking bugs and feature requests, so we have
several choices. This process happens almost as frequently as the
features/bug fix coding work of the developers. That means on-going,
more or less continuous review of fix/features requests every few days,
depending on how independent the developers are. Agile applies to
everyone on the project. Ideal change management is not onerous. As
tasks are completed, someone (developers) update feature status with "in
progress", "completed” and so on. There might be additional status
updates from QA and release, but SNAC probably isn't large enough to
justify anything too complex.

#### QA and Related Tests for Test-driven Development


The data extraction pipelines manage massive amounts of data, and
visually checking descriptions for bugs would be inefficient if not
infeasible. The MARC extraction process is verified by just over 100
quality assurance descriptions. The output produced from each
description is checked for some specific value that confirms that the
code is working correctly and historical bugs have not reappeared. The
EAD extraction has a set of QA files, but the output verification is not
yet automated. A variety of file counts and measures of various sorts
are performed to verify that descriptions have all been processed. All
CPF output is validated against the Relax NG schema. Processing log
files are checked for a variety of error messages. Settings used for
each run are recorded in documentation maintained with the output files.
The source code is stored in a Subversion repository.

Our disaster recovery processes must be carefully documented.
