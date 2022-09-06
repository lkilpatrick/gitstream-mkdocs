# Context variables

gitStream includes a collection of variables called contexts. 

- [`branch`](#branch-context)
    - `name`
    - `base`
    - `diff`
        - `size`
        - `files_metadata`
- [`files`](#files-context)
- [`source`](#source-context)
    - `diff`
        - `files`
- [`repo`](#repo-context)
    - `contributors`
- [`pr`](#pr-context)
    - `author`

#### `branch` context

:octicons-tag-24: Minimal version: 1.0

| Values               | Type      | Description                                              |
|----------------------|-----------|--------------------------------------------------------- |
| `branch`             | Map       | Includes the info related to the current branch          |
| `branch.name`        | String    | The current branch, `feature-123-branch`                 |
| `branch.diff`        | Map       | Includes the info compared to the default branch, `main` |
| `branch.diff.size`   | Integer   | The sum of line changed: additions, edits and deletions   |
| `branch.diff.files_metadata`  | [Map]  | List of changed files including their relative path      |

The branch context doesn't include any source code, but only related metadata.

The `files_metadata` mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `file` | String    | The name of the file before the changes, including its path     |
| `additions` | Integer   | The number of lines edited or added to the file  |
| `deletions` | Integer   | The number of lines removed from the file      |

#### `files` context

:octicons-tag-24: Minimal version: 1.0

| Values  | Type      | Description                                |
|---------|-----------|------------------------ |
| `files` | [String]  | List of all changed files with their full path |


#### `source` context

:octicons-tag-24: Minimal version: 1.0

| Values              | Type  | Description                                        |
|---------------------|-------|--------------------------------------------------- |
| `source`          | Map   | Info related to source code           |
| `source.diff`     | [Map] | Includes the info compared to the default branch, `main` |
| `source.diff.files` | [Map] | List of changed files with their code changes |

The source context include all code changes, it is not safe to share it with unknown services.

The `files` mapping includes a list of the following structure:

| Values          | Type      | Description                                          |
| ----------------|-----------|----------------------------------------------------- |
| `original_file` | String    | The name of the file before the changes, including its path |
| `new_file`      | String    | The name of the file after the changes, including its path |
| `diff`          | String    | The content in diff format `+` for additions, `-` for deletions |
| `original_content` | String    | The content as is in the `main` branch     |
| `new_content`      | String    | The new content in this branch     |

#### `repo` context

:octicons-tag-24: Minimal version: 1.0

| Values             | Type      | Description                                              |
|--------------------|-----------|-------------------------------------------------|
| `repo`             | Map       | Includes the info related to the current repo   |
| `repo.contributors`  | [Map]  | List of changed files including their relative path |

The `contributors` mapping includes a list of the following structure:

| Values          | Type      | Description                                         |
| ----------------|-----------|---------------------------------------------------- |
| `name` | String    | Name of contributor     |
| `commitsInRepo` | Number   | The number of commit by contributor to repo  |

#### `pr` context

:octicons-tag-24: Minimal version: 1.0

| Values   | Type      | Description          |
|----------|-----------|------------------------ |
| `author` | String   | The PR author name |
