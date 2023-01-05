# sample_for_pubspec

A new Flutter project.
This was used to reproduce a strange case of pre-commit hooks failing to work.
The specific combination of features is `asdf`, `flutter`, `pre-commit`, and `git-worktree`.
Not using `asdf` caused pre-commit to work fine.

## To reproduce:

- Check out this repository
- Install pre-commit (https://pre-commit.com/)
- Install the hooks in this repo (`pre-commit install`)
- Install `asdf` (https://asdf-vm.com/guide/getting-started.html)
- Install `asdf-flutter` plugin (https://asdf-vm.com/guide/getting-started.html#_4-install-a-plugin (`asdf plugin add flutter`))
- Install version through `asdf`: `asdf install`
- Verify that everything works by making a dummy change and committing. If you run into flutter errors, `cd` to where `flutter` was installed (something like `~/.asdf/installs/flutter/3.3.10-stable` then checkout beta channel with `git checkout beta`)
- Make a git-worktree branch as a sibling of the main repo (`git worktree add sample-for-pre-commit`) and `cd` there
- Make a dummy change and commit it---watch the pre-commit output and how it doesn't know which channel it is on:

```
pre-commit-in-worktree on  pre-commit-in-worktree is 📦 v1.0.0+1 via 🎯 v2.19.0-444.2.beta
❯ vim lib/main.dart # make a simple change

pre-commit-in-worktree on  pre-commit-in-worktree [!] is 📦 v1.0.0+1 via 🎯 v2.19.0-444.2.beta took 6s
❯ git commit -am "Sure enough, this leads to flutter channel weirdness"
Trim Trailing Whitespace.................................................Passed
Fix End of Files.........................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
flutter-analyze..........................................................Passed
- hook id: flutter-analyze
- duration: 14.61s

Building flutter tool...
Analyzing pre-commit-in-worktree...
No issues found! (ran in 1.6s)

flutter-version..........................................................Passed
- hook id: flutter-version
- duration: 1.4s

Flutter • channel pre-commit-in-worktree • git@github.com:prestontw/flutter-asdf-git_worktree-minimal-repo.git
Framework • revision 8e93fdcde9 (22 minutes ago) • 2023-01-05 00:09:28 -0600
Engine • revision 8c99b0feaf
Tools • Dart 2.19.0 (build 2.19.0-444.2.beta) • DevTools 2.20.0

flutter-channel-list.....................................................Passed
- hook id: flutter-channel-list
- duration: 0.25s

Flutter channels:
  main

flutter-test.............................................................Passed
- hook id: flutter-test
- duration: 2.56s

00:00 +0: loading /home/ptw/development/pre-commit-in-worktree/test/widget_test.dart                                                                                                    00:01 +0: loading /home/ptw/development/pre-commit-in-worktree/test/widget_test.dart                                                                                                    00:01 +0: Counter increments smoke test                                                                                                                                                 00:02 +0: Counter increments smoke test                                                                                                                                                 00:02 +1: Counter increments smoke test                                                                                                                                                 00:02 +1: All tests passed!

flutter-version-2........................................................Passed
- hook id: flutter-version-2
- duration: 1.15s

Flutter • channel pre-commit-in-worktree • git@github.com:prestontw/flutter-asdf-git_worktree-minimal-repo.git
Framework • revision 8e93fdcde9 (22 minutes ago) • 2023-01-05 00:09:28 -0600
Engine • revision 8c99b0feaf
Tools • Dart 2.19.0 (build 2.19.0-444.2.beta) • DevTools 2.20.0
```

Note that the Flutter channel is not an official flutter channel but the name of the
git worktree branch we just made (in the above example, it's `pre-commit-in-worktree`).
Also note that the framework revision commit is local to this repo (`8e93fdcde9`).

I think this is an issue with `asdf` because this works fine if I use a local installation
of flutter. And I think this is an issue with `asdf` over `asdf-flutter` since
`asdf-flutter` is mostly concerned with installing and listing versions.

Note that this could also be an issue with `flutter` that `asdf` raises.

For another quick note, this is specific to non-main git worktree folders.
Trying this in the "main" folder works fine:
```
...
Flutter 3.7.0-1.2.pre • channel beta • https://github.com/flutter/flutter.git
Framework • revision c29b09b878 (2 weeks ago) • 2022-12-19 18:30:12 -0600
Engine • revision 8c99b0feaf
Tools • Dart 2.19.0 (build 2.19.0-444.2.beta) • DevTools 2.20.0
...
```


## Links for setting up this repro repo

- https://asdf-vm.com/guide/getting-started.html#_6-set-a-version
- https://dart.dev/get-dart/archive
- https://docs.flutter.dev/development/tools/sdk/releases?tab=linux
- https://docs.flutter.dev/get-started/test-drive?tab=terminal
- https://pre-commit.com/
