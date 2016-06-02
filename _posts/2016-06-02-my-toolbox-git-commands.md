---
layout: post
tags: tools
title: My toolbox - git commands
---
Git is a very easy way to store and synchronize repositories. I use mostly [Bitbucket](https://bitbucket.org/) as this one has a functionality to keep your repository private. I've learned some really useful things you can do with it and I wanted to put them here for me and for others.
<!--more-->

### The workflow
Firstly [there is a workflow](https://www.atlassian.com/git/tutorials/comparing-workflows). It describes the process of how to use Git: when to create branches, what is stored where, etc. I usually prefer [Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow). I advise any beginner to read through this description and choose a workflow which suits your needs.

The one I use has the following advantages:

1. `master` branch is there only for releases. It is easier that way to see the code that is currently on production.
2. There is a `develop` branch which keeps all the changes that are tested locally and on integration/preproduction platform (if ones exist). So there I can find a candidate for a next release.
3. Each functionality and bug fix has its own branch. I don't create a separate branch for each and every Jira task, but thr rule of thumb is that each story has its own branch. If bug fix applies to a story which still has its own branch - I put it there. Otherwise it should have its own branch. If you work in a small team, you can put it directly on `develop`, but it should be more an exception than a general rule.

### Branches

The set of git commands to manage branches is well described in a chapter [Git Branching - Basic Branching and Merging on git-scm.com](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging). Here is a quick summary:

To **create a branch** and switch to it at the same time: 

{% highlight bash %}
git checkout -b newBranchName
{% endhighlight %}

(*Important:* you should be on a branch which will be a reference to the one you are creating - usually it is going to be `develop`).

To **commit** all the files: 

{% highlight bash %}
git commit -a -m 'description of the changes'
{% endhighlight %}

(*Note:* personally, I don't use -a switch as I prefer to firstly choose the files to be sure there is none which gets in there by accident; `git add files`)

To **go back** to the referencial branch: 

{% highlight bash %}
git checkout develop
{% endhighlight %}

To **merge** the branch created in point 1: 

{% highlight bash %}
git merge newBranchName 
{% endhighlight %}

(*Note:* make sure the changes you've made are solid, test them first)

To **delete** the branch: 

{% highlight bash %}
git branch -d newBranchName
{% endhighlight %}

### Other useful tips

When you are on Windows and you use SourceTree which ships with git.exe file, you may wish to use it instead of installing a standalone copy. [How to use SourceTree embedded Git/Mercurial on command line](http://www.geekality.net/2015/01/13/how-to-use-sourcetree-embedded-gitmercurial-on-command-line/) describes what you should do to configure Windows to see this file.

[Tobias Kraze](http://makandracards.com/makandra/621-git-delete-a-branch-local-or-remote) gives a tip on how to delete a remote branch: 

{% highlight bash %}
git push origin :the_remote_branch
{% endhighlight %}

If you work with a frontend code which you minify/uglify before deployment and you keep it in `dist` directory (following the template used by Yeoman), you can use [their tips for keeping it as a seperate branch](http://yeoman.io/learning/deployment.html) (me, I call it `release`): 

{% highlight bash %}
git subtree push --prefix dist origin release
{% endhighlight %}

*-Hey, can you check something on branch fix211?* I can hear once in a while, of course usually when I'm in the middle of something, but at the same time when it is not yet ready to be committed. Stashing your changes come in useful then:

{% highlight bash %}
git stash
git checkout fix211
# Checking what is happening
git checkout myBranch
git stash pop
{% endhighlight %}