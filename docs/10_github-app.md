# gitStream GitHub app

## Installation

To start using gitStream with GitHub source code hosting, a GitHub app should be installed. The installtion start with chosing the app on GitHub Apps marketplace.

1. Goto GitHub marketplace and install gitStream for free.

2. Install & authorize gitStream

gitStream can be installed for one repo, specific repos or all repos in your organization. 

Note: we reccomend installing for all repositories as it will also cover new repos you add in the future. You can chnage this setting at any time if you chnage your mind.

The permissions are: 

| Permissions           | Reason |
|----------------------|-------------------------------------------------------|
| Write access to dedicated gitstream app files | Used to set the gitStream workflow files |
| Write access to code | To allow gitStream to apporve PRs once all conditaions are met |
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
| .github/workflows/gitstream.yml | N | Used by gitStream to execute auotmation in your repo so source code doesn't get to outside services |

### Post installtiaon 

See [here](11_github-app-onboarding.md).

## Usage

Everytime a dev opens a new Pull Request or changes a Pull Request, gitStream is triggered. Next, the `.cm` file is used to determine which automatic actions are invoked based on their conditions. The resulting actions use GitHub API to achive the desired outomce.

### Automation results

Eventually the gitStream app shows the following statuses:  

If any if the actions that triggered purpose is to prevent merging PRs based on its conditions it will show as Failed. Other than that gitStream should show Success once all action were completed succesfuly.
