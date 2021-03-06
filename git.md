# Git Cheatsheet

> Powerful but confusing.

## Archive

``` bash
git archive --format=tar --prefix=foo_bar-1.0/ v1.0 | xz > foo_bar-1.1.tar.xz
```

## Branching

### Always Collapse to Single Commit

```sh
git checkout --orphan $TEMP_BRANCH
git add -A
git commit -am "Initial commit"
git branch -D master
git branch -m master
git push --force origin master
```

### Delete Remote Branch

```sh
git push origin :the_remote_branch
```

## Changing Author Info

### Whole Repository
Change `OLD_EMAIL`, `CORRECT_NAME` and `CORRECT_EMAIL` to desired values, afterwards run `git push --force --tags origin 'refs/heads/*'`.
``` bash
git filter-branch --env-filter '
OLD_EMAIL="your-old-email@example.com"
CORRECT_NAME="Your Correct Name"
CORRECT_EMAIL="your-correct-email@example.com"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

### One Commit

``` bash
# Checkout the commit we are trying to modify.
git checkout 03f482d6

# Make the author change.
git commit --amend --author "New Author Name <New Author Email>"

# Replace the old commit with the new one locally.
git replace 03f482d6 42627abe

# Rewrite all future commits based on the replacement.
git filter-branch -- --all

# Remove the replacement for cleanliness.
git replace -d 03f482d6

# Push the new history (after sanity checking with git log).
git push -f
```

## Submodules

### Update all Submodules
```bash
git submodule update --recursive --remote
```

## Tags

``` bash
git tag v1.0
git push origin v1.0
```

## Rebasing

### Squash Several Commits Into a Single Commit

```bash
git checkout squashed_feature
git rebase -i development
```

Squash all commits but the first.

```
pick fda59df commit 1
squash x536897 commit 2
squash c01a668 commit 3
```

Write a proper commit message, probably with a Jira ticket number or the like.

Checkout development branch and merge squashed feature branch.

```bash
git checkout development
git merge squashed_feature
```

Newer Git allows you to do this in one step:

```bash
git merge --squash squashed_feature
```
