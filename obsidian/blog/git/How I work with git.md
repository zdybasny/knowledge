# aliases

<https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases>

## in git cli

## in bash

## commit messages

<https://chiamakaikeanyi.dev/how-to-write-good-git-commit-messages/>
<https://dev.to/mishmanners/how-to-make-a-good-git-commit-1ed7>
<https://initialcommit.com/blog/git-commit-messages-best-practices>
<https://cbea.ms/git-commit/>

> git log (with options)

## merges, squashes, or rebases

### short overview

#### merge

According to the [git documentation](https://git-scm.com/docs/git-merge):

> Incorporates changes from the named commits \[...] into the current branch. This command is used by `git pull` to incorporate changes from another repository.

A branch (let say `main`) in the `git pull origin main` is the source branch to merge into the current branch (let say `feature`) from. When conflicts occurs, there is syntax `git merge --continue` to commit the resolved changes, or `git merge --abort` to reset the uncommitted changes from *incoming*[^1] and local. **Warning:** make sure you have no uncommitted changes before the merge you do not want to loose.

[^1]: **Why incoming, not remote?** Stashes are not on remote nor local.
**So where exactly are stashes stored if not on the local nor the remote?** `./.git/refs/stash`

So what is wrong with the simple and easy merge? It generate a new commit which doesn't provide any meaningful changes. It merges history of both branches only.

The commit is added to the current branch and can be added to the source branch:

`git checkout main`
`git pull origin feature`

### `--ff` or `--no-ff`, or `ff-only`, what is that question?

<https://medium.com/@shubhpaliwal98/when-to-use-squash-merge-and-rebase-43da3571dcbe>

### pros & cons in practice

### GitHub repository settings

### rebase in practice

### When to use?

### rebase/reset

>git pull --rebase

rebase edit/reword: <https://stackoverflow.com/questions/30194796/git-rebase-whats-the-difference-between-edit-and-reword>

## others

### tags

create, push
annotated/unannotated

### autocomplete git cli

### > git restore . > git clean -df

### git rev-parse --abbrev-ref HEAD (get branch)
