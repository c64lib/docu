---
layout: page
title: Contribute
order: 90
nav: true
description: How to contribute.
---
In this document we specify rules that must be obeyed when working with `c64lib` repositories. These rules are intended to make our lives easier, not vice versa. These rules are valid for all repositories unless explicitly overridden.

## Master branch
Master branch is the most important branch for each library. Based on this branch new releases are created. Therefore master branch is always protected - it cannot be deleted or pushed with force option.

The general rule is that we do not work directly on master branch - for each feature or bug a dedicated feature branch must be created. When work is done and tested, a pull request to master should be created and reviewed. Once it is done, feature branch should be rebased on master, squashed if needed and merged with fast forward option so that we get linear history on master branch.

## Develop branch
Develop branch is an optional branch that could be used for developing new version of library. When this option is chosen (it should be chosen for complex releases, for simple tasks we probably don't need it at all), all feature branches that are indented to be in this forthcoming release should be started from develop branch and should be merged (with rebase and fast forward, as mentioned earlier) to develop branch. When release is ready, develop branch should be rebased and merged with fast forward option to the master branch.

## Feature branch
For each feature or bugfix being developed a feature branch should be created. It should start from either master or develop branch (if develop branch exists) and each piece of development should be committed and pushed there.

For each feature or branch there should be an issue existing in one of the projects associated with given repository. Each issue has ID that is displayed as #<number> (i.e.: #10). Each feature branch name should follow that ID in following form: `<ID>-<number>` (i.e.: `CPR-10`).

Each repository has following ID assigned:
* `CMN` - c64lib/common
* `CHIP` - c64lib/chipset
* `TEXT` - c64lib/text
* `CPR` - c64lib/copper64

## Deleting branches
Feature branches should be deleted after merging. One can easily do it using GitHub GUI or alternatively using command line:

    git push origin -d <branch name>

Develop branch can also be deleted or reset to latest master commit and pushed with force option.

## Library versioning
TBD
