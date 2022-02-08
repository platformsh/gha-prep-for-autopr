# Prep for Auto Pull Request

In order for your github repository to support auto-merging of pull requests there are several settings that need to be
need to be set-up/enabled. This action sets up all those items, and adds `platformsh` (the check created when you set up
a github integration for your platform.sh project) as a required check on your default branch.

## Inputs

* `repo-owner` - Owner/namespace of the target repository. Defaults to `github.repository_owner` from
  the [github context](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context).
* `repo-name` - Target repository name. Defaults to `github.event.repository.name` from the from
  the [github context](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context).
* `github-token` -
  Github [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
  with access rights to the target repository so we can work with the github api. **REQUIRED**.
* `status-checks` - Comma seperated list of status checks that must pass for the PR to be accepted. Defaults to 
  `platformsh,TestPrEnvironment`. You do not need to include `platformsh` as it will **always** be included. If you do 
   not have a `TestPrEnvironment` check, and want to remove it, pass in an empty string for this input.
## Outputs

* `default-branch` - The default branch for the repository.

## Example usage:

See `Prep the repo for autoPR` step below.

```yaml
name: Trigger Auto PR on push to update branch
on:
    push:
        branches:
            - update

env:
    GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}

jobs:
    create-auto-pr:
        name: "Creates an auto merging PR when the branch is updated"
        runs-on: ubuntu-latest
        steps:
            - name: 'Prep the repo for autoPR'
              id: prepautopr
              uses: platformsh/prep-for-autopr@main
              with:
                github-token: ${{ secrets.GITHUB_TOKEN }}
            - name: 'Create & merge PR'
              id: create-merge-pr
              uses: platformsh/create-pr@main
              with:
                  github-token: ${{ secrets.GITHUB_TOKEN }}
                  trigger-source: 'auto push'
                  default-branch: ${{ steps.prepautopr.outputs.default-branch }}
```
