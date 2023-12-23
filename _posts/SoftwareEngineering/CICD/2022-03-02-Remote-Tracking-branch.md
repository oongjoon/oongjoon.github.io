---
title : "What is Remote Branch:origin/master? "

excerpt: "git remote server"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---
If you are collaborating, you will necessarily be collaborating using a remote repository. A typical example of a remote server is github. I used github a lot, but I didn't know exactly how it would be reflected when it was reflected on the github server. I just thought that it would be ok if there was no error using the push and pull commands. When updating to the github server, if you know how the update is done, and know how it is reflected when it is reflected locally on the github server, you will be able to collaborate better (refer to the bottom of the blog for a reference). (Remote Tracking of progit I read and organized the branch part.)



# Remote Tracking branch

## Property

A remote branch means a branch on a remote server. When the repository on the remote server is reflected to local, a remote tracking branch that tracks the remote branch is automatically created.

This remote tracking branch has the following features:

1. You cannot move arbitrarily.
2. It has the name `<remote><branch>`.

To explain `1`, the remote tracking branch reflects the data at the time of the most recent connection to the remote repository. Because, the remote tracking branch is the reference tracking the branch of the remote repository. Since it is a reference, the remote tracking branch moves as the remote repository is updated. The branch moved is when it was most recently associated with the repository.

Let's take an example to explain `2` . For example, I had an issue and I pushed the iss12 branch to the server. I also have an iss12 branch locally. Then the branch pointed to by iss12 on the server will be `origin/iss12` in local.

As a side note, the name origin is meaningless. Initially, when cloning a remote repository, the default value is origin.





## Scenario

I will explain the scenario well in progit and explain it with it.





![image](https://user-images.githubusercontent.com/50165842/155986736-bb4396dc-6b7b-4be9-8942-3b5780024e4d.png)



When git clone is done, the remote repository in git.ourcomapany.com is downloaded locally. So local will have remote tracking branch `orign/master` , and local will have `master` branch.

![image](https://user-images.githubusercontent.com/50165842/155987281-14dcacf6-0817-431e-b4f1-bd113ff2811e.png)

Work on the local `master` branch and commit. The `master` branch of local will be moved.

Another team member pushes to the remote repository. The `master` branch of the remote repository will be moved. The repository of other team members and the server and my repository will be different. The remote repository and the local repository are independent as long as they don't update the latest information, so they can have different states.

![image](https://user-images.githubusercontent.com/50165842/155987816-1b7491f1-2b42-4d8b-a418-57f22427ccbd.png)

Let's try using git fetch origin to reflect the repository of the remote server from the local. Based on the f4265 commit, two commits are reflected in local. Move `origin/master` . That is, it receives new information that is not in local, updates it locally, and moves the remote branch to the most recent location.



![image](https://user-images.githubusercontent.com/50165842/155988403-b8a514a3-1475-4501-b6b9-7336bea1790a.png)





Let's assume that the application is operated by adding one more storage rather than simply dividing it into local and main storage. The repository to be added at that time will be called git.team1.ourcompany.com.



![image](https://user-images.githubusercontent.com/50165842/156175544-5cff8002-c388-4e4b-8f8f-3fdf7426014c.png)

It reflects the latest data from the new remote server (team1), but the commit log does not change because it is data that already exists locally (commit). A remote tracking branch called `teamone/master` is added.



# Update to remote server

The command used to update to a remote server in git is `git push`. I will try to find out about this command in detail.

## git push

The general format for git push is `git push <reomte> <branch>`. It means that the branch in the local will be reflected to the remote server in the alias of the remote server stored in the local.

To briefly explain the inner workings of git, in `git push <remote> master`, master means `refs/heads/master:refs/heads/master` . This means that the local branch named master is pushed to the remote server, and the update is made to the remote server's master branch. So, it can be rewritten with the command `git push <remote> master:master`. This can be used when the name of the local branch and the branch name of the remote server to update are different. For example, `git push <remote> master:dev` means "update the local master branch to the remote server's dev branch".

# Get from remote server

Now that you know how to apply git to the remote server, let's look at how to get it from the remote server.

## git fetch

The `git fetch <remote>` command will download data that is not local from the remote server. At this time, it should be remembered that the remote tracking branch is a branch that cannot be moved arbitrarily.

To merge the remote tracking branch into the current branch, use a command such as `git merge origin/master` to merge.

If you are creating a new branch, you might be able to use a command like `git checkout -b newma origin/master ` .



## Tracking branch

A tracking branch is automatically created when you checkout a remote tracking branch as a local branch. When you pull from this tracking branch, it automatically receives data from the remote repository and merges it into the tracking branch.

When you do git clone, remote tracking branch `origin/master` and tracking branch `master` are automatically created.

It is also possible to create your own tracking branch. You can create your own tracking branch using `git checkout -b <branch> <remote>/<branch>`.

You can use a command like `git --track <remote><branch>` to automatically generate a tracking branch name.

If there is one remote with the entered branch, and if it is not local, `git checkout <branch>` will create a tracking branch as well.

If you want to use the current branch as the tracking branch, you can use the command `git branch -u <remote><branch>` to set the current branch as the tracking branch. In this case, you can use the option `--set-upstream-to` instead of `-u`.

To cancel tracking for a branch set as a tracking branch, use the `git branch <branch> --unset-upstream` command.

To see information about the tracking branch, you can use the command `git branch -vv`. It shows a list of local tracking branches, showing if the local tracking branch is leading or lagging behind.



## git pull

One thing to note about the git pull command is that it is a command that performs the combined operation of git fetch + git merge. git fetch fetches the latest data that is not locally located from the server. And, git merge merges this data with the current branch.

When the tracking branch is set, the git pull command receives data from the remote server, updates the remote tracking branch, and merges the tracking branch and remote tracking branch.

For explicit operation, the book recommends that git merge after git fetch is better.



# Reference

[Git - Remote Branches (git-scm.com)](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)

