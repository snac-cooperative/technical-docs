### Working locally and version control

The Git technology was created to track revisions to many files. Gitlab provides a web site with some ability
to edit files, but usually people work on their local computers.

It is a more efficient work flow to work on files locally on your own computer, and use git tools to push the
modified files back to the Gitlab repository. 

#### SSH key requirements

Generate an ssh key:
```
ssh-keygen -t dsa
```

Add the key via your gitlab profile settings -> ssh keys.
Edit your ~/.ssh/config file to contain a section like the following:

```
Host gitlab gitlab.iath.virginia.edu
user git
Hostname gitlab.iath.virginia.edu
IdentityFile ~/.ssh/gitlab_id_dsa
EscapeChar none
```

To see the ssh key finger print, use the -lf switch:
```
ssh-keygen -lf ~/.ssh/gitlab_id_dsa
```


Important: the URL to "clone" a repository can be found on each project's main page. It will be an SSH URL beginning with "git..." or an HTTP URL beginning with "http..."

```
git@gitlab.iath.virginia.edu:snac/Documentation.git

http://gitlab.iath.virginia.edu/snac/Documentation.git
```

Do this: Go to the Gitlab home by clicking the logo (some kind of animal) in the upper left corner. On the
home page click "snac / Documentation". Notice the two buttons at the top center of the page "SSH" and
"HTTP". Click each to change the URL. That URL is what you clone, whether using command line git tools, or a
graphical git client application.

#### SourceTree, Windows and Mac graphical client application for git repositories, including Gitlab and Github.
https://www.sourcetreeapp.com/

#### A few brief notes about using SourceTree are at the end of the FAQ
https://www.sourcetreeapp.com/faq/

#### Install or upgrade git, necessary for ssh-keygen. 
https://confluence.atlassian.com/display/STASH/Installing+and+upgrading+Git

#### Github docs about keys. Mostly applies to Gitlab. Requires Git.
https://help.github.com/articles/generating-ssh-keys/

#### Notes on generating ssh keys. Requires Git. Deploy keys don't apply to the SNAC project.
http://doc.gitlab.com/ce/ssh/README.html

#### Creating ssh keys on Windows. Requires Git.
https://confluence.atlassian.com/display/STASH/Creating+SSH+keys#CreatingSSHkeys-CreatinganSSHkeyonWindows

#### Mac github, requires 10.9, probably github only.
https://mac.github.com/
