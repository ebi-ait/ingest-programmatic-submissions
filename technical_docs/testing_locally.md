# Testing locally

## Purpose of this document

This document showcases how to deploy the pages locally, so that they can be tested before making any changes into the
remote repository.

If changes are made to the config YAML files, please apply them both to "_config_local.yml" and "_config.yml"

## Before you start

- [Install and setup Jekyll](https://jekyllrb.com/docs/step-by-step/01-setup/) - Follow "installation" steps.

# Test

- Run the following commands:
```bash
jekyll build
jekyll serve --config _config_local.yml
```

And follow the server address (Usually an IP adress with port 4000) to see how the page looks like.

The build command only needs to be run when the "config" files are altered.

