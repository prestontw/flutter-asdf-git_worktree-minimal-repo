# git worktree and git hooks minimal reproducing repo

This was used to reproduce a strange case of pre-commit hooks failing to work.
The specific combination of features is `flutter`, `pre-commit`, and `git-worktree`.

Note: the issue is actually entirely within git. Using

```bash
#!/usr/bin/env bash

(cd $HOME/development/flutter && git -c log.showSignature=false log -n 1 --pretty=format:%H)
```

we are able to do this entirely with git. Flutter is just an example of a project that is a git repo.
TODO: test this with another project that isn't a git repo.

## To reproduce:

- Check out this repository
- Edit `$HOME/development/flutter` to a location that points contains a local git repository
- Install the hooks in this repo (`cp pre-commit .git/hooks/pre-commit`)
- Verify that everything works by making a dummy change in the checked-out repo and committing. The expected output should be the last commit in the pointed to git repo in the `pre-commit` file.
- Make a git-worktree branch as a sibling of the main repo (`git worktree add ../pre-commit-in-worktree`) and `cd` there
- Make a dummy change and commit it---watch the pre-commit output and see that it reports the last commit of the branch we are on, not the repository the `pre-commit` file points to:

```
flutter-pre-commit-git_worktree-minimal-repo on  main [⇡]
❯ vim pre-commit

flutter-pre-commit-git_worktree-minimal-repo on  main [!⇡]
❯ cat pre-commit
#!/usr/bin/env bash

# This doesn't need to be `flutter`, just a location that is a git repository.
(cd $HOME/git/advent-of-code-2020 && git -c log.showSignature=false log -n 1 --pretty=format:%H)

flutter-pre-commit-git_worktree-minimal-repo on  main [!⇡]
❯ cp pre-commit .git/hooks/pre-commit
```
