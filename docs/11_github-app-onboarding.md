# Post installation instructions

## Insert workflow automation files to your repo

!!! attention

    To start using gitStream, create the following files in each repo's main branch with the following content.


`.cm/gitstream.cm`

```yaml
manifest:
  version: 1.0

checks:
  content:
    is:
      docs_only: {{ files | allExtensions(['md', 'txt', 'rst']) }}
      tests_only: {{ files | allTests }}
  size:
    is:
      small: {{ branch.diff.size < 20 }}

automations:
  docs_only:
    if:
      - {{ checks.content.is.docs_only }}
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
          comment: Estimated {{ branch | estimatedReviewTime }} to review
```


`.github/workflows/gitstream.yml`

```yaml
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
```

## Set GitHub repo settings

To make sure gitStream can block PRs from merging under certain condtions you should set the following:

1. Go to repo settings
2. On the left pane select Code and automation > Branches 
3. Set "Branch protection rules" for your desired branch 
4. Enable "Require status checks to pass before merging"
5. Enable "Require branches to be up to date before merging"
6. Search for status checks in the last week for this repository
7. Select gitStream.cm as required check
