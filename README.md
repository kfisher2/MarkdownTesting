# Globalscape Git FAQ

NOTE: This document is currently **under construction**. 

This document is intended to be supplemental to [Git Workflow](Git Workflow.md). It details frequently asked questions related to git processes at Globalscape.

Globalscape Git resources
* [Git FAQ](Git FAQ.md)
* [Git Workflow](Git Workflow.md)
* [Git Beginner Guide](Git Beginner Guide.md)

Contents:
* General FAQs
* Branch Structure Overview
* About Rebase Process
* About Pull Requests
* How do I recover from mistakes or a bad command?


## General FAQs

### How does this differ from the published Gitflow docs?

Git Workflow.md is an overview of the process we should use for git version control at Globalscape. Git FAQ.md covers frequently asked questions not covered by the workflow and should 
be used as a supplemental resource in conjuction with Git Workflow.

### What does "cherry pick" mean?

One way to apply committed work from one branch onto another is to cherry-pick it. It takes the work that was introduced
 in a commit and tries to reapply it on the branch you’re currently on. Essentially, you are taking the work you already did in one branch and automatically re-doing this work in a 
 different branch. This is useful if you have a number of commits on a branch and you want to integrate only one of them to a different branch, or
  if you only have one commit on a topic branch and you’d prefer to cherry-pick it rather than run rebase.
  
  TODO - example cherry pick scenario

### What does "squash" or "squashing" mean? 

In the generic sense, Squashing is the process of merging two or more commits into one. In the specific sense, it refers to a rebase option that merges this 
commit into the parent and appending the message to the parent. For a squash example, see [Git Workflow.md](Git Workflow.md) "3. a) Cleaning up your commits before a pull request with interactive rebase".

### Which version is master?

Master is the vNext (version Next), where the changes that will be in the *next* version go. It is the clean branch; all changes to master must have been through QA first. 
It should always reflect a production-ready state.

TODO - this could be better

#### What can be put directly into master? 

Only code that has been through the QA process can be put in master. This means nothing can be put *directly* into master. (Everything must go through a peer review process before entering
 master, and feature branches should not come directly off of master.)

### What is the difference between local and remote branches?

For every git branch you have on your machine, there is a local and remote branch. The local branch refers to the version of the branch on your local machine that you make changes to. Git also creates a remote branch to keep track of these changes.
This remote branch exists on the git server, updating itself with changes made on the local branch when you `git push...`. When you do a push, you tell git to update the remote branch with any changes you have committed on your local branch. 
For example, let's say you make a feature branch JS_12345 with the command `git checkout -b JS_12345`. This will create a local branch `JS_12345` on your machine and a remote branch `origin JS_12345` on the git server. The `origin` is the default
designation of a remote branch. When you do a `git commit ...` the changes are applied only to the local branch `JS_12345`. The remote branch will not be updated with the changes; you must use `git push ...` to communicate those local commits 
to the remote branch `origin JS_12345`.

### Which branch should I branch off of? 

#### For maintenance work
* You should branch off a `quarterly maintenance` branch.
* If this is a *fix in current version*:
  * Use this quarter's `quarterly maintenance` branch off of `master` (ex. `Q1_2017`)
    * **Note**: If you are notified that QA is *at capacity* for the current quarter, you will need to use the branch corresponding to the next quarter.
* If this is a *fix in previous version*:
  * Use this quarter's `quarterly maintenance` branch off the `release` branch corresponding to the previous version (ex. `7.2_Q1_2017`)
  * **Note**: If you are notified that QA is *at capacity* for the current quarter, you will need to use the branch corresponding to the next quarter.

#### For roadmap work
* You should branch off of the roadmap branch corresponding to your project.
* If your roadmap project has multiple roadmap branches, choose whichever <roadmap>_<sub_topic> branch makes the most sense to you.
  
#### For hotfix
* You should branch off from wherever the change is needed. If you need to pull an exact build number, see "How do we pull an exact build?" below.

### Who is 'in charge' of a given branch?

This person is in charge of any rebases on the given branch as well as initiating any necessary pull requests when the work on the branch is complete.

* `Master` - **No one.** No one will need to rebase or 
* `Feature` - **Whomever is the initals for the feature branch.** (Feature branches should follow the naming convention <initials>_<workItem ID>)
  * When a given feature branch is under peer review, the peer reviewers can also be considered 'in charge' of the branch.
* `Quarterly maintenance` - Currently, **Lonnie French** is in charge of these branches. This page will be updated if that changes.
* `Release` - **No one.** Since release branches contain previous releases, they will never need to be rebased with master or pull requested into master (or any other branch).
* `Roadmap` (development) - The **team lead** for that roadmap project.
  * When the team lead is absent, another team lead may be temporarily 'in charge'.
  * When all team leads are absent, a team lead may (before leaving) designate someone (who is confident with rebase) to be temporarily 'in charge'.

If you're unsure what the difference between the types of branches are, see the "Branch Structure Overview" section.

### How do we deal with maintaining two releases?

With the understanding that releases are referred to as *major.minor.revision.build* where a *major* release would be version 7, a *minor* release would be version 7.2, a *revision* number is designated as 7.2.3, and a *build* number 
is designated as 7.2.3.19. We maintain the current and previous minor releases, so if we are currently on minor release 7.3, we maintain both 7.3 and 7.2. The current minor release is maintained on the `master` branch, and the previous 
minor release is maintained on a `release` branch. Every time a build or revision is made, it is tagged with a git tag on the appropraite branch.

Note: As an artifact, our current `release` branch for the previous minor release is incorrectly called 7.2.2. The subsequent `release` branches will have the correct name.

### How do I pull an exact build?

Every build should be tagged in git. Typing `git tag` should list all of the available tags (try `git fetch --tags` if you're missing tags). You can perform a search with `grep` by typing `git tag | grep "7.3.4"`. Replace `"7.3.4"` with any text you want to search for.
You can go to a tag, for example `EFT/EFT_7.1.0_build_1`, via `git checkout -b EFT/EFT_7.1.0_build_1`. This creates a new branch in the state of the specified tag (named the same as the tag). Note that you will not need to do any rebases since this
branch should not receive new updates. Remember to delete this branch when work is complate

Quick reference:
* `git tag | grep <build number>` to search for a tag
* `git checkout -b <tag_name>` to create a branch with the state of when the tag was created.
* Remember to delete this branch when your work is complete.

**CAUTION: This 'exact build' branch should not interact with other branches.** If you want changes you make on this branch to be included in some other branch, you **must cherry-pick** the changes.
* See "What does "cherry pick" mean?" for more info on cherry picking.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Branch Structure Overview

### What is the branch naming convention? What do I name my branch?

See each branch type below for their naming conventions.

### What is a roadmap branch?

Each roadmap branch corresponds to a roadmap project. This branch type is commonly referred to as a *development branch*.

#### Naming convention

`<roadmap_name>` --- Example: `MESAT-II`
* Where `<roadmap_name>` is a descriptor referencing the roadmap project.
* If the project requires multiple mainline branches, use `<roadmap_name>_<sub_topic>` --- Example: `AWS_AutoScaling` and `AWS_Metered`

#### Example roadmap branches

![](/Processes and Guidelines/lib/images/develop-branch.jpg)

### What is a release branch?

Release branches are branches where we maintain previous releases. For example, if the current version number is 7.3, branch "7.2" would be a release branch. Since release branches correspond to previous releases,
 they should *never* be rebased with `master`. (That is to say, release branches should not be updated to reflect the state of the master branch.)

#### Naming convention

`<version_number>` --- Example: `7.2`
* Just the version number is sufficient to be unique. 

#### Example release branch

Release branches should never have `master` rebased onto them. If a change from `master` branch is needed on the `release` branch, it should be cherry-picked. See "What does "cherry pick" mean?" for more on cherry picking.

![](/Processes and Guidelines/lib/images/maintenance-branch.jpg)

### What is a quarterly maintenance branch?

This branch is used to encompass code changes for each quarter that are not within the scope of a roadmap project (primarily maintenance work). 

#### Naming Convention

`<parent_branch>_Qx_20xx` --- Example: `7.2.2_Q1_2017`
* Where `<parent_branch>` is the name of whichever branch this quarterly maintenance branch is coming from and the `x`s are the quarter number and year.
* When `master` is the parent branch, the name can just be `Qx_20xx` --- Example: `Q3_2017` 

#### Example quarterly maintenance branches

Quarterly maintenance branches are off of `master` and `release` branches. 

![](/Processes and Guidelines/lib/images/quarterly-branch.jpg)

### What is a feature branch (topic branch)?

A feature branch, pictured in orange, is a branch created by an individual to complete development work on a TFS work item. These can branch off from any branch except `master`.

#### Naming convention

`<initials>_<workItem ID>` --- Example: `JS_38527`    
* For example, if John Smith is working on Bug #38527, he would create a feature branch called `JS_38527` for his development work.

##### Why is the naming convention for feature branches `<initials>_<workItem ID>`?
It is preferred to prefix your feature branch's name with your initials followed by the TFS Requirement or Bug number.  This way when it is saved to the server, 
everyone can see who owns a particular branch and know which requirement or bug this relates to.  You can easily find your branches and delete them when work is complete
 since they are prefixed with your initials.  This way you don't accidentally delete somebody else's branch.

#### Example feature branches

This example shows that feature branches can come off of any branch but `master`. Also note that the feature branch will rebase with its parent branch (the pink arrows). This should happen daily.
See [Git Workflow.md](Git Workflow.md) 2. a) Making sure you're up to date with rebase"

![](/Processes and Guidelines/lib/images/temporary-branch.jpg)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## About Rebase Process

### Why do we rebase?

We rebase to integrate code changes frequently, resolving conflicts as they occur (instead of at the end of a project's life). 
1. More frequent integration makes it more likely that git will be able to automatically resolve changes without developer input.
2. When a conflict occurs, it will be smaller in scope and the conflicting code changes will be more recent and fresh in the developers` minds.  

Rebasing results in fewer conflicts when the final merge (in the form of a pull request) happens.

### What does rebase do?

Rebasing is the process of moving a branch to a new base commit. From a content perspective, rebasing is just moving a branch from starting off of one commit on the parent branch to starting off of another. In other words, you are updating
 the feature branch to include changes from its parent branch. Note that internally, Git accomplishes this by creating new commits (*) and applying them to the specified base — literally rewriting your project history. 
It’s important to understand that, even though the branch looks the same, it’s composed of entirely new commits. 

The command `git rebase <base>` rebases the current branch *onto* <base>. In our case, base will be the <parent_branch>. The general process can be visualized as the following:

![](/Processes and Guidelines/lib/images/rebase-example.png)

### How do I rebase?

#### For feature branches

* See [Git Workflow.md](Git Workflow.md) "a) Making sure you're up to date with rebase". 

#### For roadmap branches

* Note that only the team lead of the roadmap project should perform rebase operations on the roadmap branch. 
  * If a team lead is absent, see "Who is 'in charge' of a given branch?" above for exceptions.
* Instructions can be found under "Team Lead Workflow" in [Git Workflow.md](Git Workflow.md).

### When should rebases happen?

You should **only rebase in branches you are *in charge* of**. (See "Who is 'in charge' of a given branch?" for help.) In most cases, a rebase should happen daily (see specific cases and picture below). This is done to ensure branches are never more than a day behind any changes relevant to the branch. That means that when we say "rebase daily", we really mean, 
"rebase daily if there are any changes". For an example of "rebase daily if there are any changes", see [Git Workflow.md](Git Workflow.md) "2. a) Making sure you're up to date with rebase" (covers feature branches).

Note the terminology: To have *master rebased onto roadmap* or to *rebase master onto roadmap* means that `roadmap` will get any new changes made on `master` since the last rebase.

In what cases do rebases happen? (The pink arrows represent rebases.) **Again: Do not rebase in any branches that you are not 'in charge' of**
* Daily (if there are changes):
  * All `roadmap` branches should have `master` rebased onto them. (aka update `roadmap` with any new changes from `master`)
  * All `quarterly maintenance` branches should have `master` rebased onto them.
  * All `feature` branches should have their parent branch rebased onto them (unless the parentage invovles a `release` branch).
* Special cases:
  * `release` branches never have `master` rebased onto them 
    * In the case where a `release` branch needs the same change as `master`, the change should be *cherry-picked* either directly from `master` or from master's `quarterly maintenance` branch. (See "What does "cherry pick" mean?" above)
  * ***1** - If a *cherry pick* occurrs here, both the `quarterly` branch and the any subsequent `feature` branches should be updated with the new code changes using rebase.
  * ***2** - If a different feature branch is merged (via pull request) into the `quarterly` branch, any other `feature` branches should be updated with the new code changes using rebase.
  
For information about the different branch types, see "Branch Structure Overview".

![](/Processes and Guidelines/lib/images/rebase-overview.jpg)

### How do I force push?

**You may only force push to branches you are 'in charge' of.** (See "Who is 'in charge' of a given branch?" for help.)

* Please create a backup branch since force pushing disables safety checks and **can cause the remote repository to lose commits**.
* Create a backup of your branch:
  * `git checkout -b <branch>_BACKUP`
* Go back to your branch and force push:
  * `git checkout <branch>`
  * `git push --force origin <branch>`
* Keep the backup branch around until you are *absolutely sure* everything went through the way you want it.
* Once you are sure, delete the backup branch: `git branch -D <branch>_BACKUP`
* If you need to 'undo' your force push, see "How do I recover with a backup branch?" below.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## About Pull Requests

### When is a pull request used?

A pull request is required when rejoining feature branches to their correpsonding parent branch (usually a roadmap branch). This is when net-new work is being pushed into the larger scheme, 
and their purpose is to serve as a common forum for a formal Peer Review. A pull request should also be used when using 'cherry-pick' to apply changes from one branch onto another. Pull requests are not 
required for rejoining a roadmap branch back to master. However, note that **only the person 'in charge' of the roadmap branch may do this**. 

between which branches; when? how? manageing code conflicts

### How do I generate a pull request?

See [Git Workflow.md](Git Workflow.md) "4. Create a pull request with the work item ID prepended (ex. "Req #1234 ...")"

### How do peer reviews work?

Developers should add one or more peer reviewers to every pull request. If you're not sure who to add as a reviewer, see "Who should I add as a peer reviewer?" below.

### What do I do as a peer reviewer?

The Peer Reviewer should go to the Pull Requests page and select "Assigned to me" to find his/her assigned Pull Requests. Notes can be made on the overall commit, on an individual file, or on an individual line.
 TFS will also evaluate whether the commit can be merged into master without conflicts. If there are conflicts, the peer reviewer should rebase the commit onto master and resolve the conflicts before beginning the review. This is a special case 
 where someone other than the person 'in charge' is allowed to perform rebase on a branch. 

  * Make sure the branch is up to date (using rebase if necessary).
    * (This is a special case where someone other than the person 'in charge' is allowed to perform rebase on a branch.)
  * Look over the code changes, making comments and asking questions as necessary.
  * Once you have addressed any issues, decide if you Approve or Reject the changes.
    * Find your name under "Reviewers"
    * Change the drop down box value from `No response` to your response (`Approved`, `Rejected`, etc.)
      * If you change your response to something other than `Approved` make a note of why so the owner of the pull request can make changes..
  * **If you are the last (or only) peer reviewer to Approve changes, you are responsible for completing the pull request.**
    * Double check the branch has been rebased with the most up to date changing (rebasing as necessary)
    * Double check that there are still "No merge conflicts" (resolving conflicts as necessary)
    * Complete the pull request by clicking "Complete pull request" (But first check to make sure there are "No merge conflicts", resolving conflicts if necessary)

### Who should I add as a peer reviewer?

See [Git Workflow.md](Git Workflow.md) "c) Adding peer reviewer(s)."

### How do I manage code conflicts?

Go into the file(s) with conflicting changes and manually alter the conflicting code to resolve conflicts. Once you commit and push these changes, they will show up on the pull request in TFS. 
You may have to click on "Target branch updated | re-evaluate" in the top right to check if there are still merge conflicts.

**TODO** no idea how correct this is. help

### Why does a pull request have the 'wrong' author? How do I see the 'real' author of changes? (Does the pull request replace the author?)

The author of a pull request will be the peer reviewer who hits "Complete pull request". The author of the code changes can be found found on the original commit (see example below).

Note that both the green and red commits are about Bug #341573. The difference is that the PR 877 commit shows the author as the peer reviewer who completed the PR, and the original commit shows the author as the person who made the code changes.

![](/Processes and Guidelines/lib/images/pr-author.png)


--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## How do I recover from mistakes or a bad command?

### How do I reset git to a pervious state?

If you did not create a backup branch, you can reset git to a previous state with git reset.

* You should be able to see a log of what git has done recently by running:
  * `git reflog`
* It should look something like: 
```
1ab9e9d HEAD@{0}: commit: Add image for markdown
...
78f6b12 HEAD@{9}: commit: Add images for markdown
b805900 HEAD@{10}: pull: Fast-forward
bf26585 HEAD@{11}: checkout: moving from test_branch to master
6aa9233 HEAD@{12}: rebase -i (finish): returning to refs/heads/test_branch
6aa9233 HEAD@{13}: rebase -i (fixup): Updated Git Workflow.md
bf26585 HEAD@{14}: rebase -i (start): checkout HEAD~2
1aceae0 HEAD@{15}: rebase -i (finish): returning to refs/heads/test_branch
1aceae0 HEAD@{16}: rebase -i (squash): test commit
2edd6c4 HEAD@{17}: rebase -i (start): checkout HEAD~2
2f93420 HEAD@{18}: commit: commit 2
```
* You can use git reset to move to a previous git state, in general:
  * `git reset --hard HEAD@{#}`
* For example, if we wanted to 'undo' the rebase shown, we would:
  * `git reset --hard HEAD@{18}`
* This puts you where you were before the rebase and after you commited "commit 2".
  * You can see that you have made "commit 2" in this state by looking at the top of the `git log` output
* If you've messed up you can go to the git ref before your "reset" with another reset.
  * If you haven't done anything else, this would be:
    * `git reset --hard HEAD@{1}`
  * Otherwise look at the `git reflog` to determine where one step before your reset is.

### How do I recover with a backup branch?

This is fairly simple: delete the original branch that is in a bad state and use the "_BACKUP" branch instead (renaming it to the original).

* This is **only** if you have made a _BACKUP branch.
* Delete the bad branch:
  * `git branch -D <feature_branch>` to delete locally
  * `git push origin --delete <feature_branch>` to delete remotely
* Rename the _BACKUP branch:
  * git branch -m <branch>_BACKUP <branch>
  * git push -u origin <branch>

### What can be undone? Force push?

Force pushes cannot be undone. Force pushing to master should **never** happen. 

Things that can be undone involve everything on local—you should be doing a rebase locally and making sure that it’s 100% good to go before pushing anywhere. If you screw up locally, no big deal, 
you can always force-pull from remote to get your branch back in a good state. The answer here is: if you aren’t sure, find someone who knows to look over your shoulder and be your sanity check. Communication!!!! 
Better to measure twice and cut once than cut once and lose your finger. 

### How do I edit or change my commit message?

#### For your most recent commit

See "Changing your last commit" in [Git Beginner Guide.md](Git Beginner Guide.md).

#### For other commits

See "3. a) Cleaning up your commits before a pull request with interactive rebase" in [Git Workflow.md](Git Workflow.md).

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Unanswered Questions

### what a merge (push?) to master (or any branch) entails - and when it happens?
### where do we build from to send to QA?
 – whatever branch QA wants. If its develop then its develop, maintenance from maintenance, etc. 
### where do we build from to send GA? 
these will be from master (?). after everything has been cleared by QA.
### where do we build from to send hotfix?
?????
### how is CPR process affected?
Not sure how CPR is working for EFT at the moment, CPR process seems a bit fast and loose across the dept.
 





