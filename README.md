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

TODO - details
