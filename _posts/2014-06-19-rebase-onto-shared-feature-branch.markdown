---
layout: post
title:  "Using rebase --onto with shared feature branches"
date:   2014-06-19 11:24:39
tags: git continuous_integration
---
Collaborating in git is all fun and games until someone needs to rebase. Normally this isn’t an issue because if you want to pull down the mainline changes, you simply rebase master and force push your branch.
![My helpful screenshot]({{ site.url }}/images/git_rebase_onto/basic_master_feature.png)

$ git rebase master

Notice how the commits in purple are transformed into the commits in  blue. This is because rebasing rewrites history (even though they are the same patches) and gives you different commits as a result. 

If you’re working alone, this is totally harmless. But, if someone else depends on that branch, they’re now in a heap of trouble because their branch still has the purple commits and doesn’t know anything about these blue commit. This is why we came up with the rule Never rewrite the history of a shared branch.

Let’s look at a workflow that involves shared feature branches.

Our usual workflow involves a feature or topic branch, from which we branch individual story branches. That work then gets merged back up into the feature branch when it’s finished (resulting in the purple commits), and then all of the other story branches rebase continually to pick up those changes to the feature branch.

So what happens when we want to integrate new changes to the main line?

If we rebase the feature branch, we get those blue commits again.

But our story branches still think those commits are purple. Rebasing them onto the feature branch results in (potentially) huge headaches because git doesn’t know that the purple and blue commits are actually the same thing and you wind up with all sorts of merge conflicts.

What we really want is to throw away the purple commits and rewrite the story branches such that the green and orange commits are now children of the blue commits.

Solution!

When we rebase the feature branch, leave the old branch and checkout a new version of the feature and rebase there.




git rebase --onto feature-v2 feature story1

Since we know all of the commits in the 

 

