# Break Out Specific Files/Directories to a New Git Repository

Say you have a repository that has a directory/file/component inside it which
has grown to such a size that it would be better to manage it as a separate
repository. However, you don't just want to copy the relevant files into a new
repo and lose all the important commit history, so you need some way to include
that in the move as well.

If your current project is so well structured that you only need to "extract" a
single folder, then there exist a simpler and cleaner guide [here][1] with
some good surrounding comments. However, if you (like me) notice that the
files you are interested in have been moved/renamed a couple of times during
the development, the "easy" method may not be able to find all the important
history. The method I am about to describe will therefore be a little bit more
advanced than what most people need, but sometimes there is no way around it.

1. [Finding Only the Relevant Files/Directories](#finding-only-the-relevant-filesdirectories)
2. [Extracting the Relevant Commits](#extracting-the-relevant-commits)
    - [Recommended Preparations](#recommended-preparations)
    - [Filter the Relevant Commits](#filter-the-relevant-commits)
3. [Import Into New Repository](#import-into-new-repository)
    - [Import Into Completely Empty Repository](#import-into-completely-empty-repository)
    - [Import Into Repository with Initial Commit](#import-into-repository-with-initial-commit)


## Finding Only the Relevant Files/Directories
First you need to do the manual job of finding out all the different possible
paths/names the files of interest have used, and then understand how a `grep`
command is best created to single out these paths. The program [`gitk`][5] has
a GUI which may be used for easier visualization and traversing of the current
commit history.

However, we actually want to create a `grep` command that finds all files which
are **NOT** those we want to keep. This is because we will delete anything that
we do not want to save, and thus only the important files/commits will remain.
To our help we will use the `-v` flag, which [means][2] "invert the sense of
matching, to select non-matching lines".

> NOTE: We will later also be using the `-z` flag for `grep`, which will allow
        it to properly handle filepaths with spaces. However, this flag is
        omitted in these examples for brevity.

#### Example 1:
We have a large repository where one file has been moved around to the
following different paths.

1. `/path1/file.txt`
2. `/path2/subdir/file.txt`
3. `/path3/file.txt`

We want to delete all files which are not it, so it is easy to see that the
command

```bash
grep -v "/file.txt"
```

would return all filepaths that are not part of the ones of interest. Of course
this requires that there are no other files named `file.txt` in the repository
that might create a conflicting match.

#### Example 2:
We have a large repository where one file has been renamed a couple of times.

1. `/path1/file.txt`
2. `/path2/subdir/same_file.txt`
3. `/path3/still_same_file.txt`

Here it is a little bit more difficult, but we can do it by piping multiple
inverted `grep` searches into each other. So in the following command

```bash
grep -v "/file.txt" | grep -v "/same_file.txt" | grep -v "/still_same_file.txt"
```

we slowly filter out the list of filepaths to **not** include any of the
possible variations of the filename of interest. Once again this method require
that there aren't any other files with a conflicting name. But in that case it
is not difficult to be even more specific and use the whole filepath when
filtering.

#### Example 3:
We have an entire directory that has been renamed.

1.  ```
    . directory_1/
    ├── file1.txt
    └── file2.txt
    ```
2.  ```
    . directory_2/
    ├── file1.txt
    └── file2.txt
    ```

If we want to keep the history of all the files within those folders we only
need to define those folder names in the command.

```bash
grep -v "/directory_1" | grep -v "/directory_2"
```

All the files contained therein will be included, since the `grep` command
will match all files that has the directory name in their path.


## Extracting the Relevant Commits
When we know how to properly filter out all the filepaths we want to **keep**
it is time to run the filtering command in the git repository.

> NOTE: Most of these commands are taken straight from this guide [here][3],
        but I have added some more comments.

### Recommended Preparations
I strongly suggest you clone the source repository again so it is easy to revert
in the case you mess up. So you could either use git to clone a repository that
is already present on your computer

```bash
git clone file:///path/to/original_repo repo_clone
```

or you could just do a simple copy the entire folder

```bash
cp -a original_repo repo_clone
```

When inside the new `repo_clone` I also recommend you remove the "origin"
so you can not accidentally push any changes to the remote repository.

```bash
git remote rm origin
```

Then I suggest that you create new branch for easy referencing later.


```bash
git checkout -b "feature_extraction"
```

### Filter the Relevant Commits
The following command will go through all commits made in the history of the
project, and at every point it will delete all files that have not been excluded
by our inverted `grep` expressions. With the `--prune-empty` flag we also tell
git to not include any commits that would just be empty now when we delete all
other files. Thus we will end up with the commits that only involves our files
of interest in some way.

```bash
git filter-branch --prune-empty --index-filter \
        'git ls-tree -z -r --name-only --full-tree $GIT_COMMIT | \
        grep -z -v "/directory_1" | \
        grep -z -v "/directory_2" | \
        xargs -0 -r git rm --cached -rf' \
    -- --all
```

In the above command you may add or remove as many lines of the inverted `grep`
commands as you need. Some trial and error might be necessary, but `gitk` is
usually quite nice to use to easily navigate the newly created commit history.


## Import Into New Repository
When we have managed to scrub everything uninteresting from the
"feature_extraction" branch in `repo_clone` we are now ready to import it
into the new standalone repository. However, the process is a little different
regarding if you start a
[completely empty repository](#import-into-completely-empty-repository)
or if you already have one
[with an initial commit](#import-into-repository-with-initial-commit)
(e.g. initialized with README on GitHub and then cloned to the local computer).
Go to the subsection here which best describes your current situation.

### Import Into Completely Empty Repository
It is possible to initialize a git repository without any commits, which makes
importing the "feature_extraction" branch much simpler than in the case when
we already have [an initial commit](#import-into-repository-with-initial-commit)
present.

Either clone a completely empty repository from the remote location

```bash
git clone git@github.com:<user>/empty_repo.git new_repo && cd new_repo
```

or just create a new folder, and initialize a git repository inside it.

```bash
mkdir new_repo && cd new_repo
git init
```

Then it is easy to import the branch from the other repository.

```bash
git pull ../repo_clone feature_extraction
```

Now you will have a clean history which only includes the commits from the
previous repository that in some way changes the files of interest.


### Import Into Repository with Initial Commit
If you already have a repository on e.g. GitHub, which you initialized with a
README or similar, you should first clone it to your local computer and then
move into the new folder.

```bash
git clone git@github.com:<user>/initialized_repo.git new_repo && cd new_repo
```

Now we need to import the "feature_extraction" branch into this repository.
However, since there already exist an "Initial commit" you could either
[merge](#merge) the new history, or you could [rebase](#rebase) it on top of
the existing one. I think a rebase looks cleaner if you intend to pretend that
this repository was always standalone, while a merge allows you to show in a
more clear way that this is a feature branch extraction from another repository.

#### Merge
In comparison to what we did when we used a
[completely empty repository](#import-into-completely-empty-repository) we now
need to add the [`--allow-unrelated-histories`][4] to be able to successfully
import the new stuff.

```bash
git pull ../repo_clone feature_extraction --allow-unrelated-histories
```

This will then prompt you to create a commit message for the merge being done.
Here you can include a description to why you are doing this and from where
you are importing all these commits. This could help future developers better
understand when and why this was done, so spend some time making this message
good and informative.


#### Rebase
In order to be able to rebase the new commits onto the current repository's
history you will first need to perform the steps explained in the
[merge](#merge) section above. However, you won't need to spend time writing
a proper merge commit message, since you will run the the following command
right afterwards

```bash
git rebase origin/master
```

This will remove the "merge" message, and you may also want to add the `-i`
flag (after the "rebase" word) to be able to see that git is smart enough to
ignore the "Initial commit" from the imported branch. Then it will look like
this repository has a single continuous history from an initial commit that has
a timestamp from the future relative to the first "real" commit.






[1]: https://stackoverflow.com/a/17864475
[2]: https://linux.die.net/man/1/grep
[3]: https://ptc-it.de/move-files-to-new-repo-in-git/#moving-files-and-directories
[4]: https://mkyong.com/git/git-pull-refusing-to-merge-unrelated-histories/
[5]: https://git-scm.com/docs/gitk
