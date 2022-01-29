---
title : "What is a commit and a branch? in a little detail "

excerpt: "Commit and Branch "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

If you use git, branches are something everyone has heard of at least once. Create your own branch and collaborate. When we collaborate, each other's work is erased and sometimes lost. This is because you can't formalize how to do Version Control with a branch. More specifically, because you don't know what a commit is. So was I. In the next few articles, we will learn about branches and commits.

# commit

In the past, I mentioned that git treats data as a stream of snapshots rather than changes. This information is in .git, the database of git, and the key values that can access this information are commit.

## What happens when you commit?

When you commit, you need to know what happens. Before commit, you will need to change the files in the working directory. When you make changes and add them to the staging area, the changes are saved in .git in the form of blob files. This is a snapshot. Then, checksums for this are stored. When you commit, a tree structure consisting of the names of the root directory and subdirectories is created. And, this tree object is a pointer to blob files. Then, it creates a commit object including the author, a pointer to the previous commit, and a message.

A pointer to the previous commit may be one or several. And, there is no pointer to the first commit. (This part will be discussed in detail later in the branch description.)

When you do git add and commit, the following items are created.

- blob
- commit object
  - author
  - pointer to previous commit
  - tree object root pointer
- Tree objects in the root directory and subdirectories

## example

Let me explain with a more concrete example.

> I have a directory with 3 files. Save this in the staging area and commit.

```
$ git add README test.rb LICENSE
$ git commit -m 'The initial commit of my project'
```



First, if you save to the staging area, a file called blob is stored in the git repository. Then, the checksum is stored in the staging area, and this checksum is the SHA key. Then, it creates a commit object and stores metadata and pointers to the root directory in the commit object.

![image](https://user-images.githubusercontent.com/50165842/151635920-a21cfc56-399f-41f1-a7ff-d37df4ee3ada.png)

In the above example, 5 new files are created, tree object, commit object, and 3 blob files are newly created.

![image](https://user-images.githubusercontent.com/50165842/151636244-06a4ab30-c663-484d-b36e-b05ac119ce62.png)

If you continue to commit, the commit that points to the previous commit is saved, and the above image is displayed.

# branch

All version control systems support branches. In the course of development, there are cases where you need to use multiple versions of the code, but the branch supports the function of copying the entire code and developing it regardless of the original code. Moving between branches is fast for storing shots. A branch is a pointer that allows you to move between commits. That is, it is a pointer to a commit.

![image](https://user-images.githubusercontent.com/50165842/151636342-9a656aad-94f6-4022-af94-0c0691fe0f87.png)

Here, v1.0 and master are the branches.

## create branch

![image](https://user-images.githubusercontent.com/50165842/151636560-f1395a0a-ce5b-4cd7-8303-ada29f51c6b5.png)

```
git branch testing
```

To create a branch in git, use the git branch command to create it.

![image](https://user-images.githubusercontent.com/50165842/151636655-c9856336-bc20-4dca-93ea-ea4e23d599da.png)

When working with git, you will see the word HEAD. A pointer to HEAD means the local branch you are currently working on.



```
$ git log --oneline --decorate
f30ab (HEAD -> master, testing) add feature #32 - ability to add new
formats to the central interface
34ac2 Fixed bug #1328 - stack overflow under certain conditions
98ca9 The initial commit of my project
```

You can also check which commit the branch points to by using the --decorate option in the git log. This means that the branch master, testing points to the f30ab commit, and the HEAD pointer points to the master branch.

## move branch

Branches are pointers, so you can move between them. Moving a branch means making the branch you're working on, ie the HEAD branch, pointing to another branch.

```
git checkout testing
```

![image](https://user-images.githubusercontent.com/50165842/151637548-afabf189-bed4-4996-8410-7cea25f6e3b3.png)

Now let's commit.

![image](https://user-images.githubusercontent.com/50165842/151637623-54af9e86-eb57-4487-b530-afc31b8e1cc3.png)

```
$ vim test.rb
$ git commit -a -m 'made a change'
```

If you make a new commit, you can see that the testing branch pointed to by HEAD is ahead by one. You can see that the master branch is still there.

![image](https://user-images.githubusercontent.com/50165842/151637715-58f5ac95-91bc-44c8-a60d-7074c105e69a.png)

```
git checkout master
```

You can see that HEAD points to the master pointer. git checkout does two things.

- Make HEAD point to another branch.
- The files in the original directory are also returned to that point in time.

When commit is made, master and testing are separate branches and proceed independently, so a workflow of temporarily moving to the master branch while working in testing is possible.

## branching branch

![image](https://user-images.githubusercontent.com/50165842/151637986-33e847c5-28a3-470f-a84e-d90bfa75d988.png)

```
vim test.rb
git commit -a -m "mad other chagnes"
```

If you edit the file and commit it again, the project history is separated and progressed. As mentioned earlier, the work contents of c2b9e and 87ab2 exist independently in the commit pointed to by each branch.

```
 git log --oneline --decorate --graph --all
 
 * c2b9e (HEAD, master) made other changes
| * 87ab2 (testing) made a change
|/
* f30ab add feature #32 - ability to add new formats to the
* 34ac2 fixed bug #1328 - stack overflow under certain conditions
* 98ca9 initial commit of my project

```

With the above command, you can see how a git commit is branched and which branch points to which commit. Even if you create multiple git branches, switching between branches is faster than other VCSs because you change the pointer to which snapshot to point to, not to copy the project.