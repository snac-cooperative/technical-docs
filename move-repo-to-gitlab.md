Create the repo on gitlab.  (log in, create it in either your space or snac's space, and then copy the URL for cloning via ssh (git@gitlab.iath...)

On your already cloned github repo (working directory), add gitlab as a remote:

```git remote add gitlab git@gitlab.iath... (copied from earlier)

Push every local branch to gitlab
'''git push gitlab --all

You might have to change your upstream on a branch if you want to track gitlab instead of github:
'git branch --set-upstream-to=gitlab/master (or whatever other gitlab branch you want to track)  This sets the current checked-out branch's upstream to whatever is passed.

Now, when you push/pull/fetch, you can choose what you want to do:

'git push origin master pushes to remote origin, which is github
'git push gitlab master pushes to remote gitlab
'git fetch --all fetches both gitlab and github
'git fetch gitlab would just fetch gitlab (similarly for origin)
'git remote remove origin would remove github as a remote.