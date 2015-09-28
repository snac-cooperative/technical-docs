Create an ssh key specifically and only for gitlab, on the local (non-gitlab) machine:
```
ssh-keygen -t rsa -f gitlab
```

Add a stanza for gitlab to your ~/.ssh/config file:

```
Host gitlab gitlab.iath.virginia.edu
user git
Hostname gitlab.iath.virginia.edu
IdentityFile ~/.ssh/gitlab
EscapeChar none
```

Note that the key file name in the -f arg is "gitlab" which matches the IdentityFile line in the config file. 
The default ssh-keygen directory is ~/.ssh/

Add the public key gitlab.pub to gitlab:

- cat ~/.ssh/gitlab.pub
- (copy the text of the public key into your clipboard)
- login to gitlab
- click "Profile settings" (icon of a person, upper right corner)
- click "SSH Keys" (left side nav bar)
- click green "Add SSH Key" button (upper right)
- enter a name for the key, this is human readable, not a file name
- paste the key into the "key" text box
- click "Add key"

You can test the function of the new SSH key:

```
> ssh -T git@gitlab.iath.virginia.edu 
Welcome to GitLab, Tom Laudeman!
```

Create the repo on gitlab.  (log in, create it in either your space or snac's space, and then copy the URL for 
cloning via ssh 

```
git@gitlab.iath...
````
On your already cloned github repo (working directory), add gitlab as a remote:

```
git remote add gitlab git@gitlab.iath... (copied from earlier)
```    
Push every local branch to gitlab
```
git push gitlab --all
```
You might have to change your upstream on a branch if you want to track gitlab instead of github:
```
git branch --set-upstream-to=gitlab/master
```
(or whatever other gitlab branch you want to track)  
This sets the current checked-out branch's upstream to whatever is passed.

Now, when you push/pull/fetch, you can choose what you want to do:
```
git push origin master pushes to remote origin, which is github
git push gitlab master pushes to remote gitlab
git fetch --all fetches both gitlab and github
git fetch gitlab would just fetch gitlab (similarly for origin)
git remote remove origin would remove github as a remote.
```