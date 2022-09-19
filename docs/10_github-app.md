# gitStream GitHub app

## Installation

To start using gitStream with GitHub source code hosting, a GitHub app should be installed. The installation start with installing the app from the GitHub Apps marketplace.

1. Get gitStream for free from [GitHub marketplace](https://github.com/marketplace/gitstream-by-linearb)
2. Add `.cm/gitstream.cm` and `.github/workflows/gitstream.yml` to your repo
3. Get your automation for your next PR!

gitStream can be installed for one repo, specific repos, or all repos in your organization. 

Note: we recommend installing for all repositories, as it will also cover new repos you add in the future. You can change this setting at any time if you change your mind.

The permissions are: 

| Permissions           | Reason |
|----------------------|-------------------------------------------------------|
| Write access to dedicated gitStream app files | Used to set the gitStream workflow files |
| Write access to code | To allow gitStream to approve PRs once all conditions are met |
| Read access to administration, issues, and metadata | To read the code on monitored repositories |
| Read and write access to actions, checks, pull requests, and workflows | Trigger workflows, create and update pull requests and their checks, and modify workflow files |
| User email | Used to identify user |

Once installation completes, the following files are added to each of the selected repos under their root directory.

```
.
├─ .cm/
│  └─ gitstream.cm
├─ .github/
│  └─ workflows/
│     └─ gitstream.yml
```

| File and path        | Customizable | Reason |
|----------------------|--------------|----------------------------------------|
| .cm/gitstream.cm     | Y            | Used to specify gitStream automations, you can edit this file |
| .github/workflows/gitstream.yml | N | Used by gitStream to execute automation in your repo so source code doesn't get to outside services |

### Post installation  

Get the following files into each of your repos main branch:

1. `.cm/gitstream.cm` 
2. `.github/workflows/gitstream.yml`

See instructions [here](11_github-app-onboarding.md).

## Usage

Every time a dev opens a new Pull Request or changes a Pull Request, gitStream is triggered. Next, the `.cm` file is used to determine which automatic actions are invoked based on their conditions. The resulting actions use GitHub API to achieve the desired outcome.

### How it works

Next steps are common practice of changing code in repo:

1. Create a branch 
2. Commit changes  
3. Push branch to remote repo 
4. Open Pull Request 

When a new PR is opened, gitStream GitHub app gets triggered:

5. gitStream gets event for new PR and calls the installed action in `.github/workflows/gitstream.yml`
8. The installed action pulls and runs gitStream action `linear-b/gitstream-github-action@v1`. This action runs locally in the repo and relies on 
    1. The provided PR context
    2. The repos automations as defined in `.cm/gitstream.cm`
9. It sends the list of applicable automations to the gitStream GitHub app
10. gitStream GitHub app iterates over the automations and invokes each action using GitHub APIs
11. The PR gets updated according to the desired automations
12. The PR is ready for further review or merge

### Automation results

Eventually, the gitStream app shows the following statuses:  

- Success - when the applicable automation finished and PR is good to go 
- Neutral - when there aren't any applicable automations for the PR
- Failed - when the applicable automation finished without completion