# Concept

Large files commited to Git repositories must be stored using Git LFS.

Git LFS allows to checkout a lightweight copy of the repository with the large files replaced with plain **text pointers**.

All binaries, pdf's, arxml's, etc. are subject to migration to Git LFS pointers.

Git LFS tracks only those files that match the patterns specified at the **.gitattributes** files, usually located at the root of the repository.

# Git LFS Installations

Please follow the [instructions](https://git-lfs.com) to install Git LFS binaries locally.

**If you aren't going to use large files themselves, you may skip the installation.**

In this case "git checkout" command, which is also usually a part of "git clone", won't fetch large file contents, only the respective text pointers.

# Migration to Git LFS

Git LFS is usually introduced by re-writing the Git history:
* Enable tracking of intrested files.
* Re-write Git history of existing large files that match the pattern.
* Force push all new refs to the remote.

The alternative method (when Git history is preserved) is not recommended, since all large files added before the migration will still always be checked out as a part of the respective commits in Git history.

## Track Large Files

File patterns to be tracked by Git LFS are defined at [**.gitattributes**](https://www.git-scm.com/docs/gitattributes).

Add them by directly editing **.gitattributes** file or using ["git lfs track" command](https://github.com/git-lfs/git-lfs/blob/main/docs/man/git-lfs-track.adoc):
```
git lfs track '*.pdf'
git lfs track 'stubs/*/sources/*'
git lfs track --filename 'image.cpio.gz'
```

Once tracking is set for a particular file, it will be stored as a text pointer in all successive commits. See [Re-write Git History](#re-write-git-history) to re-create all previous commits.

## Re-write Git History

Git history migration may be necessary if you want to use Git LFS with a file which already presents in the Git history.

[**Import** mode](https://github.com/git-lfs/git-lfs/blob/main/docs/man/git-lfs-migrate.adoc#import) of "git lfs migrate import" command converts file objects to LFS pointers by re-creating Git history starting from the point where an interested file was added.

### Migrate using .gitattributes

If you already have a pre-configured .gitattributes file, commit it to the main branch and rebase this commit to the very beginning of Git history. Then run the migrate command:

```
git add .gitattribbutes
git rebase 
git lfs migrate import --everything --include='a/*/b/**'
```

Use **--everything** to migrate the entire repository, i.e. all local and remote branches.

Note that **--include** is very important here. If you 

### Migrate using 

```
git lfs migrate import --include-ref=feature/999-some-fearure-branch
```

Use **--include-ref** to migrate only specific branch, e.g. your feature branch.

## Remove files from Git LFS

Stop tracking files using ["git lfs untrack" command](https://github.com/git-lfs/git-lfs/blob/main/docs/man/git-lfs-untrack.adoc):
```
git lfs untrack '*.pdf'
```
Then update index to restore files to the regular state:
```
git add --renormalize .
```
