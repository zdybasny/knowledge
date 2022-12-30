
# How I work with git

## aliases

<https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases>

### in git cli

### in bash

## good commit messages

<https://chiamakaikeanyi.dev/how-to-write-good-git-commit-messages/>
<https://dev.to/mishmanners/how-to-make-a-good-git-commit-1ed7>
<https://initialcommit.com/blog/git-commit-messages-best-practices>
<https://cbea.ms/git-commit/>

> git log (with options)


## others



### tags

create, push
annotated/unannotated

### autocomplete git cli

### > git restore . > git clean -df

### git rev-parse --abbrev-ref HEAD (get branch)

### cannot lock ref

```terminal
$ git checkout -b feature/1
fatal: cannot lock ref 'refs/heads/feature/1': 'refs/heads/feature' exists; cannot create 'refs/heads/feature/1'
```

```terminal
$ git branch --delete feature
Deleted branch feature (was 007a048).
```

```terminal
$ git remote prune origin
Pruning origin
URL: https://github.com/zdybasny/git-merges-strategies.git
 * [pruned] origin/feature
```

```terminal
$ git checkout -b feature/1
Switched to a new branch 'feature/1'
```
