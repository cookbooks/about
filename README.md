## Beta
### Do not use in production.
These cookbook repositories are likely to change.  You should only use them in
contexts that can accommodate such change.

# Scope
We track:
 - Atomic-style cookbook repositories (e.g a heavywater cookbook
[chef-ganglia](https://github.com/heavywater/chef-ganglia) as [chef-ganglia](https://github.com/cookbooks/chef-ganglia))
 - Collection-style cookbook repositories of (e.g Opcode [cookbooks/haproxy](https://github.com/opscode/cookbooks/tree/master/haproxy))
as [haproxy](https://github.com/cookbooks/haproxy)).

# Objectives
This account is a collection of cookbooks that:
 - Tracks various upstream Chef cookbooks from vendors and individuals.
 - Live on when upstream drops them.
 - Strives, in the `qa` branch, to be compatible irrespective of who upstream is.

# Complementary Tools
 - [Librarian](https://github.com/applicationsonline/librarian): "Librarian is really for pulling in the 50 or so finished third-party cookbooks that you're using, not the 1 or 2 cookbooks you're actively working on".
 - [knife-github-cookbooks](https://github.com/websterclay/knife-github-cookbooks): Likely more suited to those cookbooks you *are* hacking on.

# How to contribute:
This is an ideal workflow, and assumes no familiarity with git.  Of course
one-day someone (hint) will wrap this all in a single command.
If you don't already have it setup, integrating a GUI merge-tool with Git will help:

   git config --global merge.tool meld

It uses the git-flow extensions and details every step using the `hedgehog` account and `users` repository.
[Git-flow](https://github.com/nvie/gitflow), is described [here](http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/),
shown [here](http://buildamodule.com/video/change-management-and-version-control-deploying-releases-features-and-fixes-with-git-how-to-use-a-scalable-git-branching-model-called-gitflow),
and illustrated [here](https://github.com/eadz/Git-Flow-Example), with more detail
[here](http://yakiloo.com/getting-started-git-flow/).
Git-flow extensions assist feature branch management, and help you quarantine any mess(es):
 1. Fork a cookbook, say <ckbk>, to your account (Use your Github account - free or paid)
 1. Clone your fork of <ckbk> to your desktop (shell)
     pushd
     ~/src/
     git clone git@github.com:hedgehog/users.git
     pushd users
     git fetch origin
 1. Add cookbooks/<ckbk> as the upstream.
     git remote add upstream git://github.com/cookbooks/users.git
     git fetch upstream
 1. Track the master branch upstream in the local master branch
     git branch --set-upstream master upstream/master
     git branch --track qa upstream/qa
     git branch --track develop upstream/qa
 1. Initialize git-flow.  I only make one change to the defaults: using `qa` for
    'production releases'
      git flow init
      Branch name for production releases: [master] qa
      Branch name for "next release" development: [develop]
      How to name your supporting branch prefixes?
      Feature branches? [feature/]
      Release branches? [release/]
      Hotfix branches? [hotfix/]
      Support branches? [support/]
      Version tag prefix? []
 1. checkout the `qa` branch
      git checkout qa
 1. Start a branch to bring in any changes from master (the cookbook upstream)
      git flow feature start rebase
 1. [Rebase](http://book.git-scm.com/4_rebasing.html) `qa` onto master
      git rebase master
 1. resolve and conflicts and merge.  There shouldn't be any merge conflicts if
    people have been rebasing `qa` onto `master` before making their changes.
    But if there are, use this command to start your GUI mergetool:
      git mergetool
      # Once any merge conflicts have been resolved
      git rebase --continue
      # invoke git mergetool if there is another conflict, repeat etc.
 1. finish the rebase feature, depending on the sate of your develop branch you may need to use the mergetool again - hopefully not.
      git flow feature finish rebase
 1. start a feature branch make to your changes to `develop`
      git flow feature start demo
 1. implement whatever changes you intended, rebase onto master and then onto qa if changes happen upstream while you are developing your ideas
 1. publish your feature branch to your github account.
    git flow feature publish demo
 1. **make your Github pull request for the `feature/<yours>` branch to the `qa` branch of `cookbooks/<ckbk>`**
 1. wait for the bouquets and applause.

If you look at `hedgehog/users` and `cookbooks/users`, you will see the results
of the above, where I pulled in some changes from master that had been missed in some
of the `qa` branch contributors - not their fault there was nothing like this page
in existence at the time.

I hope that helps.

# Roadmap
 - Creation of production-deployment versions, i.e just the latest of `qa` without
 the overhead of git history.
 - Failsafe/fallback distribution of the above production deployment versions
 - Better integration of the above with Librarian.

