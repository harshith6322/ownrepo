##GIT


0.git config --global user.name "harshith"
  git config --global user.email "user-gmail"
  git config --global --list or cat .gitconfig


1.npm install git  ==>to install git


2. git -v          ==>to know version
   git --version   ==>to current know version

  (image for git workflow)
 

3.git init                ==> initialization of git in new projects
  git clean -f <FileName> ==> Remove untracked files from the working directory.


4.git add <filename> OR * OR .   ==> adding file or folder to staged area

 

5.git commit -m "first commit"
  git commit -a -m "Your commit message here" ==>commit without staging area

  --amend--
  git commit --amend -m "change recent commit message"          ==>used to change the commit message for only latest commit
  git commit --amend --no-edit                                  ==>used to commit the file in stagging area to changes with previous commit
  git commit --amend --author="jack <jack@gmail.co>"            ==>used to change the author of latest commit
  git commit --amend --date="Thu, 07 Apr 2005 22:13:13 +0200"   ==>used to change date and time of an recent commit

  --undo-first-commit--
  git update-ref -d HEAD  ==>used to delete first commit 



 6.git status

 7.git log
   git log --oneline --graph   ==>shows git branch graph and ids
   git log --pretty=oneline    ==>this will only show commit message and commit ids
   git log -2  OR -3                       ==>only shows last 2 commits on screen
   git log --follow --all <FileName>       ==>filename used to see the no of commits for a single file


 8.git rm --cache [file-name.txt]	==>Remove a file (folder) from staging stage and git will not track it it like adding it to .gitignore work any time 
   git restore --staged filename   ==>Remove a file (or folder) from staging area and git still track it and only work when there atlest one commit 
   git reset filename              ==>Remove a file (or folder) from staging area


 9.git reset --soft (HEAD~1 OR commitID)                ==>used to delete only commits but not actions/changes
   git reset --hard (HEAD~1 OR commitID)                ==>used to delete the latest commit along with the changes
   git reset --mixed (this is defult one) (HEAD~1 OR commitID) ==>used to delete commit history and stagging area


   | Option    | Commit History | Staging Area | Working Directory |
| --------- | -------------- | ------------ | ----------------- |
| `--soft`  | ✅ reset        | ✅ keep       | ✅ keep            |
| `--mixed` | ✅ reset        | ❌ clear      | ✅ keep            |
| `--hard`  | ✅ reset        | ❌ clear      | ❌ clear           |


   (image for diff btw soft hard mixed)



 10.git revert <CommitID>    ==> used to delete a particular commit action and add a new commit for the change
    git revert --abort    }
    git revert --skip       ==>   to solve merge conflicts
    git revert --quit       ==>   to solve merge conflicts
    git revert --continue }


 11.git show <CommitID>               ==> To get the commit details
    git show <CommitID>  --stat    ==>To get the commit details along with the changes in filenames
    git show <CommitID> --name-only   ==>To get the commit details along with the changes in files & stats



 12.git branch                                           ==>used to see the list of branches
    git branch -a or --all or -r(remote) or -l(local)  ==>List all branches (local and remote)
    
    git branch branch-name         ==>to create a branch
    git checkout -b branch-name    ==>used to create and switch a branch at a time
    git checkout -b [branch name] origin/[branch name]   ==>Clone a remote branch and switch to it
    git checkout branch-name       ==>to switch one branch to another
    git switch brnach-name         ==>to switch one branch to another
    git checkout -b <new-branch-name>  <commit-hash>   ==> ==>Used to get deleted branch id
 

    git branch -m old-branch new-branch                  ==>used to rename a branch
    git diff [source branch] [target branch]	         ==>Preview changes before merging

    git branch -d branch-name                   ==>to delete a branch
    git branch -D [branch name]                 ==>to delete a branch forcefully
    git push origin --delete [branch name]      ==>Delete a remote branch



13.git merge <branch-name>                      ==> Merging a branch
   git merge [source branch] [target branch]    ==>Merge a branch into a target branch


14 git rebase <branch-name>   ==>combining commits from one branch onto another to make look linear
   git rebase -i HEAD~3        ==> To combine last 3 commits to single commit



15 git clone                      ==>Clone a remote repository to your local machine.
   git clone -b main              ==>clone only specific branch

   git fetch                      ==>Download changes from the remote but don't merge them
   git fetch --all                ==>used to fetch the changes from all branches in github

   git pull                       ==>Update local repository to the newest commit with merge
   git pull origin [branch-name]  ==>Pull changes from remote repository
   git pull --rebase`             ==>Keep your branch up-to-date while avoiding extra merge commits.


16.git remote -v                        ==>To see current remote url
   git remote add origin [URL]          ==>ADD REMOTE REPO
   git remote rm origin  [URL]               ==>used to unlink the github-repo
   git remote set-url origin <GITURL>   ==>To change current remote url to new url


17.git push -u origin [branch name]     ==>Push changes to remote repository (-u => and remember the branch)
   git push -u origin --all             ==>Push all branchs
   git push origin --delete [branch name]      ==>Delete a remote branch




18.git cherry-pick [commit_id]
   git cherry  ==> comapare 2 branches showing ids

    ====>Git cherry-pick is a command in Git that allows you to take a specific commit from one branch
       and apply it to another branch. It's like picking a cherry (commit) from one branch and adding
       it to another branch, allowing you to selectively copy individual commits without merging the
       entire branch.



19.git stash

    ====>Using the git stash command, developers can temporarily save changes made
      in the working directory. It allows them to quickly switch contexts when they are not quite
      ready to commit changes. And it allows them to more easily switch between branches.
      Generally, the stash's meaning is "store something safely in a hidden place."


   git stash save "message"    ==>to save the stash along with the message
   git stash apply             ==>to get back the data again
   git stash list      ==>to get the list of stashes
   git stash clear     ==> to clear all stashes
   git stash pop       ==>apply then delete the first stash 
   git stash drop      ==>used to delete the latest stash
   git stash drop stash@{2}   ==>used to delete a praticular stash




20. git tag

     ====>it tags are markers or labels that you can place on specific commits (versions) in
        a Git repository. These tags provide a way to give meaningful names to important points in
        the project's history, such as software releases or significant milestones.

   git tag          ==>To see the list of tags
   git tag tagname  ==>To create normal tag
   git tag -a -m -s"message" tagname   ==>To create annotated tag
   git tag -d tagname    ==>To delete a tag
   git show tagname      ==>To see the details of the tag
   git push origin v1.0  ==>to push an tag to remote repo


21. git fsck  ====> does not modify anything — it’s purely diagnostic.
    - git fsck --lost-found  --to see full lost data
    - git branch restore-branch <commit-hash>

22. git prune






1.git -v
2.git init
3.git clean -f <filename>
4.git add * or .
5.git rm --cached
6.git restore --staged
7.git commit -am <message>
8.git status
9.git log
10.git show
11.git diff
12.git reset --hard --soft --mixed
13.git revert
14.git branch
15.git checkout
16.git switch
17.git merge
18.git rebase
19.git clone
20.git pull
21.git push
22.git fetch
23.git cherry-pick
24.git stash
25.git tag
26.git remote
27.git reflog
28.git config 
29.git blame
30.git fsck
