---
title : "What is VCS"



excerpt: "VCS and git"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , VCS]
# classes : wide
toc: true
toc_sticky: true
---

While doing some projects, it was very disappointing that more time was spent on setting up the development environment and managing GIt rather than developing the project. At first, I felt I had to study Git because I didn't know Git well. I listened to several git tutorials on YouTube, and the more I listened to it, the more it seemed to get nothing. Since the contents of YouTube are diluted by instructor, even if you listen to the contents of YouTube, you are simply told to memorize them without understanding. I felt it was the limit of free video lectures. In the meantime, I did a google search for "best git book", and a book called "Git Pro" was the most recommended, and twas a free and there was Korean translation. So, I decided to study Git through this book. And, after reading a few pages, I felt that this book does not simply explain the commands of git, but rather explains git from the point of view of a version control system. So, through this book, I try to understand git from the perspective of a version control system.

## VCS  in my perspective

It would be nice to know everything about the Version Control System, but I also felt that the content itself was vast. Therefore, I have decided to study the Version Control System  while defining VCS in my perspective and solving questions from there

![vcs drawio (1)](https://user-images.githubusercontent.com/50165842/148645299-21277e70-e099-4236-9019-2fa9d19fbb0c.png)



This is the VCS in my perspective. There is a main branch, and the work of several people in their local branch is reflected as a commit. (Actually, it will be reflected after going through processes such as code review, test, and build.) It imports the commit log and also exports the commit.



![single_repo drawio (1)](https://user-images.githubusercontent.com/50165842/148645565-d58ea675-5793-46e4-9d3c-c1cd9a8e00c7.png)

And, what I think is the commit history of the individual repo main branch. Here, as the owner of the WoongjoonChoi repo, I have to look at this commit history and move to previous versions whenever an issue and problem arises. These versions should have commits from the main repo as well as commits from other team members.

And, looking at this picture and several YouTube lectures, I wrote down the unresolved questions in me.

1. Why is the Pointer pointing to the previous commit log?
2. What is the purpose of HEAD Pointer?
3. What does each branch pointer mean?
4. Shouldn't / be used to express local branch? Is only remote expressing origin/main like this?
5. What is the meaning of tracking in Untracking File and branch tracking?
6. Does .git store only local?
7. What exactly is the difference between the one in the working directory and the one in the staging directory?
8. When a commit is made in local, it is reflected in the commit history of the local (working directory). In my github, the commit is reflected only when I push, What is the difference between local and github repo?
9. What is the difference between git push vs git push -u option?

Roughly, it seems that there were about 10 revisions like this. I am going to study git with an emphasis on solving these questions.

## What is VCS?

VCS stands for Version Control System. VCS records changes in files over time and makes it possible to refer to previous versions. I think VCS is essential in any software domain. Through this, we can revert to the previous version as a whole on a project-by-project basis, when a file is changed, we can compare the differences with the file of the previous version, and when a problem occurs, we can find out who caused the problem, who caused the problem, and who You can see if you created an issue. It is really great to be able to enjoy these conveniences at a low cost because the Version Control System uses a computer instead of a person to record and manage these things one by one.

## History of VCS

If you read the above, you can see that VCS existed before git. Let's see how we implemented and used VCS in the past.



### local version

It can be seen as the simplest form of version management. Copy a directory and save it locally. Any thoughtful programmer will put some delimiter (timestamp) . However, when copying and pasting one by one, directories may be accidentally missing or deleted.

![image](https://user-images.githubusercontent.com/50165842/148646196-a379089b-b618-433f-af79-5debe2c756f2.png)

So, people created something called a local VCS. It records how the file changes by creating a DB in local. And, according to the book, there is something called RCS (revision control system) among the most used VCS tools. It is said that many companies are still using it to this day. RCS basically manages the patch set (the part where the file is changed), and this patch set is saved in a special file format. And, you can revert all files to a specific point in time by applying a series of patch sets.

### centralized version

However, if several developers collaborate, it may be convenient to manage files on a central server. To address these needs, CVCS (Centralized Version Control System) was created. Representative systems of CVCS include CVS, Subversion, and Perforce. These CVCS systems designate a separate server to manage files. Then, the client checks out the file from the central server and uses it.

CVCS has several advantages over the local version when it comes to collaboration. Managing files on a central server makes it easy for administrators to see what other developers are doing. You can easily identify who made the error and who created the issue. In the case of the local version, you need to manage the database of multiple servers, but in the centralized version, you only need to manage one server.

If it had been perfect, git wouldn't have come out. Also, there are downsides. If the main server goes down, developers cannot work in the meantime. I have to do it while receiving the work of other developers on the main server in real time, but I cannot receive changes to the file from the main server. To use an analogy, if Cloud Service Storage is set to a single region rather than a multi region, there is no server to communicate with in case of a disaster in that region, so it is the same as CLoud Server going down.

And, if there is a problem with the hard disk in the main server, there is no way to back it up. Snapshots on the local server are fine, but if the hard disk of the main server crashes, there is no way to recover them. Even from a local point of view, there is no way to recover this problem unless a backup copy is prepared in advance if the local hard disk fails.

### distributed version



![image](https://user-images.githubusercontent.com/50165842/148646951-ae0ccd4f-c8a0-4c71-b252-0ea4a38ca71a.png)

However, if several developers collaborate, it may be convenient to manage files on a central server. To address these needs, CVCS (Centralized Version Control System) was created. Representative systems of CVCS include CVS, Subversion, and Perforce. These CVCS systems designate a separate server to manage files. Then, the client checks out the file from the central server and uses it.

CVCS has several advantages over the local version when it comes to collaboration. Managing files on a central server makes it easy for administrators to see what other developers are doing. You can easily identify who made the error and who created the issue. In the case of the local version, you need to manage the database of multiple servers, but in the centralized version, you only need to manage one server.

If it had been perfect, git wouldn't have come out. Also, there are downsides. If the main server goes down, developers cannot work in the meantime. I have to do it while receiving the work of other developers on the main server in real time, but I cannot receive changes to the file from the main server. To use an analogy, if Cloud Service Storage is set to a single region rather than a multi region, there is no server to communicate with in case of a disaster in that region, so it is the same as CLoud Server going down.

And, if there is a problem with the hard disk in the main server, there is no way to back it up. Snapshots on the local server are fine, but if the hard disk of the main server crashes, there is no way to recover them. Even from a local point of view, there is no way to recover this problem unless a backup copy is prepared in advance if the local hard disk fails.

