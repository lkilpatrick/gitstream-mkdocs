# Welcome to gitStream

## Insert workflow automation files to your repo

To start using gitStream, install it and add the following files in your repo default branch.

1. Make sure you have installed gitStream app from [GitHub marketplace](https://github.com/marketplace/gitstream-by-linearb), then

Add the following 2 files into your repo default branch  (usually `master` or `main`):

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
