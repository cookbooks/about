## Beta
### Do not use in production.
These cookbook repositories are likely to change.  You should only use them in
contexts that can accommodate such change.

### Subject
We track, without preference:

 - American-style cookbook repositories, i.e. where the individual (cookbook) is a primary concern (of the repository).
 Example: a heavywater cookbook
[chef-ganglia](https://github.com/heavywater/chef-ganglia) is tracked as [hw-chef-ganglia](https://github.com/cookbooks/hw-chef-ganglia))
 - European-style cookbook repositories, i.e. where the individual (cookbook) is a secondary concern (of the repository).
  Example: RightScale [cookbooks_public/cookbooks/web_apache](https://github.com/rightscale/cookbooks_public/tree/master/cookbooks/web_apache))
is tracked as [rs-web_apache](https://github.com/cookbooks/rs-web_apache)).

### Scope:
These are the vendors, or upstream:

 - `ey-*`: [EngineYard](http://www.engineyard.com) [ey-cloud-recipes](https://github.com/engineyard/ey-cloud-recipes) European-repository.
 - `hw-*`: [HeavyWater](http://heavywater.ca/) [chef-*](https://github.com/search?q=%27chef-%27+username%3Aheavywater&repo=&langOverride=&start_value=1&type=Repositories&language=) American-repositories.
 - `ic-*`: (paused - possible repository corruption) [InfoChimps-Labs](http://www.infochimps.com/) [ironfan-pantry](https://github.com/infochimps-labs/ironfan-pantry) European-repository.
 - `oc-*`: [OpsCode](http://www.opscode.com/) [cookbooks](https://github.com/opscode/cookbooks) European-repository.
 - `rs-*`: [RightScale](http://www.rightscale.com/) [cookbooks](https://github.com/rightscale/cookbooks) and [cookbooks_public](https://github.com/rightscale/cookbooks_public) European-repositories.
 - `ss-*`: [SliverSky](http://silversky.ca/) (aka [fnichol](https://github.com/fnichol)) [chef-*](https://github.com/search?q=%27chef-%27+username%3Afnichol&repo=&langOverride=&start_value=1&type=Repositories&language=) American-repositories.

### Reference cookbooks
Repositories without the prefix `<char><char>-` are cookbooks that have been selected by
someone as the 'reference' cookbook from among the above 'upstream' or 'vendor'
cookbooks.  Generally these have been the Opscode cookbooks, but that is not
carved in stone. Ideally community wide contributions will ocur in the `qa`
branch of the reference cookbooks.  Open an issue against the cookbooks/about
repository if you wish to add or change a reference cookbook.

### Objectives
This account is a collection of cookbooks that:

 - Track various upstream Chef cookbooks from vendors and individuals.
 - Live on when upstream drops them.
 - Strive, in the `qa` branch of the reference cookbooks, to be compatible irrespective of who upstream is.

### Complementary Tools
 - [Librarian](https://github.com/applicationsonline/librarian): "Librarian is really for pulling in the 50 or so finished third-party cookbooks that you're using, not the 1 or 2 cookbooks you're actively working on".
 - [knife-github-cookbooks](https://github.com/websterclay/knife-github-cookbooks): Likely more suited to those cookbooks you *are* hacking on.

### How to contribute:
For extremely simple changes, e.g. typographical errors: Use the Github 'Edit this file' button.

For more complex, time consuming changes, the following is one (safety-first/paranoid) workflow that might help you stay sane in the long run:

This is an elaborate, and paranoid, workflow.
The description below assumes no familiarity with Git.
If you don't already have it configured, integrating a GUI merge-tool with Git will help:

    git config --global merge.tool meld

It uses the git-flow extensions and details every step using the `hedgehog` account and `users` repository.
[Git-flow](https://github.com/nvie/gitflow), is described [here](http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/),
shown [here](http://buildamodule.com/video/change-management-and-version-control-deploying-releases-features-and-fixes-with-git-how-to-use-a-scalable-git-branching-model-called-gitflow),
and illustrated [here](https://github.com/eadz/Git-Flow-Example), with more detail
[here](http://yakiloo.com/getting-started-git-flow/).
Git-flow extensions assist feature branch management, and help you quarantine any mess(es):

 1. Fork a [cookbooks](https://github.com/cookbooks) repository, `<ckbk>`, to your account (Use your Github account - free or paid)
 1. Clone your fork of `<ckbk>` to your desktop (shell)

        CKBK=users
        pushd ~/src/
        git clone git@github.com:hedgehog/${CKBK}.git
        pushd ${CKBK}
        git fetch origin
 1. Add `cookbooks/<ckbk>` as the upstream.

        git remote add upstream git://github.com/cookbooks/${CKBK}.git
        git fetch upstream
 1. Track the `master` branch upstream in the local `master` branch

        git branch --set-upstream master upstream/master
        git branch --track qa upstream/qa
        git branch --track develop upstream/qa  # This is not a typo
 1. Pull upstream `master` branch into your local `master` branch
        git checkout master
        git pull upstream
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
 1. checkout the `develop` branch

        git checkout develop
 1. Start a branch to bring in any changes from `master` (the cookbook `upstream`)

        git flow feature start rebase
 1. [Rebase](http://book.git-scm.com/4_rebasing.html) `master` and `qa` onto the just created `feature/rebase`

        git rebase --onto feature/rebase master qa
 1. resolve any conflicts and merge. If people have been rebasing `qa` onto
 `master` before making their changes, then there should be few or no merge conflicts.
  But if there are, use these commands to start your GUI mergetool, then continue:

        git mergetool
        # Once any merge conflicts have been resolved
        git rebase --continue
        # invoke git mergetool if there is another conflict, repeat etc.
 1. finish the rebase feature, depending on the sate of your develop branch you may need to use the mergetool again - hopefully not.

        git flow feature finish rebase
        git checkout qa
        git rebase develop
        git push origin
 1. start a feature branch make to your changes to `develop`

        git flow feature start demo
 1. implement whatever changes you intended.  If you want to test someone else's
 pull request, follow [this bog post](http://hedgehogshiatus.com/106151091). If changes happen upstream while
 you are developing your ideas or testing a pull request: rebase the `feature\demo` branch onto `master` and then onto `qa`
 1. publish your feature branch to your github account.

        git flow feature publish demo
 1. **make your Github pull request for the `feature/<yours>` branch to the `qa` branch of `cookbooks/<ckbk>`**
 1. wait for the bouquets and applause.
 1. Once the crowd settle and your request is pulled into upstream, you'll want to tidy-up locally:

        git flow feature finish demo
        git fetch upstream

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

