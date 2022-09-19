# Post installation instructions

## Insert workflow automation files to your repo

!!! attention

    To start using gitStream, create the following files in each repo's main branch with the following content.

Get the following files into each of your repos main branch:

1. `.cm/gitstream.cm` 
2. `.github/workflows/gitstream.yml`

#### `.cm/gitstream.cm`
This file should be updated with new rules and automations. 

```yaml title=".cm/gitstream.cm"
--8<-- "docs/assets/gitstream.cm"
```

!!! note

    You can edit later the `.cm/gitstream.cm` and add your own checks and automations.

#### `.github/workflows/gitstream.yml`

This file is used by gitStream to trigger the automation in GitHub, you should not edit it. Editing 
it might break the functionality or might not be compatible with future changes in gitStream. 

```yaml title=".github/workflows/gitstream.yml"
--8<-- "docs/assets/gitstream.yml"
```

## Set GitHub repo settings

!!! attention

    To get the full potential using gitStream, you need to set it as a required check.

To make sure gitStream can block PRs from merging under certain conditions, you should set the following:

1. Go to repo `settings`
2. On the left pane select `Code and automation` > `Branches` 
3. Set `Branch protection rules` for your desired branch 
4. Enable `Require status checks to pass before merging`
5. Search for `status checks in the last week for this repository`
6. Select `gitStream.cm` as required check
