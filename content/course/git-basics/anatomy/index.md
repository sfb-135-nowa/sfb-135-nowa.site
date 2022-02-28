---
title: Git Anatomy
date: 2021-12-13T11:00:00+01:00
authors: [tamara-cook]
linkTitle: Anatomy
weight: 20
type: book
---

In this chapter, you will learn to explore a cloned Git repo.
Alternatively, you can do most of this without cloning using Gitlab's web interface directly.

## Repository Contents

A local repo consists of two main areas:

`/.git` directory
: Version history data and repo configuration
: This subdirectory is managed by git and rarely edited by hand

Remaining contents
: Working copy of current version
: It is user-editable, but git can write to it

This chapter deals with the version history, the working copy will become relevant for the following chapter.

## Commit history

Please visit the example project's [history page].
From the main page, you can find this by searching for _History_.

Alternatively, you may open the local project in Tower and select History view.
Selecting a commit reveals the file changes associated with this commit.
You can switch from list view to tree view which displays the full project state associated with the commit instead of the changes.

Commits are the building-block of the version history.
_commit_ is the Git-specific term for version.
The history is sorted in reverse-chronological order, so you can read who has contributed which improvement from newest to oldest.
A commit mainly consists of these parts:

autogenerated metadata
: Unique ID, Author, date, …

User-defined metadata
: Descriptive message (normally one line)

Parent ID
: Reference to any older commit

Data
: Changes compared to parent
: Represents a certain complete folder state

Through this referential structure, the history can form tree graphs where multiple commits can have a common parent.
This is the foundation of parallel history threads.
It also means that commits are rarely deleted.
Rewriting and cleaning history is possible, but please consider commits as something that has happened in the past.
We can of course revert our mistakes from the past, but that doesn't rewrite the past.

Git has the concept of a currently active commit in a repo, somewhat like a focus or cursor.
This is called _HEAD_ in Git terminology.
HEAD is always the parent commit for the next commit you will create, and after a commit has been created, it becomes HEAD.
So HEAD advances or follows your work progress and normally doesn't have to be set explicitly, resulting in commit sequences.
Setting HEAD to an older commit and creating a new one results in a diverging history.
HEAD is needed more frequently in command line usage.

On the command line, use the `git log <id>` command to list commits, and `git show <id>` to view exactly one commit.
These commands can be seen as query tools for selecting and subsetting commits and the displayed information of each commit.
See [Viewing the Commit History] for a short summary of using `git log` and its options.
If no ID is given, HEAD is used by both commands.
Try e.g. `git log --stat` or `git log --oneline` to customize your log output, or `git log <file/path>` to subset the commits affecting a given file or folder.

## Interpreting Changesets

How can the changes introduced by a commit be inspected?
Being tool agnostic, Git doesn't track what the commit authors did in their productivity tools.
In order to report which changes were introduced by a commit, Git compares the two project states
associated with the commit and its parent, and represents the difference in a standardized output format as changes which affect files and file content.
The format is just a representation or interchange format to communicate changes, not the internal storage format used by Git.

File changes
: Files in the project can be added, modified, or deleted.

Text file content changes
: Git uses a textual representation of text changes by default, the [unified diff][diff] format
Lines within text files can be added or deleted in this diff format.
Diff prepends added and removed lines with plus and minus signs.
Modified lines are composed of a deletion and an addition.

Binary file content changes
: For binary files like PDF, images, or Office files, Git unfortunately doesn't have a representation of file content changes.

Please use `git show <id>` to output the diff of a given commit.
In Git Tower, file changes are displayed next to the selected commit in the history.
Files can be expanded to show their diff.

Graphical (diff) tools for comparing file content side by side can integrate with Git,
but this course concentrates on the traditional textual diff.
IDE programs like VS Code or IntelliJ also contain their own graphical diff tool.
You'll find many online resources about diff tools and Git.

## Navigating the tree: Tags and branches

When the history grows and diverges, the user can quickly get lost in the tree graph.
Switching between parallel paths requires the user to find the leaf of the target path.
Setting HEAD back to an older commit in order to diverge requires the user to find the correct commit again and again.
In a graphical tool, this would be possible by visual inspection, but cumbersome.
On the command line, working productively would be nearly impossible, because you would always have to query and enter the alphanumeric commit IDs.
Git was originally developed for the command line, so a second important layer has been built into Git for navigational purposes: tags and branches.
These behave like bookmarks that are created by the user to label important commits in the graph with a meaningful name.
Both can be passed to the Git commands in place of the commit IDs.
In fact, they are landmarks for the history graph.
Users normally work with this layer, and rarely have to directly work with the raw IDs.
It is much more common to modify and delete these landmarks than it is for commits, like you can change your opinion about the importance of historical events.

### Branches

Branches are made to label the leaf commit of tree paths.
We have already seen the advancing nature of HEAD.
HEAD denominates the current commit and is advanced by creating a new commit.
This advancing behavior is the outstanding feature of branches.
Creating new commits advances the currently active branch.
If you switch to another branch, the last branch's position is remembered and can be restored by switching back.
Technically, HEAD is also a special branch that normally points to a certain branch.

Please use the `git branch` command to list the branches in the local repo.
Then run `git log <branch>` or `git show <branch>` to inspect the history via branches.

On Gitlab, please visit the [example project branches](https://vhrz1125.hrz.uni-marburg.de/tcook/empirical-round/-/branches).
Tower displays the branches of a repo in the Branches section of the sidebar.
You can linearly follow a branch to the root, and get the impression of separate contexts.
An important detail to note is that the History view contains no commits that are not reachable by a branch or tag.

Please don't be confused by some branches appearing also in the Remotes section of Tower's sidebar.
Due to its distributed architecture, Git distinguishes branches into local and remote branches.
The local branches are the working branches as described above, remote branches reflect the current branch state of the respective remote.
Git doesn't share all local branches automatically, but a local branch is deliberately shared by the user.
Sharing a local branch pushes all reachable commits from this branch and sets the remote branch to the local branch's leaf commit.
A common workflow is creating many local branches that will never be shared.
This enables users to distinguish between their personal working branches and those in a central collaboration repo.

### Tags

Tags are labels for important commits in the graph.
They exist in two implementation flavors:

Lightweight tags
: Like branches, these have a name and store the ID of a commit, and they can be easily created and deleted.
They are useful as temporary labels to simplify the workflow, and they are deliberately uploaded by the user.

Annotated tags
: These are exactly like commits without change data.
They become part of the commit graph, have a parent commit, and metadata and messages,
so they should be used to create more final landmarks in the graph that will never change.
Releases are a typical example for annotated tags.

Please run the `git tag` command to list the local tags in the repo, or `git tag tag_name` to create a new tag.
In Git Tower, tags are shown in the sidebar below the branches.

## Comparing commits and branches

Git can compare two arbitrary commits referenced by ID, branch or tag with `git diff x..y`.
The result is reported in the [diff] format like with `git show` and contains the changes that _y_ would apply to _x_.
You can also use `git log x..y` to report commits in _y_ that _x_ doesn't contain.

[history page](https://vhrz1125.hrz.uni-marburg.de/tcook/empirical-round/-/commits/main/)
[diff]: https://en.wikipedia.org/wiki/Diff#Unified_format
[viewing the commit history]: https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History