# gitstream-mkdocs

## Setup

Run Docker daemon, and then:

```
docker pull squidfunk/mkdocs-material
```

For Mac M1, better to use different container:
```
docker pull ghcr.io/afritzler/mkdocs-material
```

Note: When creating this repo, the following command was used
```
docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material new .
```

## Run

```
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```

For Mac M1, its better to use:
```
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs ghcr.io/afritzler/mkdocs-material
```

Then go to http://http://localhost:8000