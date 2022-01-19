---
title : "Git file state and convert to each other"

excerpt: "Git file state"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , VCS]
# classes : wide
toc: true
toc_sticky: true
---

## Git file state and convert to each other

So far, I have classified the file status of git only as a committed state and a non-committed state. And, I've actually managed version files like that. However, files in git exist in a granular state. In fact, there may be no problems using VCS (Version Control System) by only dividing into two categories: files that are committed and files that are not. However, I am sure that if I know more in detail, I can use VCS better for the situation. Today, I studied the contents of the file state in Git Pro and wrote it as a closed book, and supplemented the lacking contents. Let's explain the File State in detail.



## Git File State

![image](https://user-images.githubusercontent.com/50165842/149623044-613f5ea0-afe8-48bc-9724-31c8b46b6bef.png)

The file status of git is largely divided into UNTRACKED and TRACKED. (The meaning of this will be explained in detail later.) TRACKED is a file managed by git, that is, a file already included in the snapshot. UNTRACKED means files not managed by git. In this TRACKED, it is again divided into MODIFIED, UNMODIFIED and STAGED. UNTRACKED is a file in the working directory that is not included in the staging area. When Git is cloned for the first time, the files are in UNMODIFIED state. Because, after the first checkout, nothing has been modified. If you modify this  , it will be in MODIFIED state. MODIFIED means that it has been modified compared to the most recent version, and UNMODIFIED means that it is the same as the most recent version. And, you can make this MODIFIED FILE into a staged state. STAGED is the state just before saving to the git server. When STAGED is reached, the STAGED state file is committed.



### git status

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

You can check the status of a file with the `git status` command.  The above means nothing has been modified.

```bash
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)
  README
nothing added to commit but untracked files present (use "git add" to
track)

```

If you add a new `README` file, you can see that `README` has been added to the UNTRACKED FILE. Above, we said that the TRACKED state means the file was in the snapshot. UNTRACKED means it is not in the snapshot.

### git add

#### UNTRCKED FILE -> STAGING AREA

```
$ git add README

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
```

It says `Changes to be committed`, which means that the file below has reached STAGED status.

The git add command is a command to start tracking this file from now on. Using git add will put the file on the STAGING AREA.
You can give a file or directory as an argument, and if you give a directory, all files are recursively placed on the STAGING AREA.



#### MODIFIED FILE

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

`Changes not staged for commit` means that it is being TRACKED, but that changes have been made and not yet STAGED, i.e. MODIFIED.

The git add command is also used to make a MODIFIED FILE STAGED.

#### git add의 다른 용도

git add is also used to resolve conflicts that occur when merging. This is because we are going to merge and commit.



### git add 후 modified

This time, we will upload `CONTRIBUTING.md` to the STAGING AREA using the git add command.

```bash
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
  modified: CONTRIBUTING.md
```

Let's fix this and check the status of the file again.

```bash
$ vim CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
  modified: CONTRIBUTING.md
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

You can see that the `CONTRIBUTING.md` file is both STAGED and MODIFIED. If COMMIT is executed at this point, the file at the time of the last add becomes a commit. If you want to commit the modified file, you must convert the MODIFIED file to STAGED state by executing git add.

### git diff

When I use git status it simply shows that the file is MODIFIED. You should use git diff to see what specifically changed. git diff shows the difference between the file in MODIFIED state before entering the STAGED AREA and the contents after entering the STAGED AREA.

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  modified: README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

Let's try git diff.

```bash
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your
PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your
change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your
patch is
+longer than a dozen lines.
 If you are starting to work on a particular area, feel free to submit a
PR
 that highlights your work in progress (and note in the PR title that it's
```

This command compares the files in the WORKING DIRECTORY(MODIFIED) with the files in the STAGED AREA. If you already put the file in the STAGING AREA, use `git diff --staged` to compare the COMMIT file and the STAGING AREA file.

```bash
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

git diff doesn't show all changes since the last commit. You should know that it only shows the modified parts that are unstaged. 

#### 외부 tool

Comparing with an external tool In this book, we continue to use the git diff command here and there. If you have a Diff tool that you like or have a beautiful look at the result, you can use it. You can use the git difftool command instead of git diff to compare with tools such as emerge and vimdiff. Commercial products are also available. The command git difftool --tool-help shows the available tools.



## Git file state revert

There may be cases where a small typo is made after committing a certain file and needs to be corrected. In that case, I usually make a new commit, and you can change the state of the file by using the commands below.

### amend

Occasionally, when a commit is made by omitting some trivial content, it is necessary to commit by adding content. In that case, use git commit --amend after editing the file and uploading it to the STAGING AREA. If you use git commit --amend, newly STAGED files will be merged with the last COMMIT.

The amend option is the process of making the old commit history into the new commit history. Therefore, previously committed contents are lost, and only newly merged contents remain in the commit log.

#### 응용 

I tried to apply this when posting a blog post. I'm making a blog post on github, and if I accidentally write the wrong date or make a typo, I thought it would be good to merge the commit logs at once with git commit --amend . However, since github is a remote repository, I failed because I did not know how to edit the commit log in the remote repository and only knew how to edit the local commit log.

### reset

This time, we will learn how to convert files in STAGED AREA to UNSTAGED (WORKING DIRECTORY) files.

Suppose after editing two files, you tried to make only one STAGED state, but you accidentally put both of them in STAGED state with gid add * .



```bash
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README
  modified: CONTRIBUTING.md
  
```

Let's try to use the `git reset HEAD <file>` command.

```
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

If you use git reset HEAD <file name>, the file at the commit point pointed to by HEAD is made UNSTAGED.

However, care must be taken when using the git reset command. It's really dangerous when used with the --hard option. However, if used without options as above, the files in the working directory are not touched. (Git reset will be discussed later.)

### git checkout --

How can I get a file back in MODIFIED state? (Can I revert to the most recent COMMIT version?)

git status will kindly tell you.

```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md

```

Let's run it like this.

```bash
$ git checkout -- CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README

```

You can see that the file has returned to its pre-MODIFIED state.

git checkout is also a very dangerous command. Git can go up to the STAGED AREA and restore the commits. However, once deleted, items not uploaded to STAGED AREA cannot be recovered. (We'll get to that later.) If you're really sure it doesn't matter, you can use the git checkout command.