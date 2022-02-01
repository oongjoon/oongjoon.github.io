---
title : "To manage software releases, git uses tags.. "

excerpt: "tag and release "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

Like other VCSs, git supports tags. Tags are used in software release, that is, in the deployment phase. For example, `v.12.4. It can be used like a 'patch note'. Let's take a look at the operations supported by git for this tag.

## create tag

There are two types of tags in git. One is an Annotated tag and the other is a lightweight tag. Let's see how to create these two.

### lightweight tag

```
$git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5

```

To create a lightweight tag in git, you can use the `git tag <tag name>` command. A lightweight tag is just a pointer to a specific commit. Therefore, it is used when there is no need for other additional information or temporary tags  is just enough.

```
$ git show v1.4-lw
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700
  changed the version number

```

It can be seen that this tag simply shows information about commit.

### annotated tag

```
$git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

You can create an annotated tag by using the `-a` option. The `-m` option allows you to specify a message.

Annotated tag stores information such as tag author, email, tag creation date, and message in the git database. You can also sign with the GNU privacy guard (GPG). In general, when creating a tag, it is a good idea to make it an annotated tag  so that  all of this information can be used.

```
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date: Sat May 3 20:19:12 2014 -0700
my version 1.4
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700
  changed the version number
```

You can see that it shows different information unlike Lightweight.

### tag on past commit

In git, if you know the checksum of a commit, you can create a tag for the previous commit. You can create it with the command `git tag -a <tag name> <commit checksum>`.

```
$ git tag -a v1.2 9fceb02
```



## tag lookup

```
$ git tag
v0.1
v1.3

```

To search tags, use the git tag command. This command lists all tags in alphabetical order. If a project has been released a lot, it may not be possible to display all of them on the command line.

```
$ git tag -l "v.1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
v1.8.5.2
v1.8.5.3
v1.8.5.4
v1.8.5.5
```

If you use a search pattern, that is, a regular expression, with the `-l` option, you can find tags corresponding to the pattern.

## share

```
$ git push origin v1.5
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag] v1.5 -> v1.5

```

```
$ git push origin --tags
Counting objects: 1, done.
Writing objects: 100% (1/1), 160 bytes | 0 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag] v1.4 -> v1.4
 * [new tag] v1.4-lw -> v1.4-lw
```



Even if you create a tag in git, it is not automatically reflected in the remote server. You need to push tag to the server with the `git push <remote repository> <tag name>` command. This command can push a single tag. To push multiple tags, use the `it push <remote storage> --tags` command. This command will reflect all tags that are not in the remote server to the server.

## check out

```
$ git checkout 2.0.0
Note: checking out '2.0.0'.
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.
If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:
  git checkout -b <new-branch>
HEAD is now at 99ada87... Merge pull request #89 from schacon/appendix final
$ git checkout 2.0-beta-0.1
Previous HEAD position was 99ada87... Merge pull request #89 from
schacon/appendix-final
HEAD is now at df3f601... add atlas.json and cover imag
```

```
$ git checkout -b version2 v2.0.0
Switched to a new branch 'version2'

```



You can checkout specific tags. If the branch is not checking out the tag pointed to, the currently working commit is in `detached HEAD` state. If you work in the `detached HEAD` state, the tag remains the same and a new commit pointed to prior commit. However, unless you know the hash value of this commit, you cannot access it. If , if this commit is a meaningful commit, it's a good idea to create a branch.
