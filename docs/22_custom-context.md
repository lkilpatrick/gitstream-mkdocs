# Custom context

!!! attention

    :octicons-beaker-24: Coming soon

You can add custom context from diffrent sources:
1. file in workspace

The content fomrat supported is
1. text
2. numbers - use parseNumber
3. JSON - use parseJSON
4. YAML - use parseYAML

Then you can use it in your cm file in the `inputs` context.


- [`github`](#pr-context)
    - [`pr`](#pr-context)
        - [`author`](#pr-context)
        - [`wip`](#pr-context)
        - [`name`](#pr-context)
    - [`checks`](#checks-context)


#### `github` context

:octicons-beaker-24: Coming soon

| Values      | Type      | Description                                                     |
| ------------|-----------|---------------------------------------------------------------- |
| `github.pr`       | Map      | Includes the PR info
| `github.pr.author`| String    | The git user name of the PR author
| `github.pr.wip`   | Bool      | `true` when the PR is marked as draft 
| `github.pr.name`  | String    | The PR name
| `github.checks`   | String    | The PR name