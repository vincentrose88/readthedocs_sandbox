# GitLab best practice

- [Executive summary](#executive-summary)
- [GitLab preferences](#gitlab-preferences)
- [Repository](#repository)
  - [Included in repository](#included-in-repository)
  - [Excluded in the repository](#excluded-in-the-repository)
    - [Documentation](#documentation)
- [Branches](#branches)
  - [DOs](#dos)
  - [DON'Ts](#donts)
  - [How-to make branches](#how-to-make-branches)
  - [Definition of Done](#definition-of-done)
  - [Sharing files between branches](#sharing-files-between-branches)
  - [Merge updated master into the branch](#merge-updated-master-into-the-branch)
  - [Cleaning up local branches](#cleaning-up-local-branches)
  - [Changes committed to the _wrong branch_](#changes-committed-to-the-wrong-branch)
    - [Cherry-pick changes to _correct branch_](#cherry-pick-changes-to-correct-branch)
    - [Revert changes on _wrong branch_](#revert-changes-on-wrong-branch)
- [Working with (large) data](#working-with-large-data)
  - [Making the link](#making-the-link)
- [Issues](#issues)
  - [Structure issues and comments with headers](#structure-issues-and-comments-with-headers)
  - [Comments vs description in issues](#comments-vs-description-in-issues)
  - [Description style](#description-style)
  - [Linked issues and use of the ~Blocked label](#linked-issues-and-use-of-the-blocked-label)
- [Commits](#commits)
  - [Commit chunks](#commit-chunks)
- [Tags](#tags)
- [Submodule](#submodule)
- [Additional resources](#additional-resources)

## Executive summary

The following goes into a lot of details on how to best use GitLab. It is meant as a look up resource (use the Table of Content above) to be used frequently by newly onboarded team members, though even veteran GitLab wizards (such as myself @vra) does occasionally refer to this resource to ensure best practices is still upheld.

With that in mind, they key messages can be summarised into:

0. Always work in a branch (see [How-to make branches](#how-to-make-branches)) and create the relevant folder structure for your work. Use soft relative links when possible and avoid absolute paths, as these will break when another person checks out the branch.

1. Ensure your setup is correctly sat up, so you can easily interact with git and always know whether you are in a folder watched by git or not. :bulb: Use `realpath .` instead of `pwd` to avoid getting confused by symbolic links.

2. Striving for atomic commits (see [Commits](#commits)) means

- Avoid having only one commit with all your work at the end of a day. The average commits per day is 5-10 for a project team member working full time.
- Avoid commits with more than a couple of files changes.
- Split the changes into commit messages with distinct tags. Example of proper workflow:
>
> 1. Commit the latest version of a script with `analys:` or `feat:`
> 2. Run the script and debug the code with the `fix:` tag
> 3. Run the working script and commit the results with the `result:` tag

3. Document your work in such a way another team member can take over. Take advantage of the markdown language to create a well structured README.md in your folder

4. Ensure that you QC your code and documentation before submitting the branch for approval and merge to `master`, e.g. Ensure the code can run end-to-end without mistakes and that the results and scripts are aligned.

5. Pull the latest version of `master` and merge into your branch often (see [Merge updated master into the branch](#merge-updated-master-into-the-branch)), or at least every time `master` is updated to avoid not catching merge conflicts.

## GitLab preferences

Two settings is highly recommended for navigating GitLab instead of the file system and using the tag feature (`@`):

1. Preference -> Behaviour -> Layout width -> Fluid
2. Preference -> Notifications -> Global mention -> On mention

## Repository

- We have only one repo, with subfolders for different work packages and documents.
- We use **branches** to work on a different aspect of the projects, such as analyses in work packages
- PM (@vra) is responsible for maintaining the `master` branch
- All new branches should branch from the latest version of `master`

### Included in repository

- Link to input data used in analyses
- Scripts and environment setup configuration (e.g. `renv.lock`)
- Relevant results and other output are part of the repo

### Excluded in the repository

- Large data files, such as raw data from the client (but link to them is fine)
- Rendered documents where the source is available, e.g. a power point presentation (can be ignored when relevant)
- Volatile large file, such as `.Rdata` (static is okay)
- Intermediate output files which can easily be re-generated

#### Documentation

Documentation, including presentations and materials, are special cases, as these should not be a part of the repo, but instead recide in the project SharePoint/OneDrive and be accessible to all project team members via MS Teams.

## Branches

We work in branches (not directly on `master`) which are merged into `master` (see [Definition of Done](#definition-of-done)) when done.

### DOs

- Always make new branches from the latest version of `master`.
- Make "thin" branches which solves a concrete issue. :bulb: **Tip**: Use the GitLab Wizard to create branches, see [How-to make branches](#how-to-make-branches) below.
- Pull master and merge into branch often.
- Merge into `master` once the branch is done (see [Definition of Done](#definition-of-done)) and open up a new branch from the updated master.
- When a significant part of an issue is done, merge the corresponding branch and open up a new issue with the remaining to-do tasks.

### DON'Ts

- Make a single branch for all your work, e.g. `vra/analysis`
- Make "thick" branches containing all the work for a work package, e.g. `wp-single-cell/analyses`
- Give generic branch names, e.g. `analysis`. :bulb: **Tip**: Use the GitLab Wizard to create branches, see [How-to make branches](#how-to-make-branches) below.

### How-to make branches

Use the issue's built-in function to create a branch for an issue to ensure everything stays connected:

1. Go to the issue which you would want to work on (and remember to assign yourself to the issue)
2. Click the "Create merge request" green button below the issue text
3. The wizard will auto-generate a branch name, which is recommended to use. The source should be `master`
4. This will also create a draft merge request, which is automatically filled out with the `close #<issue-number>` in the description, as described in step 4 in the below section ([Definition of Done](#definition-of-done))

**Alternative 1**: Use GitLab (`+` icon next branch name in project 'frontpage') to create a new branch and check it out with:

```zsh
git checkout <branch-name>
```

**Alternative 2**: Use the command-line

```zsh
git checkout master
git pull
git checkout -b <branch-name>
git push --set-upstream origin <branch-name>
```

### Definition of Done

**aka. how and when to merge to master**

Follow this 6-step guide

1. Do basic and intuitive QC of the code and results, i.e. The best we know right now. Keep quick-and-dirty version in the branch.
3. Merge updated `master` into branch (see [Merge updated master into the branch](#merge-updated-master-into-the-branch))
3. Merge to `master` with GitLab's merge request (see menubar on left (keyboard shortcut: `g->m`)). If you have used the GitLab wizard to create the branch, the merge request is already made for you. In that case, simply just remove the draft by clicking the "Mark as ready" button in the upper right corner of the merge request.
4. Add `close #<issue-number>` in description in merge request if not already present.
5. Tag with `@<initials>` another relevant project team member (e.g. the "Passer" of a Pass-the-Baton (PtB) issue) to notify them the issue is ready to be closed. Ask the other team member to give a final check of the results or point out specific areas which you would like feedback on. **NB:** This step can be omitted at the PM's discretion  - just remember to remove the auto-added approval role when creating the merge request.
6. For whoever will merge the branch, ensure that the branch is deleted, but **do not** squash commits, i.e.:
   - [x] Delete source branch when the merge request is accepted.
   - [ ] Squash commits when the merge request is accepted.

### Sharing files between branches<a name="sharing-files"></a>

- Sharing files between branches should **only** happen via `git checkout <branch-name> -- path/to/file`,
  - **NB** Only work when you are in the 'root' of the project, as the path is relative to this.
- Files in `master` are up-to-date and are always available in branches by merging the latest version of `master` into the branch

### Merge updated master into the branch

When `master` is updated, it is very useful to align your feature branch to include the newest updates. To do this simply pull the latest master and merge it into your branch. You will probably need to resolve potential conflicts (easiest with a modern editor) and potentially recreate not-tracked files into a new location, such as the RStudio project.

**Step-by-step**:

1. First commit any changes you might have on your branch

```zsh
git commit -am "analys: work-in-progress commit to ready merge with updated master"
```

2. Then checkout out the master and pull the latest version

```zsh
git checkout master
git pull
```

3. Now go back to your branch and merge master (use the default commit message)

```zsh
git checkout <branch-name>
git merge master
```

4. Resolve potential merge conflicts.
5. Recreate not-tracked files to their new locations if required such as the RStudio project
6. Commit and push

### Cleaning up local branches

As the projects progress and you finish work on more and more branches, despite these being deleted remotely (i.e. on GitLab), your **local** clone of the repository can start to fill up with already merged branches. To clean up the local clone the simple 2-step guide here will delete the local branches which have been merged into the `master` branch.

1. Pull the latest version of `master`:

  ```zsh
  git checkout master
  git pull
  ```

2. Identify all local branches merged to `master` and delete them _except_ the `master` branch itself

  ```zsh
  git branch --merged | grep -i -v -E "master" | xargs git branch -d
  ```

  :warning: **NB**: This will delete all local branches which are merged into `master`, including new branches which is identical to `master` as they are considered merged into `master`.

### Changes committed to the _wrong branch_

Should it happen that you accidentally made some changes and committed them to a _wrong branch_, it is possible to move these changes/commits to a _correct branch_ and revert the changes on the _wrong branch_.
You can do this by utilising the `git cherry-pick` and `git revert` commands.
This is only possible if you have "clean" commits, i.e. one or more commits with specifically the changes would want to move to another branch. If you have "dirty" commits e.g. several changes in multiple files and you only want to move some of these changes to another branch, this cannot not be obtained with `git cherry-pick`. However, if you just want to move the changes in a single file you can use the method described [here](#sharing-files).

#### Cherry-pick changes to _correct branch_

1. First you need be on the _wrong branch_ and get the commit ID of the changes you want to move:

```zsh
git log
```

The output from `git log` will look something like this:

```
commit 2e6c67b659be7739d62bebfafac9f01c51991af5
Author: Marie Li Jensen <mail@corp.com>
Date:   Wed Aug 23 12:25:37 2023 +0200

    Update CHANGELOG.md

commit bf09f82513a9f3eeaab38e45ba2abbb101057fb5
Author: Marie Li Jensen <mail@corp.com>
Date:   Wed Aug 23 11:32:06 2023 +0200

    results: update col overview with new ST data

commit b62cde94e324ed44a7cb7492cd3edb0623303bc0
Author: Marie Li Jensen <mail@corp.com>
Date:   Wed Aug 23 11:18:19 2023 +0200

    results: update metadata with new ST DGE results
```

From the output of the `git log` command, it is possible to simply copy paste the commits you want to move.

2. Then you should checkout to the _correct branch_:

```zsh
git checkout <correct-branch-name>
```

3. Finally, you utilise the `git cherry-pick` command to move the changes:

```zsh
git cherry-pick <commit-id>
```

Given the example commits above, the use of `git cherry-pick` would look like this:

```zsh
git cherry-pick 2e6c67b659be7739d62bebfafac9f01c51991af5
git cherry-pick bf09f82513a9f3eeaab38e45ba2abbb101057fb5
git cherry-pick b62cde94e324ed44a7cb7492cd3edb0623303bc0
```

> You can find more information about the `git cherry-pick` command [here](https://www.atlassian.com/git/tutorials/cherry-pick).

#### Revert changes on _wrong branch_

Once these changes have been move to the _correct branch_, the changes made on the _wrong branch_ should be cleaned up.

1. First you should checkout to the _wrong branch_:

```zsh
git checkout <wrong-branch-name>
```

2. Next you want to use the `git revert` command on each of the commits you have moved:

```zsh
git revert <commit-id>
```

> Use the `--no-commit` option in the `git revert` command to collect all of the reverted changes in one commit:
>
> ```zsh
> git revert --no-commit <commit-id>
> ```
>
> Commit the clean up as a single commit afterwards:
>
> ```zsh
> git commit -m "the commit message for all of the reverted changes"
> ```

The use of `git revert` would look like this using the example commits:

```zsh
git revert --no-commit b62cde94e324ed44a7cb7492cd3edb0623303bc0
git revert --no-commit bf09f82513a9f3eeaab38e45ba2abbb101057fb5
git revert --no-commit 2e6c67b659be7739d62bebfafac9f01c51991af5
git commit -m "build: revert wrongly made changes to meta, col overview and changelog"
```

## Working with (large) data

We don't want to put large datasets on git, but we want to have any checkout of the git repo to be able to access the data.
We also want to easily load in the data without using absolute paths in our scripts (this can be broken!)

**Solution**
Add a soft link to the data folder in your git project. In R use the [`here`](https://here.r-lib.org) library to access your data.

### Making the link

The link should be in the git project root (the data itself should not be in Git).
The git project root path is the cloned repo, i.e.:

`/opt/projects/<client_ID>/<project_ID>/users/<initials>/<GitLab project>`

If the git project root path is:

`/opt/projects/7816_DRB/2022-01_Target_Discovery_data_foundation/users/vra/target-discovery-data-foundation`

and the data path is:
`/opt/projects/7816_DRB/2022-01_Target_Discovery_data_foundation/data`

The command is simply

```zsh
cd `/opt/projects/7816_DRB/2022-01_Target_Discovery_data_foundation/users/vra/target-discovery-data-foundation`
ln -s /opt/projects/7816_DRB/2022-01_Target_Discovery_data_foundation/data .
git add data
git commit -m "build: Ease navigation"
git push
```

To read a data file in R, use the [`here`](https://here.r-lib.org) library with the data-link just created, e.g.

```r
### Setup
# Libraries
library(tidyverse)
library(here)

# Data: Read in the tab-separated zipped RNA expression consensus downloaded from the Human Protein Atlas
# "data" is the soft link to the data location
hpa = read_delim(here("data","rna_tissue_consensus.tsv.zip"))
```

## Issues

- Use the board view (under `Issues` in the menu bar to the left) to visualize the issues
- Assign yourself to the issue(s) you are working on
- The issue's assignee is responsible for updating the issue as needed
- You can tag another member with `@` in front of their initials, e.g. "`@vra`". This will send a mail to their e-mail
- To avoid being spammed by GitLab, go to your user preference -> Notification -> Set "Global notification level" to "On mention"
- Use existing issues to facilitate discussions or create a new issue if missing. The PM can help you with labeling and such, but the most important thing is that the issue is created, even if it is just a title.

### Structure issues and comments with headers

Don't use `#`, as it is reserved for the title. All subtitles should be in order from there on (`##`, `###` etc.)

Add `[[_TOC_]]` to generate a Table Of Content to issues and wiki pages, but not comments to ease the navigation of issue.

### Comments vs description in issues

Comments are for discussion threads and issue meeting notes.

The description is for established "issue knowledge and decision" (e.g. action points & resources).

### Description style

@vra likes checkmarks, so many issues descriptions are set up as to-do items, but feel free to use whatever you find most useful. And rather overshare than not share enough.

The issue will be created from an issue template. This is for inspiration, so feel free to not use it if not relevant.

### Linked issues and use of the ~Blocked label

When applicable we use the [linked issue](https://docs.gitlab.com/ee/user/project/issues/related_issues.html) functionality to tie issues together with the following guide-lines:

- `relates to`: Is part of another issue and can block a part of said issue, i.e. work can be done on the issues but they are inter-depended.
- `blocks` and `blocked by`: Blocks or is blocked completely by another issue, i.e. no work can be done before the blocking issue is resolved. This includes expected blockages, such as data needs to be downloaded before it can be analysed (contrived example)

The ~Blocked label is used to attract the attention of the PM and the rest of the team. Use it for example to get help.

## Commits

We _strive_ to follow [atomic commit](https://sparkbox.com/foundry/atomic_commits_with_git), with atomic being defined as:
> atomic: of or forming a single irreducible unit or component in a larger system

**NB:** Commit message should explain the _why_ and not the _what_

Additionally, we strive to tag our commit message with one of the following topic tags:

- `analys:` Adding or expanding analysis work (_will probably be the most common topic tag_)
- `result:` Relevant results from analysis added (_to ensure seperation from the `analys:` tag_)
- `feat:` Improving or adding functionality which isn't directly applicant to an analysis
- `fix:` Fix bugs (why isn't as important as how here)
- `build:` A change to the repository, such as updating `.gitignore` or changing folder structure in repository.
- `data:` Adding or formatting data
- `refact:` Refactoring code without changing any of its features, e.g. creating a function for a repeated command.
- `perf:` Improving performance
- `style:` Changes to ensure code standard and style, e.g. adding docstrings to functions
- `docs:` All documentation related work, e.g. updating a `README.md`
- `test:` Adding or improving test.

**Optional**: Add scope in brackets after topic tag, e.g. `analys(wp1-network): Find enriched networks`.

The commit message title **must** be maximum of 72 characters long, while longer comments should be stated after the title, separated with a newline, e.g.:

```
docs: Add commit info to project conventions in a title of 72 characters

This is a comment which is stated after the title and can be as long and verbose as needed.

You can even add multiple comments, as long they are separated by newlines, like this and the above comments.
```

Avoid using special characters in commit messages if possible.

Commit message should be written in the imperative tense.

### Commit chunks

To further enhance the ideal of atomic commits, you can commit individual chunks using:

```zsh
git add -p
```

Where `-p` stands for `--patch`. You can optionally add a extra argument as the path to the specific file if you only want to add chunks of this to the commit

This will start an interactive wizard where you can add individual chunks of code to your commit.

## Tags

When working on a project which contains a Target Portals, where iteratively released are expected, using GitLab Tags functionality is an excellent way to keep track of the different releases without keeping multiple copies around. This is also useful when the project expect updates to the data released for analysis.
Once `master` is at a significant milestone, you can tag that version of `master` with the command:

```zsh
git tag -a 1.0.0 -m "First release of the Target Portal to client"
```

- the `-a` flag is to attach tag to the latest commit to `master`, without it you'd be adding a tag which has no relation to the code
- the `1.0.0` parameter is the name of the tag and shows we follow [Semantic Versioning](https://semver.org/), which is the industry standard
- the `-m "First release of the Target Portal to the client` is the tag description, which is similar to a commit message.

It is best practice to ensure that everytime a branch is merged to master the file `CHANGELOG.md` is updated with a short description of what the merged branch add to the project. When creating a tag, it is therefore important to update the `CHANGELOG.md` file as well to ensure it is clear when features was added to the project.

An example of a `CHANGELOG.md` file is:

```
# Next version
(placeholder for next version)

# 1.0.0
- Shared client data analyzed and mapped to UniProtAC
- Add HPA information to each protein in client data
- Spin up of Target Portal 1.0 with required metadata information 

# 0.1.0
- Clean up [Transcriptomics Network folder](./transcriptomic-networks/) to only contain [main](./transcriptomic-networks/main/) (Released in Network Portal 1.1) and [experimental](./transcriptomic-networks/experimental/) (Temporary folder containing experimental analyses)
- Update readme in the [Transcriptomics Network folder](./transcriptomic-networks/)

# 0.0.0
- Initiate [Semantic Versioning](https://semver.org/)
```

## Submodule

You can clone an "external" repo into you "active" repo by using git's submodule ([read more](https://git-scm.com/book/en/v2/Git-Tools-Submodules)), by running the following commands

```zsh
git submodule add <url to repo> # Add the reference to the external repo
cd <path/to/submodule-repo> # Go to the folder, which will be empty
git submodule init # Activate the submodule 
git submodule update # Get all the files
```

If done correctly you should see the external repo with its content in this subfolder. Note that you are now juggling two repos, each with their own branch, so don't be surprised if your prompt changes branch name when you navigate between the two.

## Additional resources

- [How to duplicate a file while preserving git line history](https://devblogs.microsoft.com/oldnewthing/20190919-00/?p=102904)
