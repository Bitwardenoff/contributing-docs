---
sidebar_custom_props:
  access: bitwarden
---

# Branching

## Naming Convention

To keep branches organized we adhere to a naming convention for branches.

This naming convention allows us to easily identify the type of work being done on a branch and will
assist in identifying and tracking down stale branches.

### Branches for a specific Jira issue

In order to link the work on a branch to our Jira issues, the branch name should be made up of three
parts, separated by a slash:

- The team name or abbreviation (e.g. `vault`), and
- The Jira issue tag (e.g. `pm-1234`)
- A short description of the work being done (e.g. `update-csp-hashes`)

In this example, the full branch name would be `vault/pm-1234/update-csp-hashes`.

- Use only lower case letters in branch names.
- Separate words with `-` and branch name sections with `/`. Only use these characters for word or
  section separation.
- Limit work description section to ~50 characters. Overall branch name should be a maximum of ~80
  characters.
- Team names must be consistent. Either always abbreviate or do not abbreviate.

### Branches for multiple Jira issues

If the branch will contain work from multiple Jira issues (most likely due to it being a
[Long-Lived Feature Branch](#long-lived-feature-branch)), the name should be a descriptive name of
the feature, separated by dashes (e.g. `my-long-lived-feature`). Consider brevity when possible, as
our QA team will need to use this branch name when performing QA testing on the feature.

## Branching for Development

The branching strategy for development depends upon whether the work will be managed using what we
have termed a "Long-Lived Feature Branch" or not.

:::note

We use the term "feature" in this documentation to refer to any change to the codebase, whether it
be for a new set of functionality, a bug fix, or refactoring existing code. It is not meant to
exclude types of development that are not "features" per se.

:::

### Which Branching Model to Choose?

The choice of development branching model depends on how you plan to handle the **entire testable,
releasable feature**. As such, it should be planned in advance when beginning work on a new set of
Jira stories or tasks, and it requires coordination across the entire team - Development, QA, and
Product.

Choose a **Long-Lived Feature Branch** if the feature will:

- Require multiple Pull Requests to produce a testable, releasable change, and
- It is not possible to put the changes behind a feature flag

Choose a **Short-Lived Feature Branch** if the feature will:

- Require a single Pull Request to produce a testable, releasable change, or
- It is possible to put the changes behind a feature flag while multiple PRs are in flight.

:::tip Still Unsure?

If in doubt, lean toward creating shorter-lived feature branches directly off of `master` for each
developer’s work, as there is overhead built in to the Long-Lived Feature Branch.

:::

### Long-Lived Feature Branch

A Long-Lived Feature Branch is necessary when the body of work to produce the smallest independent
testable, releasable change is too large to be encapsulated in a single PR, or it requires the
contribution of multiple developers.

The Long-Lived Feature Branch should **only** consist of:

- PRs for approved changes from individual contributions to the feature, and
- Merge commits from `master`

Any other commits directly to the Long-Lived Feature Branch will complicate the eventual review of
the final PR into `master` and should be avoided.

#### Development

To begin development on a feature with a Long-Lived Feature branch, one contributor should create
the Long-Lived Feature Branch for the piece of functionality and create a **draft** PR from that
branch into `master`. This name should include the Jira Epic name if applicable.

Each developer should then branch off of that feature branch, creating an "Issue Branch" named with
the initiating Jira issue, as described in
[branches for a specific Jira issue](#branches-for-a-specific-jira-issue). We call this an Issue
Branch because Jira refers to stories and tasks as issues and to differentiate from the Long-Lived
Feature Branch above.

:::note

It is important to note that we have decided in this scenario that each of these Issue Branches
cannot (or will not) be independently tested or released. We have introduced an intermediate Feature
Branch to collect all of these related changes to allow testing and release as a whole.

:::

As each developer finishes their work, they should open a PR into the Long-Lived Feature Branch. It
is imperative that every change to the Long-Lived Feature Branch have an approved PR, as otherwise
all of these individual commits will need to be reviewed prior to the final merge to `master`. The
developer should tag the appropriate development group to review the PR.

When a developer approves the PR, the PR should be completed, merging that piece of the overall
change into the Long-Lived Feature Branch.

When all Issue Branches are merged, the `needs-qa` label should be applied to the Long-Lived Feature
Branch.

#### Syncing the Long-Lived Feature Branch

We typically denote one person as being responsible for keeping the Long-Lived Feature Branch up to
date. In most cases this will be the Tech Lead of the team but can be anyone. This person is
responsible for keeping the feature branch reasonably up to date with master, preparing it for merge
and more.

Due to how GitHub handles reviews, this person cannot also approve the final PR for the Long-Lived
Feature Branch. The reviewer can be anyone in the team. However, since the work this branch is
typically larger, some seniority with the codebase can be beneficial.

#### QA

The QA team tests on the Long-Lived Feature Branch (note that QA comes **after** the PR is
completed). This is because the premise of this flow is that the individual parts of the feature
cannot be tested individually.

If QA finds a defect, it should be fixed in another Issue Branch off of the Long-Lived Feature
Branch, with another reviewed PR into the Long-Lived Feature Branch to address the problem.

#### Final Review

Since each Pull Request has already been reviewed before being merged into the Long-Lived Feature
Branch, the feature branch review is more of a sanity check. The reviewer should ensure the
following:

- The feature branch is ready to be merged into `master`.
  - Ensure the work has been QA tested, including writing QA Notes in Jira should they be needed, or
  - Verify the feature is behind a feature flag, and the crossover boundaries go through testing.
- Review any non-reviewed commits made directly on the branch. These can be either feature work or
  merge commits.

:::warning

Since feature branches do not have the same protections as master, it's technically possible to
commit directly to the branch or merge a pull request without a up-to-date review. However this
should be discouraged, and should be avoided whenever possible, the only exception being merge
commits.

:::

When all development and functional testing is complete on the Feature Branch, the original PR into
`master` should be moved out of Draft status and tagged with the appropriate development group for
review.

The final review can be performed using GitHub's UI, by opening the Pull Request and clicking on the
`Commits` tab. Each commit can then be checked individually.

- Verify the commit has an existing review by following the Pull Request link and verifying the
  commit SHA hash matches. Look for `Author merged commit {hash} into branch`.
- If not perform a regular code review of the commit.

Merge commits should be reviewed as well, the GitHub UI will automatically simplify the merge commit
and only show the changes made. If reviewing from the command line or through a different tool,
please use the command `git show <hash>`. For some background and more information please read
[How to review a merge commit](https://haacked.com/archive/2014/02/21/reviewing-merge-commits/).

### Short-Lived Feature Branch

A Short-Lived Feature Branch is a good fit for bodies of work that:

- Can be developed by a single contributor
- Can be reviewed in a single Pull Request
- Can be tested independently, and
- Can be released independently

This will often be the case for small pieces of new functionality and for most bug fixes.

#### Development

The developer should create a branch named with the initiating Jira issue (e.g. `PM-1234`) and
create a **draft** PR from that branch into `master`.

:::note

The branch name should be as short as possible, preferably just the issue name. This makes it easier
for the QA team to switch environments to individual branches, as they have to type in the branch
name multiple times when doing so. This is especially important for Short-Lived Feature Branches, in
which testing may be much briefer.

:::

During development, the `master` branch should be regularly merged into the branch to avoid
conflicts.

When development is complete, the developer should prepare the PR for review:

- Remove the Draft status from the PR
- Add the `needs-qa` label to the PR
- Tag the appropriate development group for review and `@dept-design` if design approval is required

When a team member approves the PR, it should **remain open** to be tested. This is critical, as
completing the PR at this point would introduce un-tested changes into `master`.

#### QA

The QA team should test on the Short-Lived Feature Branch. If any defects are found, they should be
addressed with commits directly to the Short-Lived Feature Branch, triggering a re-review from the
developer's team prior to re-introducing the new changes to QA.

After QA has tested the feature and the developer has addressed any defects, the PR owner should
complete the PR and merge the changes into the `master` branch.

## Branching for Release

On the first business day after the Development Complete date for a given release, an `rc` branch is
created off of `master`. This is a snapshot of the ongoing work in `master` that will represent the
code released in the upcoming version.

The `rc` branch is used for regression testing. It is then used as the source for the production
release and deployment of each of our deployed entities. When each release is made, a tag of the
format `vYYYY.MM.#-{component}` is created. This can be used to hotfix this release at a later
point.

When the release is complete, the `rc` branch is deleted.

### Hotfix Releases

For a hotfix release, a hotfix branch is created off of the release tag for the release upon which
the hotfix should be applied. The branch naming depends upon the repository. For all repositories
_except_ `clients`, the branch name is `hotfix-rc`. However, as we can release individual clients
separately, each client in the `clients` repo has their own named hotfix branch:

- Web: `hofix-rc-web`
- Desktop: `hotfix-rc-desktop`
- Browser: `hotfix-rc-browser`
- CLI: `hotfix-rc-cli`

Once the hotfix branch has been created, the individual commits in `master` are cherry-picked into
the hotfix branches. For a client fix, this may require cherry-picking to multiple hotfix branches.

Once the hotfix has been deployed, the hotfix branches are deleted.

:::info Hotfix QA Testing

For hotfixes, we do not perform QA testing on the feature branch prior to merging into `master`.
This is an acknowledged risk that we have incurred in order to speed up the hotfix process and to
avoid having to switch all of our QA testing environments from referencing our hotfix branches.

Instead, hotfixed changes are merged into into `master` as soon as the PR is approved and then
cherry-picked to the appropriate hotfix branch(es). They are then tested on the hotfix branch.

:::
