# Welcome to gitStream

## Insert workflow automation files to your repo

To start using gitStream, create the following files in each repo's main branch with the following content.

Add the following 2 files into each of your repos main branch:

1. `.cm/gitstream.cm`
2. `.github/workflows/gitstream.yml`

Copy-paste from here:

=== ":octicons-file-code-16: `.cm/gitstream.cm`"

	```yaml+jinja
	--8<-- "docs/assets/gitstream.cm"
	```

=== ":octicons-file-code-16: `.github/workflows/gitstream.yml`"

	```yaml+jinja	
	--8<-- "docs/assets/gitstream.yml"
	```
