# flutter-git-worktree-precommit-minimal-repo

A new Flutter project.
This was used to reproduce a strange case of pre-commit hooks failing to work.
The specific combination of features is `flutter`, `pre-commit`, and `git-worktree`.

## To reproduce:

- Check out this repository
- Install pre-commit (https://pre-commit.com/)
- Install the hooks in this repo (`pre-commit install`)
- Install `flutter` through `git` (https://docs.flutter.dev/get-started/install/linux#install-flutter-manually). I used the specific command `git clone https://github.com/flutter/flutter.git -b beta` to check out the beta branch.
- Add the `bin` directory of `flutter` to your `PATH`
- Verify that everything works by making a dummy change in the checked-out repo and committing. (If things fail, try committing again and see if that passes.)
- Make a git-worktree branch as a sibling of the main repo (`git worktree add ../sample-for-pre-commit`) and `cd` there
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

The flutter version information might also display as
```
flutter-version..........................................................Passed
- hook id: flutter-version
- duration: 29.98s

Flutter • channel unknown • unknown source
Framework • revision 951e00f39e (7 minutes ago) • 2023-01-06 10:46:25 -0600
Engine • revision 7b8906637f
Tools • Dart 2.19.0 (build 2.19.0-444.3.beta) • DevTools 2.20.0

flutter-channel-list.....................................................Passed
- hook id: flutter-channel-list
- duration: 0.18s

Flutter channels:
  main
  pre-commit-in-worktree

Currently not on an official channel.
```

I am not sure whether this is an issue with `pre-commit` or `flutter` that causes
git-worktree information to be picked up instead of flutter information.

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

Trying this outside of `pre-commit` also works fine:
```
pre-commit-in-worktree on  pre-commit-in-worktree is 📦 v1.0.0+1 via 🎯 took 27s 
❯ flutter --version
Flutter 3.7.0-1.3.pre • channel beta • https://github.com/flutter/flutter.git
Framework • revision 9b4416aaa7 (2 days ago) • 2023-01-04 17:29:34 -0600
Engine • revision 7b8906637f
Tools • Dart 2.19.0 (build 2.19.0-444.3.beta) • DevTools 2.20.0
```


## Links for setting up this repro repo

- https://docs.flutter.dev/development/tools/sdk/releases?tab=linux
- https://docs.flutter.dev/get-started/test-drive?tab=terminal
- https://pre-commit.com/
