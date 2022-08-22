# FAQ

### What permissions needed?

In your org permissions make sure GitHub actions are permitted:

Choose which repositories are permitted to use GitHub Actions.
[x] Allow all actions and reusable workflows

### Does gitStream services have access to my code?

No. Like any other CI/CD automation, the source code is being scanned in the repo and is not 
shared with any external services. Only metadata that relates and affects the workflow is shared to 
allow rule based automation on the repo.

### What repos are supported?

Any repo in GitHub is currently supported. More Git providers are planned soon.

### Do I need VS Code to use gitStream?

No. gitStream is a an CM automation system, nevertheless, developers can benefit from having the VS Code 
extension as it presents in the editor what rules can be used to simplify their workflow.

### Custom filter functions 

Coming soon.