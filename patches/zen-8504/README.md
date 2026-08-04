# ZEN-8504: Catch git failure on permission denied

This directory carries a patch for the [`Zenlytic/metrics_layer`](https://github.com/Zenlytic/metrics_layer)
repository. It is placed here only because the Cursor GitHub App does not have
access to `Zenlytic/metrics_layer`, so the agent could not open a PR there
directly. **Do not merge this PR** — apply the patch to `metrics_layer` and
delete this branch.

## What the patch does

On the Save Model action (and any other git operation), a deploy key without
write access causes GitPython to raise a raw `GitCommandError` that is shown
to the user verbatim:

```
Cmd('git') failed due to: exit code(128)
  cmdline: git push origin pb-test-2
  stderr: 'ERROR: Permission to Zenlytic/dev-demo-data-model.git denied to deploy key
fatal: Could not read from remote repository.
...'
```

The patch:

- Adds `GitError` and `GitPermissionError` to `metrics_layer/core/exceptions.py`
  (subclasses of `MetricsLayerException`).
- In `metrics_layer/core/parse/github_repo.py`, catches `git.GitCommandError`
  around all git operations (`add_commit_and_push`, `create_branch`,
  `delete_branch`, `pull`, `squash_and_merge` via `_ssh_wrapped`, and clone via
  `fetch_github_repo`) and, when the failure is a permission error, raises
  `GitPermissionError` with a user-displayable message such as:

  > Permission denied while running git. Access to the repository
  > Zenlytic/dev-demo-data-model.git was denied to the deploy key. Please make
  > sure the deploy key has write access and the repository exists.

  Non-permission git errors propagate unchanged.
- Adds `tests/test_github_repo.py` with 4 tests reproducing the exact stderr
  from the issue, the HTTPS 403 variant, a non-permission error (not
  translated), and an end-to-end `add_commit_and_push` failure.

The app's Save Model handler should catch `GitPermissionError` (or
`MetricsLayerException`) and display `str(e)` to the user.

## How to apply

```bash
cd metrics_layer
git checkout -b catch-git-permission-denied
git am path/to/0001-Catch-git-permission-denied-errors-and-raise-a-user-.patch
python -m pytest tests/test_github_repo.py -v
```
