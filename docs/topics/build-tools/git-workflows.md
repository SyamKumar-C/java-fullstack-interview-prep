# Git Fundamentals and Team Workflows

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Short interview answer
Git is a distributed version-control system. A commit records a snapshot and its parent history. Branches are movable references to commits. A team workflow uses small commits, code review, conflict resolution, and controlled integration into the main branch.

## 2. Working tree, staging and commits
- Working tree: current files.
- Index/staging area: selected changes for the next commit.
- Commit: snapshot plus metadata and parent reference.
- Branch: movable name pointing to a commit.
- Remote: another repository such as origin.

```bash
git status
git add src/
git diff --staged
git commit -m "Add order validation"
git push origin feature/order-validation
```

## 3. Merge vs rebase
Merge combines histories and may create a merge commit. Rebase replays commits on a new base and creates new commit IDs.

```bash
git fetch origin
git merge origin/main
# or, for a private feature branch:
git rebase origin/main
```

Do not rebase shared/public commits unless the team explicitly coordinates a history rewrite. After a rebase, pushing may require a safe force push such as --force-with-lease; never use force blindly.

## 4. Conflict resolution
1. Fetch and integrate the target branch.
2. Open conflicted files and understand both changes.
3. Edit to the intended combined result.
4. Run tests and inspect the diff.
5. Mark resolved and complete merge/rebase.

```bash
git status
git diff
git add path/to/resolved-file
git rebase --continue
```

For a merge, use git commit after staging resolved files.

## 5. Undo operations
- `git restore file`: discard unstaged working-tree changes.
- `git restore --staged file`: unstage a file.
- `git revert <commit>`: create a new commit that reverses an earlier commit.
- `git reset`: move branch/HEAD and can discard local history; use carefully.
- `git reflog`: inspect recent local reference movements.

Prefer revert for changes already shared with a team.

## 6. Practical team workflow
Create a short-lived feature branch, make focused commits, push, open a pull request, run CI, address review, and merge according to team policy. Keep pull requests small enough to review. Never commit secrets, build output, or personal environment files.

## 7. Common mistakes
- Committing directly to protected main.
- Rebasing a branch other people are using.
- Using reset --hard without checking uncommitted work.
- Resolving conflicts by blindly choosing ours/theirs.
- Force-pushing without --force-with-lease.
- Forgetting to fetch before comparing remote changes.

## 8. Interview questions
**Q1. Git merge vs rebase?** Merge combines histories; rebase replays commits on a new base and rewrites commit IDs.

**Q2. revert vs reset?** Revert adds a reversing commit; reset moves a reference and may rewrite/discard local history.

**Q3. What is staging?** Selecting exact changes for the next commit.

**Q4. What is reflog?** A local record of recent reference/HEAD movements useful for recovery.

**Q5. How do you handle a conflict?** Understand both edits, produce the intended combined result, test it, then complete the operation.

## 9. Practice
1. Create a feature branch and make two focused commits.
2. Rebase it on an updated main branch.
3. Create a conflict and resolve it.
4. Explain how to undo a pushed bad commit safely.
