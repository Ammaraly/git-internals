# GIT Internals ⚙️
Understanding gow git works under the hood.

## 1. Git Objects
There are three types of objects in `.git` directory:
1. BLOB
   1.  Contents of a file 
   2.  Has no metadata
   3.  Identified by SHA-1 hash
2. TREE
   1. A directory listing of blobs and other trees.
   2. Also identified by SHA-1 hash
3. COMMIT
   1. A snapshot of the currently working tree
   2. Contain
      1. Pointer to the root tree
      2. Committer 
      3. Commit message
      4. Commit time
      5. Pointer to parent commit(s)
   3. Also identified by SHA-1 hash

## 2. Branches
A branch is just a named reference to a commit. `HEAD` is a special pointer that points to the current branch. When we create a new `commit`, the commit is added in the active `branch` to which `HEAD` is pointing to, but `HEAD` does not directly point to the commit, it points to the `branch` which in turn points to the `commit`. Note that with a new commit a new snapshot is created with new `BLOBS` added for modified files.
e.g
```
HEAD --> branch_1 --> commit 0898hb --> tree 076589 --> [BLOBS, TREES]
```
## 3. Deep Dive
Creating git repo without using git porcelain commands *(notes at the bottom)*:
```shell
mkdir new_repo
cd new repo

mkdir .git
mkdir .git/objects
mkdir .git/refs
mkdir .git/refs/heads

echo ref: refs/heads/master > .git/HEAD

Brief channel is awesome | git hash-object --stdin -w
git cat-file -t <hash-we-got-from-last-object-creation>
git cat-file -p <hash-we-got-from-last-object-creation>

git update-index --add --cacheinfo 100644 <hash-we-got-from-last-object-creation> brief.txt
git status

git cat-file -p <hash-we-got-from-last-object-creation> > brief.txt
git status

git write-tree
git cat-file -t <hash-we-got-from-last-tree-creation>
git cat-file -p <hash-we-got-from-last-tree-creation>

git commit-tree <hash-we-got-from-last-tree-creation> -m "Initial commit"
git cat-file -t <hash-we-got-from-last-commit-creation>
git cat-file -p <hash-we-got-from-last-commit-creation>

echo <hash-we-got-from-last-commit-creation> > .git/refs/heads/master

echo <hash-we-got-from-last-commit-creation> > .git/refs/heads/new-branch-at-same-commit

echo ref: refs/heads/new-branch-at-same-commit > .git/HEAD
git status

```
A git repository is basically:
- A collection of objects
- A system for naming those objects, called refs (references)
  
There are two types of git commands:
- Porcelain commands (user friendly interface)
- Plumbing commands (internal commands only to be used when shit goes south) 