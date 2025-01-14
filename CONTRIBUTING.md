# Contributing to Flutter Plugins

[![Build Status](https://api.cirrus-ci.com/github/flutter/plugins.svg)](https://cirrus-ci.com/github/flutter/plugins/master)

_See also: [Flutter's code of conduct](https://github.com/flutter/flutter/blob/master/CODE_OF_CONDUCT.md)_

## Important note

As of January 2021, we are no longer accepting non-critical PRs for plugins
for which there is a corresponding [Flutter Community Plus
Plugin](https://plus.fluttercommunity.dev/), as we hope in time to be able
to transition users to those versions of the plugins. If you have a PR for
something other than a critical issue (crashes, build failures, null safety, etc.)
for any of the following plugins, we encourage you to submit it
[there](https://github.com/fluttercommunity/plus_plugins/pulls) instead:
- `android_alarm_manager`
- `android_intent`
- `battery`
- `connectivity`
- `device_info`
- `package_info`
- `sensors`
- `share`
- `wifi_info_flutter` (corresponds to `network_info_plus`)

## Things you will need

 * Linux, Mac OS X, or Windows.
 * git (used for source version control).
 * An ssh client (used to authenticate with GitHub).

## Getting the code and configuring your environment

 * Ensure all the dependencies described in the previous section are installed.
 * Fork `https://github.com/flutter/plugins` into your own GitHub account. If
   you already have a fork, and are now installing a development environment on
   a new machine, make sure you've updated your fork so that you don't use stale
   configuration options from long ago.
 * If you haven't configured your machine with an SSH key that's known to github, then
   follow [GitHub's directions](https://help.github.com/articles/generating-ssh-keys/)
   to generate an SSH key.
 * `git clone git@github.com:<your_name_here>/plugins.git`
 * `cd plugins`
 * `git remote add upstream git@github.com:flutter/plugins.git` (So that you
   fetch from the master repository, not your clone, when running `git fetch`
   et al.)


## Setting up tools

There are scripts for many common tasks (testing, formatting, etc.) that will likely be useful in preparing a PR.
See [plugin_tools](./script/tool/README.MD) for more details.

## Running the examples

To run an example with a prebuilt binary from the cloud, switch to that
example's directory, run `pub get` to make sure its dependencies have been
downloaded, and use `flutter run`. Make sure you have a device connected over
USB and debugging enabled on that device.

 * `cd packages/battery/example`
 * `flutter run`

## Setting up XCUITests

Sometimes, XCUITests are useful when integration testing a plugin that has native UI on iOS (e.g image_picker, in_app_purchase, camera, share, local_auth etc). Most of the time, XCUITests are not necessary, consider using `integration_test` if the tests are not focused on iOS system UI.

If XCUITests has always been set up for the plugin, a RunnerUITests folder under `<the_plugin>/example/ios` directory can be found.
If XCUITests has not been set up for the plugin, follow these steps to set it up:

1. Open <path_to_plugin>/example/ios/Runner.xcworkspace using XCode.
1. Create a new "UI Testing Bundle".
1. In the target options window, populate details as following, then click on "Finish".
  * In the "product name" field, type in "RunnerUITests".
  * In the "Team" field, select "None".
  * In the Organization Name field, type in "Flutter". This should usually be pre-populated.
  * In the organization identifer field, type in "com.google". This should usually be pre-populated.
  * In the Language field, select "Objective-C".
  * In the Project field, select the xcodeproj "Runner" (blue color).
  * In the Target to be Tested, select xcworkspace "Runner" (white color).
1. A RunnerUITests folder should be created and you can start hacking in `RunnerUITests.m`.
1. To enable the test on CI, the plugin needs to be removed from the "skip" list:
  * Open `./cirrus.yml` and find PLUGINS_TO_SKIP_XCTESTS.
  * Remove the plugin name from the list.

## Running the tests

### Integration tests

To run the integration tests using Flutter driver:

```console
cd example
flutter drive test_driver/<name_of_plugin_test>.dart
```

To run integration tests as instrumentation tests on a local Android device:

```console
cd example
flutter build apk
cd android && ./gradlew -Ptarget=$(pwd)/../test_driver/<name_of_plugin>_test.dart app:connectedAndroidTest
```

These tests may also be in folders just named "test," or have filenames ending
with "e2e".

### Dart unit tests

To run the unit tests:

```console
flutter test test/<name_of_plugin>_test.dart
```

### Java unit tests

These can be ran through Android Studio once the example app is opened as an
Android project.

Without Android Studio, they can be ran through the terminal.

```console
cd example
flutter build apk
cd android
./gradlew test
```

### XCTests (iOS)

XCUnitTests are typically configured to run with cocoapods in this repo. To run all the XCUnitTests for a plugin:

```console
cd ios
pod lib lint --allow-warnings
```

XCUITests aren't usually configured with cocoapods in this repo. They are configured in a xcode workspace target named RunnerUITests.
To run all the XCUITests in a plugin, follow the steps in a regular iOS development workflow [here](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/05-running_tests.html)

For convenience, a [plugin_tools](./script/tool/README.MD) command [xctest](./script/tool/README.MD#run-xctests) could also be used to run all the XCUITests in the repo.

## Contributing code

We gladly accept contributions via GitHub pull requests.

Please peruse our
[style guide](https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo)
before working on anything non-trivial. These guidelines are intended to
keep the code consistent and avoid common pitfalls.

To start working on a patch:

 * `git fetch upstream`
 * `git checkout upstream/master -b <name_of_your_branch>`
 * Hack away.
 * Verify changes with [plugin_tools](./script/tool/README.MD).
```sh
cd script/tool && pub get && cd ../../
dart ./script/tool/lib/src/main.dart format --plugins plugin_name
dart ./script/tool/lib/src/main.dart analyze --plugins plugin_name
dart ./script/tool/lib/src/main.dart test --plugins plugin_name
```
 * `git commit -a -m "<your informative commit message>"`
 * `git push origin <name_of_your_branch>`

To send us a pull request:

* `git pull-request` (if you are using [Hub](http://github.com/github/hub/)) or
  go to `https://github.com/flutter/plugins` and click the
  "Compare & pull request" button

Please make sure all your checkins have detailed commit messages explaining the patch.

Plugins tests are run automatically on contributions using Cirrus CI. However, due to
cost constraints, pull requests from non-committers may not run all the tests
automatically.

Once you've gotten an LGTM from a project maintainer and once your PR has received
the green light from all our automated testing, wait for one the package maintainers
to merge the pull request and `pub submit` any affected packages.

You must complete the
[Contributor License Agreement](https://cla.developers.google.com/clas).
You can do this online, and it only takes a minute.
If you've never submitted code for that plugin before, you may also add your (or
your organization's) name and contact info to the AUTHORS file for the plugin.
You may also add it to the AUTHORS file for [the repository](AUTHORS).

### The review process

Reviewing PRs often requires a non trivial amount of time. We prioritize issues, not PRs, so that we use our maintainers' time in the most impactful way. Issues pertaining to this repository are managed in the [flutter/flutter issue tracker and are labeled with "plugin"](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Areactions-%2B1-desc). Non trivial PRs should have an associated issue that will be used for prioritization. See the [prioritization section](https://github.com/flutter/flutter/wiki/Issue-hygiene#prioritization) in the Flutter wiki to understand how issues are prioritized.

Newly opened PRs first go through initial triage which results in one of:
  * **Merging the PR** - if the PR can be quickly reviewed and looks good.
  * **Closing the PR** - if the PR maintainer decides that the PR should not be merged.
  * **Moving the PR to the backlog** - if the review requires non trivial effort and the issue isn't a priority; in this case the maintainer will:
    * Make sure that the PR has an associated issue labeled with "plugin".
    * Add the "backlog" label to the issue.
    * Leave a comment on the PR explaining that the review is not trivial and that the issue will be looked at according to priority order.
  * **Starting a non trivial review** - if the review requires non trivial effort and the issue is a priority; in this case the maintainer will:
    * Add the "in review" label to the issue.
    * Self assign the PR.

### The release process

We push releases manually. Generally every merged PR upgrades at least one
plugin's `pubspec.yaml`, so also needs to be published as a package release. The
Flutter team member most involved with the PR should be the person responsible
for publishing the package release. In cases where the PR is authored by a
Flutter maintainer, the publisher should probably be the author. In other cases
where the PR is from a contributor, it's up to the reviewing Flutter team member
to publish the release instead.

Some things to keep in mind before publishing the release:

- Has CI ran on the master commit and gone green? Even if CI shows as green on
  the PR it's still possible for it to fail on merge, for multiple reasons.
  There may have been some bug in the merge that introduced new failures. CI
  runs on PRs as it's configured on their branch state, and not on tip of tree.
  CI on PRs also only runs tests for packages that it detects have been directly
  changed, vs running on every single package on master.
- [Publishing is
  forever.](https://dart.dev/tools/pub/publishing#publishing-is-forever)
  Hopefully any bugs or breaking in changes in this PR have already been caught
  in PR review, but now's a second chance to revert before anything goes live.
- "Don't deploy on a Friday." Consider carefully whether or not it's worth
  immediately publishing an update before a stretch of time where you're going
  to be unavailable. There may be bugs with the release or questions about it
  from people that immediately adopt it, and uncovering and resolving those
  support issues will take more time if you're unavailable.

To release a package, a [publish-plugin](./script/tool/README.MD#publish-and-tag-release) tool script should be used. This command publishes the new version to pub.dev, and tags the commit in the format of `<package_name>-v<package_version>` then pushes it to upstream.

Alternatively, one can release a package in the below 2-step process.

1. Push the package update to [pub.dev](https://pub.dev) using `pub publish`.
2. Tag the commit with git in the format of `<package_name>-v<package_version>`,
   and then push the tag to the `flutter/plugins` master branch. This can be
   done manually with `git tag $tagname && git push upstream $tagname` while
   checked out on the commit that updated `version` in `pubspec.yaml`.
