# Git Tutorial

### First-time Git setup
```bash
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"
git config --global core.editor emacs #it needs full path on windows
git config --list #list of all settings

#defined aliases in git
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD' #seeing the last commit

git config --global credential.helper cache #save user pass for few minutes on https
```

### For getting help
```bash
git help <verb>
man git <verb>
git <verb> -h or --help
```

### Git Basics
```bash
git init
git add . #add all files
git add *.java #add .java files
git add LICENSE
git commit -m 'initial project version' # -a automatically stage changes without add command
```
#### Git commite message tips
```bash
git clone https://github.com/libgit2/libgit2 dirName #cloning an existing repository
git status # -s or --short
```
`git add` is a multipurpose command you use it to begin tracking new files, to stage files, and to do other things like marking merge-conflicted files as resolved.
```bash
git diff #changed but not yet staged
git diff --staged or --cached #compares your staged changes to your last commit
git diff --check #check whitespace errors before commit
git difftool --tool-help #for GUI
git rm #for untracking file, -f(force) --cached(if you forgot to add to ignore file)
git mv file_from file_to #renaming a file
git log #commit history -p or --patch show difference --stat for abbreviated --pretty=oneline/short/full/fuller/format:"%h - %an, %ar : %s" --since=2.weeks/"2008-01-15"/"2 years 1 day 3 minutes ago"
git log --no-merges issue54..origin/master #display only those commits that are on the latter branch (in this case origin/master) that are not on the first branch
git commit --amend #redo commit to make additional changes you forgot
git reset HEAD <file> #unstaging a file
git checkout -- <file> #undo a modified file, it is a dangerous command!
git instaweb --httpd=webrick #show web view (--stop) 
git reflog #log of where your HEAD and branch references have been for the last few months
git show HEAD@{5}
```
+ Tilde  `~`  go backward in a straight line to go back a number of generations
+ Caret  `^`  suggests an interesting segment of a tree or a fork in the road, on merge commits — because they have two or more (immediate) parents
-   `HEAD~2`  : 2 commits older than HEAD
-   `HEAD^2`  : the second parent of HEAD, if HEAD was a merge, otherwise illegal
-   `HEAD~~`  : 2 commits older than HEAD
-   `HEAD^^`  : 2 commits older than HEAD
-   `HEAD@{2}`  : refers to the 3rd listing in the overview of  `git reflog`

If HEAD was a merge, then
- **first parent**  is the branch into which we merged,
- **second parent**  is the branch we merged.

First Parent | First Grandparent | Second Parent | Second Grandparent
:---:|:---:|:---:|:---:
`HEAD~`| 
`HEAD^`| 
`HEAD~1`|`HEAD~2`|`HEAD^2`|`HEAD^2~`
`HEAD^1`|`HEAD^^`| |`HEAD^2^` 

```bash
git show master@{yesterday} #cannot use for older than a few months
git show HEAD^ # ^(caret) show previous commit, mean the parent of head (in windows head^^ or “head^”)
git show HEAD~ #refers to the first parent
```
[More information](https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection)

### Remote
```bash
git remote #list of remotes -v(shows URLs)
git remote add <shortname> <url> 
git fetch <shortname> #downloads the data to your local repository doesn’t merg
git pull <shortname> #automatically fetch and then merge
git push <remote> <branch>
git push <remote> <local-branch>:<server-branch>
git remote show <remote> or  git ls-remote <remote> #more information about particular remote
git remote rename pb paul #renaming a remote
git remote remove paul #removing
```

### Git Tagging
Git supports two types of tags: *lightweight* and *annotated*. 
A **lightweight tag** is just a pointer to a specific commit. **Annotated tags** are stored as full objects in the Git database. They’re checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG).
```bash
git tag #listing the available tags
git tag -l "v1.8.5*" #list v1.8.5…. Versions
git tag -a v1.4 -m "my version 1.4" #annotated tag
git tag v1.4-lw #lightweight tag
git tag -a v1.2 <hash-commit> #tag old commits
git push origin --tags or <tag-name> #by default  push command doesn’t transfer tags
git tag -d <tagname> #deleting tag
git push <remote> :refs/tags/<tagname> #delete tag on remote servers
```

### Git Branching
```bash
git branch <branch-name> #create new branch
git log --oneline --decorate #shows you where the branch pointers are pointing
git checkout <branch-name> #switching branch
git checkout -b <branch-name> #create new branch and switch
git checkout master #befor merge you should checkout on which branch you want merge on
git merge hotfix #master merge with hotfix
git branch -d hotfix #after that delete hotfix branch
git branch #listing your current branches -v(last commit on each branch) --merged --no-merged(listing merged or not merged branches)
git branch --no-merged master #what is not merged in the master branch
git checkout --track origin/serverfix #create a local branch from serverfix (tracking branches)
git checkout serverfix #if branch exist on remote, Git will create a tracking branch for you
git checkout -b sf origin/serverfix #create a tracking branch with a different name
git branch -u #or --set-upstream-to origin/serverfix If you already have a local branch and want to set it to a remote branch you just pulled down, or want to change the upstream branch you’re tracking if you’re on the master branch and it’s tracking origin/master, you can say git merge @{u} instead of git merge origin/master
git branch -vv #show tracking branches
git push origin --delete serverfix #delete a remote branch
```

### Rebasing
there are two main ways to integrate changes from one branch into another: the merge and the rebase.
```bash
git checkout experiment 
git rebase master #add changes to head
git checkout master #after that merge fast-forward
git merge experiment
git rebase --onto master server client
```

### Git on the server
A remote repository is generally a bare repository ( a Git repository that has no working directory, it is the contents of your project’s .git directory and nothing else. ) choose which protocols you want to your server support: 
+ Local
+ HTTP
+ Secure Shell (SSH)
+ Git
#### Local Protocol
If you have a shared mounted filesystem or in another directory with the same host, in this protocol every user has full shell access to the remote directory
```bash
git clone /srv/git/project.git 
git remote add local_proj /srv/git/project.git #add to an existing project
```
#### HTTP Protocol
smart HTTP and dumb HTTP
```bash
git clone https://example.com/gitproject.git
```
#### SSH Protocol:
```bash
git clone ssh://[user@]server/project.git (or) git clone [user@]server:project.git
```
#### The Git Protocol
It listens on a port 9418, it’s like ssh protocol for using this protocol you must create a **git-daemon-export-ok** file.  
First, you should create a bare repository with `git clone --bare my_project my_project.git`, put the bare repository on a server and setup your SSH protocol on the server,  create a git user account and a .ssh directory for that user
```bash
sudo adduser git
su git
cd
mkdir .ssh && chmod 700 .ssh 
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```
Next, you need to add some developer SSH public keys to the authorized_keys file 
```bash
cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys 
cat /tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys 
cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys
```
Now you can set up an empty repository for them
```bash
cd /srv/git 
mkdir project.git 
cd project.git 
git init --bare
```

### GitLab
[One click installation](https://bitnami.com/stack/gitlab)
https://gitlab.com/gitlab-org/gitlab-ce/tree/master




### .gitignore file
```git
# ignore all .a files
*.a
# but do track lib.a, even though you're ignoring .a files above
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO
/TODO
# ignore all files in any directory named build
build/
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt
# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```
[.gitignore Templates](https://github.com/github/gitignore )




​
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc2NjU5MDM5MF19
-->