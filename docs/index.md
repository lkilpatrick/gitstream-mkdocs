# Introduction

Welcome to gitStream. 

This site will contain all the infomation you need to get started with gitStream and Contniuous Merge!


# Getting Started 

To get started with gitStream CM automation do the following:

### Step 0:
choose a repo, or clone our playground repo: 


TODO: move this repo to public access We should move it to continuous merge

```shell
  git clone git@github.com:linear-b/gitstream-playground.git
  cd gitstream-playground
```

### Step 1: 
Create a .cm file in your repo root directory, you can use example below. You should create it at the following path  .cm/.gitstream.cm - I would like a line by line explanation of this file eventually

```yaml
manifest:
  version: 1.0

checks:
  content:
    is:
      asset_only: {{ files | allExtensions(['png', 'svg', 'jpg', 'gif', 'css']) }}
      docs_only: {{ files | allExtensions(['md', 'txt']) }}
      tests_only: {{ files | allTests }}
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}
      small_or_less: {{ branch.diff.size < 50 }}
      medium_or_less: {{ branch.diff.size < 200 }}
      large: {{ branch.diff.size >= 200 }}

automations:
  change_tests:
    on: [pull_request]
    rules:
      - rule: {{ checks.content.is.tests_only }}
    actions: 
      - exec: hmarr/auto-approve-action@v2
        with:
          github-token: ${{'{{'}} secrets.GITHUB_TOKEN {{'}}'}}
      - exec: actions-ecosystem/action-add-labels@v1
        with:
          labels: tests
  docs_update:
    on: [pull_request]
    rules:
      - rule: {{ checks.content.is.docs_only }}
    actions: 
      - exec: hmarr/auto-approve-action@v2
        with:
          github-token: ${{'{{'}} secrets.GITHUB_TOKEN {{'}}'}}
      - exec: actions-ecosystem/action-add-labels@v1
        with:
          labels: docs
  xsmall:
    on: [pull_request]
    rules:
      - rule: {{ checks.size.is.xsmall }}
    actions: 
      - exec: actions-ecosystem/action-add-labels@v1
        with:
          labels: xsmall
  large_change:
    on: [pull_request]
    rules:
      - rule: {{ checks.size.is.large }}
    actions: 
      - exec: actions-ecosystem/action-add-labels@v1
        with:
          labels: large
```

### Step 2: 

Create GitHub action workflow files by using the on-line generator (link) or download the cli tool (link). Place the action file at .github/workflows/gitstream.yml

As an example for the above rules:

```yaml
name: gitStream
'on':
  - pull_request
jobs:
  gitstream:
    runs-on: ubuntu-latest
    outputs:
      gate_change_tests_passed: ${{ steps.checks.outputs.gate_change_tests_passed }}
      gate_docs_update_passed: ${{ steps.checks.outputs.gate_docs_update_passed }}
      gate_xsmall_passed: ${{ steps.checks.outputs.gate_xsmall_passed }}
      gate_large_change_passed: ${{ steps.checks.outputs.gate_large_change_passed }}
    steps:
      - run: echo "getting the repo code"
      - name: Check out repository code
        uses: actions/checkout@v3
      - name: Run gitstream checks
        uses: linear-b/actions/gitstream@v1
        id: checks
        with:
          repo: ${{ github.repository }}
          branch: ${{ github.head_ref }}
          use_cache: true
  gate_change_tests:
    needs: gitstream
    if: needs.gitstream.outputs.gate_change_tests_passed == 'true'
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: hmarr/auto-approve-action@v2
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
      - uses: actions-ecosystem/action-add-labels@v1
        with:
          labels: tests
  gate_docs_update:
    needs: gitstream
    if: needs.gitstream.outputs.gate_docs_update_passed == 'true'
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: hmarr/auto-approve-action@v2
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
      - uses: actions-ecosystem/action-add-labels@v1
        with:
          labels: docs
  gate_xsmall:
    needs: gitstream
    if: needs.gitstream.outputs.gate_xsmall_passed == 'true'
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions-ecosystem/action-add-labels@v1
        with:
          labels: xsmall
  gate_large_change:
    needs: gitstream
    if: needs.gitstream.outputs.gate_large_change_passed == 'true'
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions-ecosystem/action-add-labels@v1
        with:
          labels: large
```

### Step 3: 

Commit these 2 files into your default branch, usually main or master branch.

```shell
  git add .cm/gitstream.cm
  git add .github/workflows/gitstream.yml
  git commit -m "hello gitstream, goodbye toil"
  git push
```

For new branches, once you open a Pull Request the automation rules will be applied based on the checks results.



## Installation
## Configuration
## Understanding CM


# .CM file
## File Used
## YAML


# Conditions
## Gates
## Lanes
## Grammar

# Actions

# Commands

# Examples