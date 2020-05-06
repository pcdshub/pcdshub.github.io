# pcdshub.github.io
Home page for PCDS Python tools

## How to Contribute
In order to publish the documentation with `GitHub Pages` the generated HTML documentation needs to be kept on the "master" branch of the repository. To work around this the file the conventional Sphinx `.rst` files that instruct the build are kept on branch "source". If you would like to contribute to the documentation:

1. Fork this repository then check it out on your machine
2. Create a branch from the "source" branch contained within this repository
```bash
# Add the original repository as a remote
git remote add pcds https://github.com/pcdshub/pcdshub.github.io
# Fetch all the branches
git fetch --all
# Create your own source branch to track the original source
git checkout --track pcds/source
# Create your own feature branch
git checkout -b my_doc_change
```
3. Create a Pull Request to merge your changes with the "source" branch of this repository. Changes will be automatically updated after the Travis job completes.
