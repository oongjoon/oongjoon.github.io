---
title : "Work on Remote Repository"

excerpt: "git remote repo"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , VCS , Git]
# classes : wide
toc: true
toc_sticky: true
---
When developers work on a huge project, they inevitably need to collaborate. For collaboration, collaboration is done using a remote repository.



## Working with remote repositories

Let's say you need to use something called a remote repository. So what is this remote repository, and how can I work with others with it?

### What is remote storage?

A remote repository is a repository that exists on the Internet or a network. In this case, this repository can be a repository in local, or it can be a repository in someone else's local.

### Check the remote repository

To check the remote repository, use the `git remote` command. If you use the -v option, the URL is also displayed.

```
$ git remote
origin

$ git remote -v
origin https://github.com/schacon/ticgit (fetch)
origin https://github.com/schacon/ticgit (push)

```

## Add remote repository

If you use `git clone`, the remote repository is automatically added with the name `origin`. If you want to manually add a remote repository, you can use the `git remote add <name> <URL>` command. In the example below, a remote repository named pb is available.

```
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin https://github.com/schacon/ticgit (fetch)
origin https://github.com/schacon/ticgit (push)
pb https://github.com/paulboone/ticgit (fetch)
pb https://github.com/paulboone/ticgit (push)
```



## Working with remote repositories

### fetch

Using the command `git fetch <name>` will reflect all data and information in the remote repository that is not currently local.

```
$ git fetch <remote>
```

### pull

The command `git pull <name>` will fetch and then merge with the local repository.

```
$ git pull
```

### push

If you want to share your work on an upstream repository, you can use Push. You can do this by using the command `git push <repository name> <branch name>`.

```
$ git push origin master
```

However, this command can only be used if you have write access to the repository.

### Specific information of remote

To check the specific information of the remote repository, you can use `git remote show <repository name>`.

```
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:
  master tracked
  dev-branch tracked
  Local branch configured for 'git pull':
  master merges with remote master
  Local ref configured for 'git push':
  master pushes to master (up to date
```

If you omit the repository name and use it, it shows something like

- Shows which branch is pushed to which branch.
- What are the remote branches that are not imported locally?
- What are the branches that have been deleted from the server but have not been imported?
- What branches are automatically merged when using git pull?

```
$ git remote show origin
* remote origin
  URL: https://github.com/my-org/complex-project
  Fetch URL: https://github.com/my-org/complex-project
  Push URL: https://github.com/my-org/complex-project
  HEAD branch: master
  Remote branches:
  master tracked
  dev-branch tracked
  markdown-strip tracked
  issue-43 new (next fetch will store in
remotes/origin)
  issue-45 new (next fetch will store in
remotes/origin)
  refs/remotes/origin/issue-11 stale (use 'git remote prune' to
remove)
  Local branches configured for 'git pull':
  dev-branch merges with remote dev-branch
  master merges with remote master
  Local refs configured for 'git push':
  dev-branch pushes to dev-branch
(up to date)
  markdown-strip pushes to markdown-strip
(up to date)
  master pushes to master
(up to date)

```

## Modify local remote repository

You've learned how to work with remote repositories. The remote repository may become unnecessary, or the version or purpose of the repository may be changed and renamed or deleted.

### rename

If you need to rename it, you can use `git remote rename <old name> <new name> ` .

```
$ git remote rename pb paul
$ git remote
origin
paul
```



### remove

Once you have deleted the remote repository, you can delete the repository with `git remote rm <repository name> ` ,or `git remote remove <repository name>` .

```
$ git remote remove paul
$ git remote
origin

```
