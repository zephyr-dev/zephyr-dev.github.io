---
layout: post
title:  "Using rebase --onto with shared feature branches"
date:   2014-06-19 11:24:39
tags: git continuous_integration
---
Collaborating in git is all fun and games until someone needs to rebase. Normally this isn’t an issue because if you want to pull down the mainline changes, you simply rebase master and force push your branch.

![My helpful screenshot](/images/git_rebase_onto/basic_master_feature.png)

```bash
$ git rebase master
```
![My helpful screenshot](/images/git_rebase_onto/basic_master_feature_after_rebase.png)

Notice how the commits in <span style="color: rgb(153, 0, 255)">**purple**</span> are transformed into the commits in <span style="color:cyan">**blue**</span>. This is because rebasing rewrites history (even though they are the same patches) and gives you different commits as a result.

If you’re working alone, this is totally harmless. But, if someone else depends on that branch, they’re now in a heap of trouble because their branch still has the <span style="color: rgb(153, 0, 255)">**purple**</span> commits and doesn’t know anything about these <span style="color:cyan">**blue**</span> commit. This is how we came up with the phrase *If you rewrite the history of a shared branch, you're gonna have a bad time.*

##Let’s look at a workflow that involves shared feature branches.

![My helpful screenshot](/images/git_rebase_onto/shared_feature_setup.png)

Our usual workflow involves a feature or topic branch, from which we branch individual story branches. That work then gets merged back upstream onto the feature branch when it’s finished (resulting in the <span style="color: rgb(153, 0, 255)">**purple**</span> commits), and then all of the other story branches rebase continually on the feature branch to pick up those changes.

###So what happens when we want to integrate new changes that were made on the main line?
![My helpful screenshot](/images/git_rebase_onto/shared_feature_setup.png)

If we rebase the feature branch, the <span style="color: rgb(153, 0, 255)">**purple**</span> commits turn into those <span style="color:cyan">**blue**</span> commits again.

![My helpful screenshot](/images/git_rebase_onto/shared_feature_setup.png)

But our story branches still think those commits are <span style="color: rgb(153, 0, 255)">**purple**</span>. Rebasing them onto the feature branch results in (potentially) huge headaches because git doesn’t know that the <span style="color: rgb(153, 0, 255)">**purple**</span> and <span style="color:cyan">**blue**</span> commits are actually the same thing and you wind up with all sorts of merge conflicts.

What we really want is to throw away the <span style="color: rgb(153, 0, 255)">**purple**</span> commits and rewrite the story branches such that the <span style="color:rgb(0, 255, 0);">**green**</span> and <span style="color:orange">**orange**</span> commits are now children of the blue commits.

Solution!

When we rebase the feature branch, leave the old branch and checkout a new version of the feature and rebase there.

![My helpful screenshot](/images/git_rebase_onto/shared_feature_setup.png)

```bash
git rebase --onto feature-v2 feature story1
```

Since we know all of the commits in the 

 

