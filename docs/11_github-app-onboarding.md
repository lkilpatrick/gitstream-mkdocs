# Post installation instructions

## Insert workflow automation files to your repo

!!! attention

    To start using gitStream, create the following files in each repo's main branch with the following content.

This file should be updated with new rules and automations. 

```yaml title=".cm/gitstream.cm"
manifest:
  version: 1.0

# Define condition under the `checks` section, each check consists of
# an expression. The checks results are evalauated and used as conditions 
# for the next section `automations`. 
# Checks expressions are wrapped with double curly braces and includes a
# context variable like `files` and filter like `length`.
# Filters are essentially functions that can be applied to variables. They 
# are called with a pipe operator (|) and can take arguments.
checks:
  content:
    is:
      docs_only: {{ files | allExtensions(['md', 'txt', 'rst']) }}
      images_only: {{ files | allExtensions(['png', 'jpg', 'svg']) }}
  size:
    is:
      xsmall: {{ branch.diff.size <= 5 }}
      small: {{ branch.diff.size > 5 and branch.diff.size <= 20 }}
      medium: {{ branch.diff.size > 20 and branch.diff.size <= 100 }}
      large: {{ branch.diff.size > 100 and branch.diff.size <= 200 }}
      xlarge: {{ branch.diff.size > 200 }}
  review:
    etr: {{ branch | estimatedReviewTime }}

# The `automations` section include the list of automation that applies 
# for the repo in which gitStream is installed. 
# Each autaomtion has `if` key with a list of the necessary condtions and
# a `run` key with a list of all actions. All the listed condtions need to  
# pass in order for the following actions to be executed.
automations:
  approve_docs_changes:
    if:
      - {{ checks.content.is.docs_only }}
    run: 
      - action : approve@v1
      - action : add-labels@v1
        args:
          labels: ['non-code']
  approve_docs_changes:
    if:
      - {{ checks.content.is.images_only }}
    run: 
      - action : approve@v1
      - action : add-labels@v1
        args:
          labels: ['non-code']
  small_pr:
    if:
      - {{ checks.size.is.small }}
    run: 
      - action : add-labels@v1
        args:
          labels: ['small']
  add_context:
    if:
      - true
    run: 
      - action : add-comment@v1
        args:
          comment: Estimated {{ checks.review.etr }} minutes to review
```


This file is used by gitStream to trigger the automation in GitHub, you should not edit it. Editing 
it might break the functionality or might not be compatible with future changes in gitStream. 


```yaml title=".github/workflows/gitstream.yml"
name: gitstream Workflow

on:
  workflow_dispatch:
    inputs:
      client_payload:
          description: The Client payload
          required: true
      full_repository:
          description: the repository name include the owner in `owner/repo_name` format
          required: true
      head_ref:
          description: the head sha
          required: true
      base_ref:
          description: the base ref 
          required: true
      installation_id:
          description: the installation id
          required: false
      resolver_url:
          description: the resolver url to pass results to
          required: true
      resolver_token:
          description: Optional resolver token for resulver service
          required: false
          default: ''

jobs:
  continuous-merge-rules:
    runs-on: ubuntu-latest
    name: gitStream automation
    steps:
      - name: Evaluate Rules
        uses: linear-b/gitstream-github-action@v1
        id: rules-engine
        with:
          full_repository: ${{ github.event.inputs.full_repository }}
          head_ref: ${{ github.event.inputs.head_ref }}
          base_ref: ${{ github.event.inputs.base_ref }}
          client_payload: ${{ github.event.inputs.client_payload }}
          installation_id: ${{ github.event.inputs.installation_id }}
          resolver_url: ${{ github.event.inputs.resolver_url }}
          resolver_token: ${{ github.event.inputs.resolver_token }}
      - name: Update gitstream check on error
47      if: failure() && steps.rules-engine.outcome == 'failure'
```

## Set GitHub repo settings

!!! attention

    To get the full potnetial using gitStream, you need to set it as required check.

To make sure gitStream can block PRs from merging under certain conditions you should set the following:

1. Go to repo settings
2. On the left pane select Code and automation > Branches 
3. Set "Branch protection rules" for your desired branch 
4. Enable "Require status checks to pass before merging"
5. Enable "Require branches to be up to date before merging"
6. Search for status checks in the last week for this repository
7. Select gitStream.cm as required check
