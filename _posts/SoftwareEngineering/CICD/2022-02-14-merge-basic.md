---
title : "git에서의 merge pattern "

excerpt: "branch and merge basic "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---
When you develop a new feature in git, you create a new branch. Usually, most developers are well aware of this fact. However, they typically do not know that there is a pattern in the relationship of any two branches. According to this pattern, the method of merging will also be different.

# Fast-forward pattern

![image](https://user-images.githubusercontent.com/50165842/153867718-67a869a3-4a99-4a6e-9dad-383990a437b1.png)

During development, issues may arise during the development process. In this case, we will create a new issue branch and work on it.

```
git checkout -b iss53
```

![image](https://user-images.githubusercontent.com/50165842/153867835-8b1e2f79-9daa-415f-bb4f-229deed2c400.png)

While providing the service, a bug was suddenly discovered and a hotfix was needed. However, since the issue has not been resolved yet, the branch doing hotfixes and the issue branch should not be mixed. In this case, you can move from the issue branch to the master branch.

```
$ git checkout master
Switched to branch 'master'

$ git checkout -b hotfix
Switched to a new branch 'hotfix'
$ vim index.html
$ git commit -a -m 'fixed the broken email address'
[hotfix 1fb7853] fixed the broken email address
 1 file changed, 2 insertions(+)


```

![image](https://user-images.githubusercontent.com/50165842/153867936-c958f820-c20d-47d1-911a-ad79f34a4d79.png)

Here, when the hotfix is completed, it must be combined to finally deploy the hotfix to the production environment. In this case, merge is used.

```
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2++
 1 file changed, 2 insertions(+)

```



A message indicating that the merge has been completed in the fast-forward method is displayed. When merging from branch A to branch B, if branch B points to a commit after branch A, the A branch is moved to the commit pointed to by B. These patterns are called fast-forward patterns.

![image](https://user-images.githubusercontent.com/50165842/153868290-65d6e81c-c982-4483-91ff-a1b204e5317f.png)



The hotfix branch is no longer needed, so we will delete it.

```
$ git branch -d hotfix
Deleted branch hotfix (3a0874c).

```



# 3-way-merge pattern

![image](https://user-images.githubusercontent.com/50165842/153869254-67ddcd96-eb51-4f8f-a3af-d0bbdcfb67ae.png)

Now we will go back to the iss53 branch and handle the issue.

```
$ git checkout iss53
Switched to branch "iss53"
$ vim index.html
$ git commit -a -m 'finished the new footer [issue 53]'
[iss53 ad82d7a] finished the new footer [issue 53]
1 file changed, 1 insertion(+)
```



It is important to note that the current hotfix handling does not affect iss53 . As soon as the issue is dealt with, the master branch is merged into the iss53 branch.

```
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html | 1 +
1 file changed, 1 insertion(+)
```

![image](https://user-images.githubusercontent.com/50165842/153869598-5ad24d3a-3fa5-454b-aa55-26e98249e445.png)

In this case, the iss53 branch does not point to a commit after the master branch. Here, the merge pattern is slightly different. First, it finds the common ancestor of both branches. Create a new commit with common anscestor , master , iss53 and merge them into the iss53 branch. Since it uses 3 commits, it is called 3-way merge.

## merge conflict



![image](https://user-images.githubusercontent.com/50165842/153964680-0996a4ae-a6d0-4866-9e99-b0342720585f.png)



```
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```



Since a merge is created using three commits, the same part can be modified. At this time, if you modify the same part at the same time, git cannot automatically merge.



You can use git status to see which part has not been merged.

```
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
Unmerged paths:
  (use "git add <file>..." to mark resolution)
  both modified: index.html
no changes added to commit (use "git add" and/or "git commit -a")

```

```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
========
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

The conflicting part is displayed like this. ==== The upper part is the part pointed to by the HEAD pointer, and the lower part is the file part pointed to by iss53. After making corrections and deleting <<<<.======,>>>> , save it to git again with the git add command to solve the problem.

```
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)
Changes to be committed:
  modified: index.html

```

It says that the conflict has been resolved. Now, if you use git commit to include the merge in the commit history, a commit message is created as shown below.

```
Merge branch 'iss53'
Conflicts:
  index.html
#
# It looks like you may be committing a merge.
# If this is not correct, please remove the file
# .git/MERGE_HEAD
# and try again.
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# All conflicts fixed but you are still merging.
#
# Changes to be committed:
# modified: index.html
```

Recording how you resolved the urge, why you did it, and what you need to check will make it easier to understand when you see this message again later.
