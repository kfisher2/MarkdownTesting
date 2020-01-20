# Globalscape Git Workflow

** This page is currently under construction. ** A handy dandy guide for your friendly neighborhood Globalscape Git developer.

Globalscape Git Resources:
* [Git Workflow](Git Workflow.md)
* [Git FAQ](Git FAQ.md)
* [Git Beginner Guide](Git Beginner Guide.md)    

**If you are new to git, please see [Git Beginner Guide.md](Git Beginner Guide.md) for first time set-up and the basics.**

Contents:
* General Workflow
* Hotfix Workflow
* Team Lead Workflow


## General Workflow

This is the default workflow for completing TFS requirements and bugs. The other workflows are for special cases, such as hotfix (see below).

### 1. Find a work item (requirement or bug) to work on in TFS

#### a) Finding an open work item

* In [TFS](https://gstfs1/tfs/DefaultCollection/globalscape/_dashboards), navigate to the **WORK** tab.
* To see all work items in the backlog, click the **Backlogs** tab.
* To query for a subset of work items or view pre-made queries, click the **Queries** tab.

##### For maintenance work

* A work item is `Ready to Work` if:
	* The Requirement or Bug is assigned to a Project Manager (currently that is `Erica Schaefer` or `Gene Burnell`).  
	**and**
	* The Requirement or Bug `State` is set to `Proposed`.   
	**and**
	* There is a development `Task` associated with this Requirement or Bug. You can check this by:
		* Double click the work item.
		* Find the **ALL LINKS** tab and click it.
		* See if there is a `Task` item linked as a `Child`.
		* Note that even if the `Task` is assigned to someone else, you may take over the work item as    
			long as the parent Requirement or bug is assigned to a Project Manager.
	
* A pre-made TFS ready to work maintenance query is located at [EFT Server - EFT - Q1 - 2017 - Backlog Ready to Work](https://gstfs1/tfs/DefaultCollection/globalscape/EFT%20Server/_workitems?path=Shared%20Queries%2FEFT%20Server%20-%20EFT%20-%20Q1%20-%202017%20-%20Backlog%20Ready%20to%20Work&_a=query)

##### For roadmap work

Note that this documentation covers roadmap work after the estimation process is complete and development begins.

* If your team assigns Requirements or Tasks to individual developers during the estimation process, work on whichever work items you are `Assigned To`.
* Otherwise:
	* Navigate to the iteration for your roadmap project (Ask your team lead if this is unclear).
	* A work item is `Ready to Work` if:
		* The Requirement is assigned to the Project Manager (this should be `Gene Burnell`).    
		**and**
		* The Requirment `State` is set to `Active`

#### b) Claim the open work item to begin work

##### For maintenance

* In the work item under **STATUS**
	* Set the `Assigned To` field to yourself
	* Set the `State` to `Active`
* For any child `Task` that you work on, under **STATUS**
	* Set the `Assigned To` field to yourself
	* Set the `State` to `Active`
	
##### For roadmap

* In the work item under **STATUS**
	* Set the `Assigned To` field to yourself
* For any child `Task` you work on, under **STATUS**
	* Set the `Assigned To` field to yourself
	* Set the `State` to `Active`
	
	
### 1. Create a feature branch for development 

This feature branch should be named following the convention `<initials>_<workItem ID>` where `<workItem ID>` is the requirement or bug number (ex. "JS_42169" or "jms_42169" etc.).

#### a) Creating the feature branch 

* Navigate to the branch the changes are needed on: 
	* `git checkout <branch_name>` --- Example: `git checkout EFT_MESAT-II`
	
Note that we will refer to this branch `<branch_name>`, as the parent branch from now on (because it is the branch you will create your new branch from, making this branch a 'parent' and your new branch a 'child').
For help deciding which branch your changes are needed on, see "Which branch should I branch off of" in [Git FAQ.md][(Git FAQ.md).

* Create your new branch: 
	* `git checkout -b <initials>_<workItem ID>` --- Example: `git checkout -b JS_34217`
	
Note that you may have to, for example, fix a bug for two versions. In this case, append the version number to one of the branches to avoid duplicate names: `<initials>_<workItem ID>_<version>` --- Example: `HB_21972_7.2`

### 2. Do the necessary work. Once a day: Rebase from your parent branch and push your changes to the server

#### a) Making sure you're up to date with rebase

This rebase is to ensure that your local project is up to date with the most recent changes. First, you will check to see if there are any changes that need    
to be rebased. If there are no changes, there is no need to rebase (since there is nothing to rebase). If there are changes, you will perform the rebase for your branch.   
See [Git FAQ.md](Git FAQ.md) "What does rebase do?" for more information on how rebase works.

Follow these steps:
1. Check to see if there are any changes from the parent of your feature branch:
	* `git checkout <parent_branch>` 
	* `git pull --rebase`
	* If the pull returns changes it means you need to rebase, proceed to step 2.
	* If the pull returns no changes, then check to see if there are any changes from master that your parent is missing:
		* `git checkout master`
		* `git pull --rebase`
		* If this returns no changes, you're done! There are no changes that need to be rebased.
		* If there are changes, this means that there are changes on master branch that have not gotten to your parent branch:
			* Contact whoever is *in charge* of <parent_branch> and ask them to perform a rebase for <parent_branch>.
				* See [Git FAQ.md](Git FAQ.md) "Who is 'in charge' of a given branch?" for help.
			* Once the rebase is completed on their end, repeat this process from step 1.
2. Rebase your feature branch onto its parent branch:
	* To rebase: Run `git rebase <parent_branch>` from your feature branch.
	* Note: **If you are not comfortable with the rebase command, please create a backup branch first.** 
		* Push all changes to avoid losing work.
		* Create a backup with: `git branch -m <feature_branch> <feature_branch>_BACKUP`
			* That is, `git branch -m <original> <backup>`
		* Then switch back to your feature branch: `git checkout <feature_branch>`
		* To use the backup: See [Git FAQ.md](Git FAQ.md) "How do I recover with a backup branch?".
	
Note: After rebase operations, you may need to force push to your feature branch. **Remember you can only force push to a branch you are 'in charge' of.** See [Git FAQ.md](Git FAQ.md) "How do I force push?" for help.

#### b) Pushing your changes to the server at least once a day

This step ensures you do not lose more than one day of work if there are problems with your local machine. It doesn’t matter if you have a bunch of messy,    
needless commits. You can squash those later. (Squash is covered in the next step, step 3: "When work is complete, clean up your commits".)

* From your feature branch, check the files which have changed: 
	* `git checkout <feature_branch>`
	* `git status`
	
This will display if your branch is up to date or it will list out the files that have changed.     
See [Git Beginner Guide.md](Git Beginner Guide.md) "Checking the status of your files" for help with `git status`.

* Add files you want to commit to the staging area:
    * `git add <filename>` --- Example: `git add filename.cpp`
	* You can use `git status` again to check that you've added the correct files before committing.
    * `git commit -m '<commit message>'` --- Example: `git commit -m "Requirement #999 - Made change XYZ"`

Note the use of the #999; where a hash tag is followed immediately by a TFS work item number, TFS will automatically link the source code commit with the TFS work item, and all changes can be traced back. Very important.     
See [Git Beginner Guide.md](Git Beginner Guide.md) "Tracking new changes" and "Committing your changes" for help with `git add` and `git commit`.

* Pushing your work to the remote feature branch
	* If the remote branch does not exist: 
		* `git push -u origin <branch name>` --- Example: `git push -u origin AM_38714`
	* If the remote branch exists: 
		* `git push origin <branch name>` --- Example: `git push origin AM_38714`
	* Note that after rebase operations, you may need to force push to your feature branch. Remember you can **only force push to a branch you are 'in charge' of** (usually only feature branches).
		* See [Git FAQ.md](Git FAQ.md) "How do I force push?" for help.
		
If you're confused about "remote branch", see [Git FAQ.md](Git FAQ.md) "What is the difference between local and remote branches?" for help.

### 3. When work is complete, clean up your commits   

#### a) Cleaning up your commits before a pull request with interactive rebase

* In general, squash commits to one commit per work item unless you have a *compelling reason* to use multiple commits.
	* A *compelling reason* to have multiple commits (A,B,...) would be to seperate code changes into one or more sets of functionality-affecting, **buildable** changes
		* For example: You squash your changes into only 2 commits (A and B) because there is a possibility that, in the future, you will want to revert the changes made in commit A while retaining the changes made in    
			commit B or vice versa. This works because you can revert either A or B and still have a **buildable** result.
	* Definitely squash commits like "typo fix", "forgot to do stuff", or "renames a thing".
* Reword commits for clarity. For example, change "adding tests" to "Task #123 Adding tests for xyz functionality".
	* **Always prepend a related TFS work item ID to commits** (whether it be a requirement number, bug number, or task number).
	
1. You can squash and reword your commits with interactive rebase:
* To determine how many commits to include, examine the commit log to see how many commmits you used.
	* Run `git log` using `UP` and `DOWN` arrow keys to navigate and `q` to exit.
	* To view only the top `x` commits, run `git log -x`.
* Perform an interactive rebase with the correct number of commits.
	* `git rebase -i HEAD~<number of commits>` --- Example: `git rebase -i HEAD~3` will use the top 3 commits
	* This will open up a linux-based text editor, if you want a Notepad-like interface, install [Git Extensions](http://gitextensions.github.io).
	* You should see the following pop up: (Note that if you remove *everything*, save, and close, your rebase will be aborted.)
	
```		
 pick 2edd6c4 Req #12981 Commit 1
 pick 2f93420 Req #12981 Commit 2

# Rebase bf26585..2f93420 onto bf26585 (2 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```
* Replace `pick` in front of each commit with whichever command you need.
	* If you want to have all of the changes in one commit with "Commit 1" as the main commit:
		* Keep `pick` for "Commit 1" (unless you need to `reword` to add the TFS work item ID)
		* Replace `pick` with either `squash` or `fixup` for "Commit 2"
			* `squash` will keep the "Req #12981 Commit 2" message while `fixup` will discard it
Example:
```
 pick 2edd6c4 Req #12981 Commit 1
 fixup 2f93420 Req #12981 Commit 2 
```
* Save and close your text editor. This will finish the rebase process or it will prompt you again to make commit message changes with the text editor. (This would happen if you used `reword` or `squash`)
* Use `git log` and the arrow keys to examine your commit history (exit with `q`)
* If everything looks good, push your changes:
	* `git push origin <feature_branch>
	* Note that after rebase operations, you may need to force push to your feature branch. Remember you can **only force push to a branch you are 'in charge' of** (usually only feature branches).
		* See [Git FAQ.md](Git FAQ.md) "How do I force push?" for help.
* **If you need to "undo" your rebase, see "How do I recover from mistakes or a bad command?" in [Git FAQ.md](Git FAQ.md) for help.**

### 4. Create a pull request with the work item ID prepended (ex. "Req #1234 ...").

#### a) Making sure everything is up to date

* Follow the steps in **2. a) Making sure you're up to date with rebase**.

#### b) Starting the pull request
1. In TFS, navigate to the repository where your feature branch is located.
	* Click **CODE**
	* Select the repository from the drop down box under code (Ex. select "EFT")
	* Click the **Pull Requests** tab.
	* At this point, if you recently committed your feature branch, you may see a quick link to create a pull request.  If not:
	* Click the blue **New pull request** button in the top right.
	* Where it says "Review changes in `Select a branch...` relative to `master`":
		* Change `Select a branch...` to your feature branch (i.e. `<initials>_<workItem ID>`)
		* **Change `master` to your feature branch's parent `<parent_branch>`**
			* This is important, **do not** pull request relative to `master`
	* In the title bar above **DESCRIPTION** the PR title **must have the TFS work item ID prepended.** (ex. "Bug #109522 ...")
		* (TFS uses this to link the pull request changes with the work item.)
	* In the **DESCRIPTION** box, include a helpful description of what happened and why for your reviewers and for future reference.
		* For example, a helpful description might:
			* List what functionality was added or changes made.
			* Briefly state what motivated these code changes.
			* Note if you made decisions based on assumptions that might not always hold true. 
			* Leave a note or question for the reviewers.
	* Add one or more peer reviewers.

#### c) Adding peer reviewer(s). 
* Under **REVIEWERS**, remove any automatically populated groups.
	* For example, in EFT, you would remove "globalscape\[EFT Server]".
* Then add one or more peer reviewers to **REVIEWERS**.
	* This should be:
		* Someone on your team who knows the area of code you are working on
		* Otherwise, someone else who knows the code or platform you're working on.
		* Otherwise, your team lead.
		* If your code involves a significant architexture change, add Greg as a peer reviewer (and explain the architecture change).
* For more information on how peer reviews should work, see the next section (d) and "How do peer reviews work?" in [Git FAQ.md](Git FAQ.md).

#### d) Opening and completing the pull request

1. Double check that you are happy with the state of your pull request.
2. Click the button "New pull request" to finish and open the pull request.
	* If there are merge conflicts when you open the pull request:
		* Fix code conflicts and perform rebase. (See "How do I manage code conflicts?" in [Git FAQ.md](Git FAQ.md).)
3. Address any comments that come in through your reviewer.
	* Once you have your alerts set up, TFS will automatically inform you of changes to your pull requests   
		See [Git Beginner Guide.md](Git Beginner Guide.md) "Setting up TFS alerts" for help with alerts.
4. No more action on the pull request is required by you, when the last peer reviewer accepts your changes, that person should hit    
	the "Complete pull request" button. See [Git FAQ.md](Git FAQ.md) "About pull requests" for more information.
5. Once your last peer reviewer completes the pull request, update the corresponding work item in TFS:
	* In the work item under **STATUS**
		* Set the `Assigned To` field to `Ivan Vasquez`
		* Set the `State` to `Resolved`
	* For any child `Task` that you completed, under **STATUS**
		* Set the `State` to `Resolved`
		* Leave the `Assigned To` field as yourself.	

#### e) Cleaning up

1. Delete the local feature branch: 
	* `git branch -D <feature_branch>` 
	* Note: You can't delete a branch while you have it checked out. You can delete your feature branch from master (do `git checkout master` first).
2. Delete any BACKUP branches you made both locally and remotely:
	* `git branch -D <feature_branch>_BACKUP` for local
	* `git push origin --delete <feature_branch>_BACKUP` for remote
	* You do not have to remove your feature branch from remote because closing the pull request does this automatically.
	
## Hotfix Workflow Summary

Follow this workflow if you've been assigned a TFS work item for a hotfix. You should be assigned a TFS `Bug` with an associated TFS `Task` telling you where the hotfix is needed.

When you receieve a hotfix `Task`:
* Under **STATUS** of the `Task`, change the `State` to `Active`
* Create a branch for your hotfix following the naming convention `<version>_<customer>_Hotfix_<workItem ID>` (ex. "7.3.1.2_Scripps_Hotfix_341088").
	* This should branch off of whichever build you need the hotfix on. If the version does not exist as a branch, you will need to pull it by tag: see "How do I pull an exact build?" in [Git FAQ.md](Git FAQ.md) for help.
* Make the necessary changes to release to the customer and create the binaries as usual.
* When work is complete, put the location of the binaries to release to the customer in the **FIX** tab of the parent `Bug` and add information about how you implemented the fix.
* Set the parent `Bug` field `State` to `Resolved` and the `Assigned To` to a Level 3 Support.
* Resolve any issues support might have.
* When the hotfix is stagnant for more than 30 days, tag and delete the branch. (See "How do I tag and delete a branch?" in [Git FAQ.md](Git FAQ.md).

If Product Management decides this hotfix should be included in current versions, they will make a new child `Task` for each version the fix should be applied to. If you are then assigned this `Task`, follow the "General Workflow" above
 to complete the work. Note that you will most likely want to cherry-pick the changes from your hotfix branch. (See "What does "cherry pick" mean?" in [Git FAQ.md](Git FAQ.md) for more info.)

TODO - update this to be more specific about how the binaries should be released?


## Team Lead Workflow

This section documents how team leads should be working with git. If you are the team lead for a roadmap project, you are 'in charge' of any associated roadmap branches.
 (See "Who is 'in charge' of a given branch?" in [Git FAQ.md](Git FAQ.md) for more details.)
 
### Keep your roadmap branch(es) up to date daily

This is to ensure you are always including new changes made by your team on the project. This should be done before a rebase.
* `git pull --rebase` for all roadmap branches

### Rebase from any roadmap branch you are in charge of daily

This rebase is to ensure that your project(s) are up to date with the most recent changes. First, you will check to see if there are any changes that need    
to be rebased. If there are no changes, there is no need to rebase (since there is nothing to rebase). If there are changes, you will perform the rebase for your branch.   
See [Git FAQ.md](Git FAQ.md) "What does rebase do?" for more information on how rebase works.

For each roadmap branch you are 'in charge' of:
1. Check to see if there are any changes from master:
	* `git checkout master` 
	* `git pull --rebase`
	* If this returns no changes, you're done! There are no changes to update your branch with. 
	* Otherwise proceed to step 2.
2. Rebase your roadmap branch:
	* Run `git rebase master` from your roadmap branch.
	
Note: After rebase operations, you may need to force push to your roadmap branch. See [Git FAQ.md](Git FAQ.md) "How do I force push?" for help.

If you feel you have a *very compelling* reason not to rebase, you need to get special permission to not rebase.

### Finish work on a roadmap branch

When your team has completed work for a roadmap branch and the changes need to be merged into master, you are in charge of the merge.

* Before-Merge Checklist
1. You must have permission or been told to do the merge by Product Management.
2. Make sure the roadmap branch is rebased onto the lastest master
3. Ensure all code in your roadmap branch has been through QA and is 100% production-ready.
* Merge steps:
	* merge
	* resolve any code conflicts
	* tag and delete the branch (See "How do I tag and delete a branch?" in [Git FAQ.md](Git FAQ.md).)

# Globalscape Git FAQ

This document is intended to be supplemental to [Git Workflow](Git Workflow.md). It details frequently asked questions related to developer processes at Globalscape.

This is a *living document*, meaning it will change as processes are changed to fit needs.

### Related Links:
* [Git FAQ](Git FAQ.md)
* [Git Workflow](Git Workflow.md)
* [Git Beginner Guide](Git Beginner Guide.md)

### Contents:
* [Branch Structure Overview](#branch-structure-overview)
* [General FAQs](#general-faqs)
* [About Rebase Process](#about-rebase-process)
* [About Pull Requests](#about-pull-requests)
* [How do I recover from mistakes or a bad command](#how-do-i-recover-from-mistakes-or-a-bad-command)

## Branch Structure Overview

### What is master branch?

It is the clean branch; all changes to master must have been approved by QA and PM. It should always reflect a production-ready state.

#### Which version is master branch?

Master is the vNext (version Next), where the changes that will be in the *next* version go. Note that you cannot put changes directly into master, see "What can be put directly into master?" below.

#### What can be put directly into master?

Nothing can be put directly into master. Feature branches should never come off of master and commits should not be made directly to master.

####  How does code get into master?

Individual development work will be done on feature branches. Feature branches are then pull requested into either quarterly maintenance or roadmap branches. Roadmap/maintenance branches are then merged into
 master by the person *in charge* of them (after being passed by QA and PM). (See ["Who is *in charge* of a given branch?"](#other-branch-faqs) below for more info.)

See the [Git Workflow.md](Git Workflow.md) document for more details about the process. [General Workflow](Git Workflow.md#general-workflow) covers individual development. [Team Lead Workflow](Git Workflow.md#team-lead-workflow)
 covers roadmap branches.

## What is a roadmap branch

In general, a roadmap branch corresponds to a roadmap project. This branch type is commonly referred to as a *development branch*. The creation of these branches will be facilitated by a project manager; team leads should not create these branches
 until after they have discussed when/where to create a branch with a project manager.

Roadmap branches can branch from either master or from another roadmap branch.

#### Naming convention

Note: The creation of these branches must be facilitated by a project manager. 

`<roadmap_name>` --- Example: `MESAT-II`
* Where `<roadmap_name>` is a descriptor referencing the roadmap project.
* If the project requires multiple branches, use `<roadmap_name>_<sub_topic>` --- Example: `AWS_AutoScaling` and `AWS_Metered`

#### Example roadmap branches

The following shows the standard roadmap branch, which branches from master. Roadmap branches will be rebased from master weekly (the pink arrows).

![](/Processes and Guidelines/lib/images/develop-branch.jpg)

This next example shows the less common scenario when a roadmap branch branches from a roadmap branch. All branches rebase from their parent, the MESAT-II branch rebases from master and the Workspaces branch rebases from MESAT-II.

![](/Processes and Guidelines/lib/images/nested-develop-branch.jpg)

## What is a release branch?

Release branches are branches where we maintain previous releases. Release branches are created from master branch when a new minor version is released to GA (ex. 7.2). Since release branches correspond to previous releases,
 they should *never* be rebased with `master`. (That is to say, release branches should not be updated to reflect the state of the master branch.) 

#### Naming convention

Note: The creation of these branches must be facilitated by a product manager.

`<major.minor.revision>` --- Example: `7.2.2`
* Release branches should specify the major version, minor version, and revision number. (This follows the general *major.minor.revision.build* format.)

Legacy note: Older release branches do not all follow this convention. Ask Lonnie French for help if you are unable to find a release branch you are looking for.

#### Example release branch

Release branches should never have `master` rebased onto them. If a change from `master` branch is needed on the `release` branch, it should be cherry-picked. See ["What does "cherry pick" mean?"](#general-faqs) for more on cherry picking.

![](/Processes and Guidelines/lib/images/maintenance-branch.jpg)

## What is a quarterly maintenance branch?

This branch is used to encompass code changes for each quarter that are not within the scope of a roadmap project (primarily maintenance work). 

#### Naming Convention

Note: The creation of these branches must be facilitated by a product manager.

`<parent_branch>_Qx_20xx` --- Example: `7.3_Q1_2017`
* Where `<parent_branch>` is the name of whichever branch this quarterly maintenance branch is coming from and the `x`s are the quarter number and year.

Legacy note: Currently, quarterly maintenance branches have "_Maintenance" appended to the  branch name.

#### Example quarterly maintenance branches

Quarterly maintenance branches are branched from `master` and `release` branches. 

![](/Processes and Guidelines/lib/images/quarterly-branch.jpg)

## What is a feature branch

A feature branch, pictured in orange, is a branch created by an individual to complete development work on a TFS work item. These can branch off from any branch except `master`. Note that this is also commonly referred to as a "topic" branch.

#### Naming convention

`<initials>_<workItem ID>` --- Example: `JS_38527`    
* For example, if John Smith is working on Bug #38527, he would create a feature branch called `JS_38527` for his development work.

Note: Since feature branches are for a single developer's work, this naming convention can be altered to fit personal preferences. The hard requirement is that you include your name or initials in the branch name so it can be identified as yours. 
Some examples of other example branch names: `jms-38527`, `smith/38527`, `smithBranchDescription`, etc. When using `/` note the [possible issues you may encounter](https://coderwall.com/p/qkofma/a-caution-about-git-branch-names-with-s).

##### Why is the naming convention for feature branches `<initials>_<workItem ID>`?
It is preferred to prefix your feature branch's name with your initials followed by the TFS Requirement or Bug number.  This way when it is saved to the server, 
everyone can see who owns a particular branch and know which requirement or bug this relates to.  You can easily find your branches and delete them when work is complete
 since they are prefixed with your initials.  This way you don't accidentally delete somebody else's branch.

#### Example feature branches

![](/Processes and Guidelines/lib/images/temporary-branch.jpg)

This example shows that feature branches can come from any branch but `master`. Also note that the feature branch will rebase with its parent branch (the pink arrows). This will happen weekly.
See ["Making sure you're up to date with rebase"](Git Workflow.md#making-sure-you-re-up-to-date-with-rebase).

## Other Branch FAQs

### Who is in charge of a given branch

The person *in charge* is the only one who is allowed to do rebases on the branch, initiate pull requests, or do any force pushing on the branch. (See the "What is a ... branch?" section above for the differences between branches.)

* `Master` - **No one.** No one should rebase, force push, or pull request on master.
* `Feature` - **Whomever is the initals for the feature branch.** (Feature branches follow the naming convention <initials>_<workItem ID>)
* `Quarterly maintenance` - Currently, **Lonnie French** is in charge of these branches. This page will be updated if that changes.
* `Release` - **No one.** Since release branches contain previous releases, they will never need to be rebased with master or pull requested into master (or any other branch).
* `Roadmap` - The **team lead** for that roadmap project.
  * When the team lead is absent, any other team lead may be temporarily *in charge*.
  * When all team leads are absent, a team lead may (before leaving) designate someone to be temporarily *in charge*.
  
### Which branch should I branch from

Quarterly maintenance, and release branches will branch off from master (aka have master as the parent branch). Roadmap branches will branch off of either master or another roadmap branch. **Note that project and/or product managers will 
facilitate the creation of these branches, and they should not be created by anyone. If you need a new branch, discuss it with your PMs.**

Feature branches will branch from (have a parent of) whichever branch work is needed on. Anyone may create a feature branch at any time (except off of master). See the following cases:

#### For maintenance work
* You should branch off a `quarterly maintenance` branch.
* If this is a *fix in current version*:
  * Use this quarter's `quarterly maintenance` branch from `master` (ex. `Q1_2017`)
    * **Note**: If you are notified that QA is *at capacity* for the current quarter, you will need to use the branch corresponding to the next quarter.
* If this is a *fix in previous version*:
  * Use this quarter's `quarterly maintenance` branch off the `release` branch corresponding to the previous version (ex. `7.2_Q1_2017`)
  * **Note**: If you are notified that QA is *at capacity* for the current quarter, you will need to use the branch corresponding to the next quarter.

#### For roadmap work
* You should branch from the roadmap branch corresponding to your project.
* If your roadmap project has multiple roadmap branches, choose whichever <roadmap>_<sub_topic> branch makes the most sense to you.
  
#### For hotfix
* You should branch off from wherever the change is needed *except master*. Never branch from master, pull the most recent build number if necessary. If you need to pull an exact build number, see ["How do I pull an exact build?"](#general-faqs) below.

### What is the difference between local and remote branches

For every git branch you have on your machine, there is a local and remote branch. The local branch refers to the version of the branch on your local machine that you make changes to. Git also creates a remote branch to keep track of these changes.
This remote branch exists on the git server, updating itself with changes made on the local branch when you `git push...`. When you do a push, you tell git to update the remote branch with any changes you have committed on your local branch. 
For example, let's say you make a feature branch JS_12345 with the command `git checkout -b JS_12345`. This will create a local branch `JS_12345` on your machine and a remote branch `origin JS_12345` on the git server. The `origin` is the default
designation of a remote branch. When you do a `git commit ...` the changes are applied only to the local branch `JS_12345`. The remote branch will not be updated with the changes; you must use `git push ...` to communicate those local commits 
to the remote branch `origin JS_12345`.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------


## General FAQs

### What is a *compelling reason* to have multiple commits

* A *compelling reason* to have multiple commits (A,B,...,n) would be to separate code changes into one or more sets of functionality-affecting, **buildable** changes
  * For example: You squash your changes into only 2 commits (A and B) because there is a possibility that, in the future, you will want to revert the changes made in commit A while retaining the changes made in 
   commit B or vice versa. This works because you can revert either A or B and still have a **buildable** result.
    * A more concrete sub-example: If you completed work on this branch for multiple child `Task` items under a Requirement, you squash your changes to one commit per `Task`
      * If you have this case, prepend the commit messages with the Task number (i.e. "Task #123 Change MESAT API")
      * So for a requirement with 2 child `Task` items, you would have 2 commits, "Task #123 Change MESAT API" and "Task #456 Makes related changes to OAI" 

### What does "cherry pick" mean

One way to apply committed work from one branch onto another is to cherry-pick it. It takes the work that was introduced
 in a commit and tries to reapply it on the branch you’re currently on. Essentially, you are taking the work you already did in one branch and automatically re-doing this work in a 
 different branch. This is useful if you have a number of commits on a branch and you want to integrate only one of them to a different branch, or
  if you only have one commit on a topic branch and you’d prefer to cherry-pick it rather than run rebase.
  
### What does "squash" or "squashing" mean

In the generic sense, Squashing is the process of merging two or more commits into one. In the specific sense, it refers to a rebase option that merges this 
commit into the parent and appending the message to the parent. For a squash example, see 
["Cleaning up your commits"](Git Workflow.md#cleaning-up-your-commits).

### What is the structure of a maintenance work item

A maintenance work item should consist of a top-level `Bug` or `Requirement` with underlying child `Task` work items that are action items, that is, tasks to be completed. The image below shows a basic maintenance `Bug` with child `Task` items that 
detail that the Product Manager wants the bug to get the `Fix in 7.2` and `Fix in 7.3`. The details and dialogue for the bug should go into the `Bug` top-level item, while only (in this case) version specific information/discussion should go in
 the associated child `Task` items. The reason we use `Task` items is to keep track of where the work item is in the development cycle. See the second image below.

![](/Processes and Guidelines/lib/images/bug-tasks-pm.png)

In this example, we can see the state of the `Requirement` by simply examining its child `Task` items. The `Task` items show that this `Bug` is still in development by Andrey Krokhin and has not yet been started by QA (both QA `Task` items are in the `Proposed` state).

![](/Processes and Guidelines/lib/images/bug-tasks-dev.png)

### How do we deal with maintaining two releases

With the understanding that releases are referred to as *major.minor.revision.build* where a *major* release would be version 7, a *minor* release would be version 7.2, a *revision* number is designated as 7.2.3, and a *build* number 
is designated as 7.2.3.19. We maintain the current and previous minor releases, so if we are currently on minor release 7.3, we maintain both 7.3 and 7.2. The current minor release is maintained on the `master` branch, and the previous 
minor release is maintained on a `release` branch. Every time a build or revision is made, it is tagged with a git tag on the appropriate branch.

Note: As an artifact, our current `release` branch for the previous minor release is incorrectly called 7.2.2. The subsequent `release` branches will have the correct name.

### How do I pull an exact build

* Every build should be tagged in git. Typing `git tag` should list all of the available tags (try `git fetch --tags` if you're missing tags).
* You can perform a search with `grep` by typing `git tag | grep "7.3.4"`. Replace `"7.3.4"` with any text you want to search for.
* You can go to a tag, for example `EFT/EFT_7.1.0_build_1`, via:
  * `git checkout -b EFT/EFT_7.1.0_build_1`. 
  * This creates a new branch in the state of the specified tag (named the same as the tag). 
  * Note that you will not need to do any rebases since this branch should not receive new updates. 
* Remember to delete this branch when work is complete

Quick reference:
* `git tag | grep <build number>` to search for a tag
* `git checkout -b <tag_name>` to create a branch with the state of when the tag was created.
* Remember to delete this branch when your work is complete.

**CAUTION: This 'exact build' branch should not interact with other branches.** If you want changes you make on this branch to be included in some other branch, you **must cherry-pick** the changes.
* See "What does "cherry pick" mean?" for more info on cherry picking.

### How do I tag and delete a branch

**You may only tag and delete branches that you are *in charge* of.** (See ["Who is *in charge* of a given branch?"](#other-branch-faqs) above for help.)

This should be done at the end of a branch's life cycle. We use tags to make allow us to get back to the final state of the branch even after we delete it.

* Tag the branch:
  * `git tag <tag_name>`
    * The current convention is `<branch_name>_final` to designate that this is the end of the branch's life cycle.
  * `git push origin <tag_name>`
* Delete the branch:
	* `git branch -D <branch>` to delete locally
	* `git push origin --delete <branch>` to delete remotely
  
### Who are the level 3 supports

Level 3 Analyst
*	Michael O’Campo
*	Jesse Heredia
*	Brian Arriaga (soon to be PM)
*	Jackson Hulme 
  
### How does this differ from the Gitflow docs?

Our processes are similar to the "Gitflow" process originally described by [Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/) and redefined by [Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow).
While these are fantastic resources to better understand our process, our Globalscape documents should be considered to have a higher priority on conflicting issues.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## About Rebase Process

### Why do we rebase?

We rebase to integrate code changes frequently, resolving conflicts as they occur (instead of at the end of a project's life). 
* More frequent integration makes it more likely that git will be able to automatically resolve changes without developer input.
* When a conflict occurs, it will be smaller in scope and the conflicting code changes will be more recent and fresh in the developers` minds.
* With rebase you can rewrite your branch history, squashing related commits, and rewording commit messages - this allows for a cleaner commit history which is easier to follow.

Rebasing results in fewer conflicts when the final merge happens.

### What does rebase do?

Rebasing is the process of moving a branch to a new base commit. From a content perspective, rebasing is just moving a branch from starting from one commit on the parent branch to starting from another. In other words, you are updating
 the feature branch to include changes from its parent branch. Note that internally, Git accomplishes this by creating new commits and applying them to the specified base — literally rewriting your project history. 
It’s important to understand that, even though the branch looks the same, it’s composed of entirely new commits. 

The command `git rebase <base>` rebases the current branch *onto* <base>. In our case, base will be the <parent_branch>. As pictured in the example, a branch <feature> was created from the <parent> branch 2 commits ago (each circle is a commit).
These 2 commits were then made on <parent> branch, causing the <feature> branch to be out of date. Now, a rebase occurs moving the <feature> branch *ahead* of master by reapplying the changes on <feature> branch *after* the 2 new commits on <parent> branch.

![](/Processes and Guidelines/lib/images/rebase-example.png)

### How do I rebase?

#### For feature branches

* See  ["Making sure you're up to date with rebase"](Git Workflow.md#making-sure-you-re-up-to-date-with-rebase). 

#### For roadmap branches

* Note that only the team lead of the roadmap project should perform rebase operations on the roadmap branch. 
  * If a team lead is absent, see ["Who is *in charge* of a given branch?"](#other-branch-faqs) above for exceptions.
* Instructions can be found in the ["Team Lead Workflow"](Git Workflow.md#team-lead-workflow).

### When should rebases happen?

You should **only rebase in branches you are *in charge* of**. (See ["Who is *in charge* of a given branch?"](#other-branch-faqs) for help.) In most cases, a rebase should happen weekly (see specific cases and picture below). This is done to ensure branches are 
never more than a week behind any changes relevant to the branch. For the rebase process for feature branches, 
see ["Making sure you're up to date with rebase"](Git Workflow.md#making-sure-you-re-up-to-date-with-rebase). For roadmap branches, see the ["Team Lead Workflow"](Git Workflow.md#team-lead-workflow).

**Note the terminology:** To have *master rebased onto roadmap* or to *rebase master onto roadmap* means that `roadmap` will get any new changes made on `master` since the last rebase. People will also commonly use the phrase 
*rebase roadmap from master* to mean the same thing. We will use the prior terminology here to discuss rebase.

In what cases do rebases happen? (The pink arrows represent rebases.) **Again: Do not rebase in any branches that you are not *in charge* of**
* Weekly:
  * All `roadmap` branches should have `master` rebased onto them. (aka update `roadmap` with any new changes from `master`)
  * All `quarterly maintenance` branches should have `master` rebased onto them.
  * All `feature` branches should have their parent branch rebased onto them (unless the parentage involves a `release` branch).
* Special cases:
  * `release` branches never have `master` rebased onto them 
    * In the case where a `release` branch needs the same change as `master`, the change should be *cherry-picked* either directly from `master` or from master's `quarterly maintenance` branch. (See "What does "cherry pick" mean?" above)
  * ***1** on picture - If a *cherry pick* occurs here, both the `quarterly` branch and the any subsequent `feature` branches should be updated with the new code changes using rebase.
  * ***2** on picture - If a different feature branch is merged (via pull request) into the `quarterly` branch, any other `feature` branches should be updated with the new code changes using rebase.
  
For information about the different branch types, see ["Branch Structure Overview"](#branch-structure-overview).

![](/Processes and Guidelines/lib/images/rebase-overview.jpg)

### How do I force push

**You may only force push to branches you are *in charge* of.** (See ["Who is *in charge* of a given branch?"](#other-branch-faqs) for help.)

* If you did not just create one for rebase, create a backup branch since force pushing disables safety checks and **can cause the remote repository to lose commits**.
* Create a backup of your branch:
  * `git checkout -b <branch>_BACKUP`
    * Make sure this name is unique, if you already have one or more backup branches, name the new branch `...BACKUP_<date>` . 
* Go back to your branch and force push:
  * `git checkout <branch>`
  * `git push --force origin <branch>`
* Keep the backup branch around until you are *absolutely sure* everything went through the way you want it (
* Once you are sure, delete the backup branch: `git branch -D <branch>_BACKUP`
* If you need to 'undo' your force push, see ["How do I recover with a backup branch?"](#how-do-i-recover-with-a-backup-branch).

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## About Pull Requests

### What is a pull request?

Pull requests let you tell others about changes you've pushed to a branch. Once a pull request is opened, reviewers are added to discuss and code review the potential changes. You can add follow-up commits 
to a branch before the changes are merged. (Note that a pull request uses a git merge to incorporate changes.) Putting code review information on the pull requests allows you (or someone else) to 
easily get context on changes. This is especially useful if you need to get information on changes long after they were made.

Note that questions/information in pull requests does not have to be in English, and should be in whichever language is easiest for the developers who are discussing the code.

### When is a pull request used?

A pull request is used when rejoining feature branches to their corresponding parent branch. This is when net-new work is being pushed into the larger scheme, 
and their purpose is to serve as a common forum for a formal Peer Review. This is in order to get a second set of eyes on code before QA or a customer get it. Ideally, peer reviews will 
catch a number of issues before it reaches QA or a customer.

Other notes:
* A pull request should also be used when using 'cherry-pick' to apply changes from one branch onto another, even though these are not "net-new".
* Pull requests are not required to merge a roadmap or quarterly maintenance branch into master. However, note that **only the person *in charge* of the roadmap/maintenance branch may do this and only at specified times**. 

#### Can I skip the pull request process?

You may only skip the pull request process for urgent issues *when a product manager grants permission to skip.* (For example, this might be allowed for a time sensitive hotfix. However, the best
 course of action would still be to make a pull request and get **immediate** attention from the reviewer(s).

#### Between which branches do pull requests occur?

All branches are pull requested into their 'parent' branch. (See ["Which branch should I branch from?"](#other-branch-faqs) for help with parent branches.) The possibilities are listed below.

**Remember only someone [in charge of a given branch](#who-is-in-charge-of-a-given-branch) may initiate pull requests.**

* Feature branches into:
  * Roadmap 
  * Quarterly maintenance 
  * Release 
* Roadmap branches:
  * Generally merged, not pull requested, into:
    * Parent Roadmap branch
    * Master 
* Quarterly maintenance:
  * Generally merged, not pull requested, into:
    * Master 
* Release
  * Should never be pull requested.

### How do I generate a pull request?

See ["Create a pull request"](Git Workflow.md#5-create-a-pull-request).

### Where can I find pull requests assigned to me?

If you did not receive an e-mail notification when a pull request was assigned to you, please set up your alerts for TFS. This can be found by clicking the settings gear in the upper right hand corner and clicking the alerts tab.

You can view pull requests by going to the **CODE** tab for your repository and clicking the "Pull Requests" sub-tab.

### How do peer reviews work

See the immediately following FAQs on peer reviews.

### What do I do as a peer reviewer?

As a peer reviewer, you are now tasked with understanding the changes the developer is making to the code. Note that your name will be associated with these changes, so you should understand what changes are being 
made in the code before accepting a pull request. Make sure to ask the developer any questions you have.

#### What priority are code reviews?

You should do your peer reviews ASAP (as soon as possible). Do not leave code reviews open for multiple days, the day after the code review is released, make it a top priority. 
This is done to avoid unnecessarily long delays where pull requests are sitting unactive. To avoid context switching throughout the day, it is suggested that you set aside time at either 
the beginning or end of each day for code reviews. So collect code reviews throughout the day and then complete them at a set time that day or the next morning.
 However, if you have a code review that you were told is *urgent* (i.e. hotfix), please address that code review as soon as you receive it.
 

#### Time to spend on a review:

Time spent on code reviews should fall somewhere under the 25% of each day that is spent on non-development work. The time you spend on each pull request will vary depending on the complexity of the 
changes to review. In general, spend the time that is necessary for you to understand what the changes do. For less complex changes, this 
might be accomplished by asking a few questions on the peer review. For more complex changes, you might have to build, test, and/or schedule pair programming with the developer to undestand the changes. How *complex* a pull request is will vary between
 each reviewer. Changes that are *simple* for one developer could be *complex* for another developer if they are reviewing something they are less familiar with. Remember that
 your role as a reviewer is to try to find any issues the developer might have missed. 

#### Guideline for code review:

* Get a basic understanding of the context surrounding the change
  * Ask the developer if this is not immediately clear, you shouldn't have to spend more than a few minutes to get the context.
* Look over the code
  * Check for both style and substance.
  * If you think "Hmm, I'm not sure what this piece of code is doing.". Ask the developer about it (even if it seems like a silly question). 
* Write comments and explain issues 
  * Responding to new comments as necessary
  * Mark your response as `Waiting for author` when appropriate (see next section).
  
#### Note your response in TFS:

* Decide your response:
  * Find your name under "Reviewers"
    * Change the drop down box value from `No response` to:
      * `Waiting for author` - when you are waiting for changes in response to comments
      * `Approved` - when you are confident in this code and willing to accept any consequences as if you developed it yourself
      * `Rejected` - when you believe this code should not be included

#### Close the PR if you are the last peer reviewer:

**If you are the last (or only) peer reviewer to Approve changes, you are responsible for completing the pull request.**
  * Double check that there are still "No merge conflicts"
  * Double check that all peer reviewers have `Approved` and you are satisfied with the code. Remember, your name will be on these changes.
  * Complete the pull request by clicking "Complete pull request"

### Who should I add as a peer reviewer?

See ["Adding peer reviewers"](Git Workflow.md#adding-peer-reviewers).

### How do I manage code conflicts

Go into the file(s) with conflicting changes and manually alter the conflicting code to resolve conflicts. Once you commit and push these changes, they will show up on the pull request in TFS. 
You may have to click on "Target branch updated | re-evaluate" in the top right to check if there are still merge conflicts.

### Why does a pull request have the 'wrong' author? How do I see the 'real' author of changes? (Does the pull request replace the author?)

The author of a pull request will be the peer reviewer who hits "Complete pull request". The author of the code changes can be found on the original commit (see example below).

Note that both the green and red commits are about Bug #341573. The difference is that the PR 877 commit shows the author as the peer reviewer who completed the PR, and the original commit shows the author as the person who made the code changes.

![](/Processes and Guidelines/lib/images/pr-author.png)


--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## How do I recover from mistakes or a bad command

### How do I reset git to a previous state

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
* This puts you where you were before the rebase and after you committed "commit 2".
  * You can see that you have made "commit 2" in this state by looking at the top of the `git log` output
* If you've messed up you can go to the git ref before your "reset" with another reset.
  * If you haven't done anything else, this would be:
    * `git reset --hard HEAD@{1}`
  * Otherwise look at the `git reflog` to determine where one step before your reset is.

### How do I create a backup branch

* Push all changes to avoid losing work.
* Create a backup with: 
   * `git branch -m <original> <backup>`
	* Specifically: `git branch -m <feature_branch> <feature_branch>_BACKUP`
	* If you already have one or more backups, name `<backup>` as `<feature_branch>_BACKUP_<date>` --- Example: "JS_8927_BACKUP_021517" or "JS_8927_BACKUP_021517_1" and "JS_8927_BACKUP_021517_2" etc
* Then switch back to your feature branch: `git checkout <feature_branch>`

### How do I recover with a backup branch

This is fairly simple: delete the original branch that is in a bad state and use the "_BACKUP" branch instead (renaming it to the original).

* **This is only if you have made a _BACKUP branch.**
* Delete the bad branch:
  * `git branch -D <feature_branch>` to delete locally
  * `git push origin --delete <feature_branch>` to delete remotely
* Rename the _BACKUP branch:
  * git branch -m <branch>_BACKUP <branch>
  * git push -u origin <branch>

### What can be undone? Force push?

Force pushes cannot be undone unless you created a backup branch. This is why force pushing to a branch you are not *in charge* of should **never** happen. (See "How do I recover with a backup branch?" above.)

Things that can be undone involve everything on local—you should be doing a rebase locally and making sure that it’s 100% good to go before pushing anywhere. If you screw up locally, no big deal, 
you can always force-pull from remote to get your branch back in a good state. The answer here is: if you aren’t sure, find someone who knows to look over your shoulder and be your sanity check. Communication!!!! 
Better to measure twice and cut once than cut once and lose your finger. 

### How do I edit or change my commit message?

#### For your most recent commit

See ["Changing your last commit"](Git Beginner Guide.md#changing-your-last-commit) in Git Beginner Guide.md.

#### For other commits

See ["Cleaning up your commits"](Git Workflow.md#cleaning-up-your-commits).

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
