# Dynamsoft Documentation Repository Base

This repository contains the base set of files used to initialize a repository for solution documentation. Here we explain how to use Jekyll to build a static site, serve it locally for testing, and deploy using GitHub actions to Dynamsoft FTP servers for both our **preview site** and **production site**. Additionally, this README is an SOP on rules and best practices for maintaining our documentation. Note that some the instructions and descriptions stated here **may not apply to older repositories** that pre-date standardization efforts.

- [Dynamsoft Documentation Repository Base](#dynamsoft-documentation-repository-base)
  - [Initializing the Repository](#initializing-the-repository)
  - [Building and Previewing Locally](#building-and-previewing-locally)
  - [Deploying to Preview Site](#deploying-to-preview-site)
  - [Deploying to Production Site](#deploying-to-production-site)
  - [Directory and File Structure](#directory-and-file-structure)
    - [`_config.yml`](#_configyml)

## Overview

Dynamsoft product documentation are built using Jekyll using self-hosted CI runners before deploying to our production site. In addition to the production site, we have an internal testing site where any changes should be previewed before going to production. The preview site CI is automatically triggered upon changes to the `preview` branch of the repository. Likewise, the production site CI is triggered on changes to `main` (or `master` for some older repositories). To keep our repositories clean and secure, be sure to work off your own branches rather than `preview` and `main`, and squash commits when merging to either `preview` or master both to limit exposing confidential information, and to keep the repository presentable.

Updates to documentation repositories should follow this pattern:

1. Work on your private branch.
2. Build/serve on your local workstation with Jekyll before commits.
3. Once the changes look good, squash the commits and merge to the `preview` branch.
4. Confirm that the changes are correct on the preview site once the site is rebuilt.
5. Merge to the `main` branch to push changes to production.

## Initializing the Repository

First, clone the git repository for the new documentation, and then copy over all files in this repository. The contents of this README.md should be replaced with relevant content. **Do not copy** `.git`, but **do keep** `.gitignore`.

Next, search and replace placeholder text with solution names (these are just placeholders and not `liquid` variables used by Jekyll):

1. search for every instance of `{product-name}` and replace them with the official solution name in kebab case.
   1. e.g. for Dynamsoft Document Scanner JS, use `document-scanner-js`.
   2. Check with the website team to find out the actual location of the documentation and the documentation repository. Some solutions use the scheme `https://dynamsoft.com/{product-name}/docs`, whereas other solutions have multiple platforms, so **may use `https://dynamsoft.com/product-name/docs/platform` instead**. Similarly, the repository URL may be of the form `dynamsoft-docs/product-name-docs-platform`. **Check all paths in `_config.yml` carefully**.
      1. e.g. MRZ JS uses `https://dynamsoft.com/mrz-scanner/docs/web`, and the repository `https://github.com/dynamsoft-docs/mrz-scanner-docs-js`.
2. Replace both `{product-title}` and `Product Name` with **both** space-separated and title-cased solution names.
   1. e.g. for MRZ Scanner JS, just use `MRZ Scanner JS`.
3. Rename the file `_includes/productNav/{product-name}Nav.html` to use the official lower case solution initialism. This file is only used in local builds. The actual file used for production and preview deployments reside in the `Docs-Template-Repo` repository.
   1. e.g. `dbrNav.html` for Dynamsoft Barcode Scanner.
4. In `.github/workflows/main.yml`, replace occurrences of `product-name` and `product-docs-base` with the official solution name in kebab case, just as in case 1 above. Also check for discrepancies in paths for multi-platform solutions just as in case 1.
5. Uncomment front matter variables in markdown files where they exist.

Now, the repository is set up to build, preview, and compile the site.

As the site is build with Jekyll, please read the official [Jekyll documentation](https://jekyllrb.com/docs/) when writing the documentation for help with syntax.

## Building and Previewing Locally

Building locally allows you to preview the site and check that it builds correctly before making too many commits and deploying (or failing to deploy) too many times on the preview site. Building locally requires a Ruby runtime version 3.2.2 or later (we highly recommend installing ruby with an environment manager like [`mise`](https://mise.jdx.dev/dev-tools/) or `rvm`), and resources from [`Docs-Template-Repo`](https://github.com/dynamsoft-docs/Docs-Template-Repo). When running CI/CD, GitHub actions uses the resources from `Docs-Template-Repo` repository and uses Jekyll to build the site. The following steps just replicate the build process locally. Other deployment processes are not replicated here so should be tested by deploying to the preview site.

### Copying Resources

Clone `Docs-Template-Repo` or download it as an archive, and copy resources into the working project directory. **Check the `.gitignore` from  `product-docs-base` for files to copy over**. (i.e. copy over every file/directory listed in the `.gitignore`) Since these resources are only for local use, they are all included in `.gitignore` so they can be safely copied without polluting `git`.

### Serving Locally

In a console at the working directory, run `bundle install` to install the Ruby Gems found in the `Gemfile`. This installs all necessary tools like Jekyll and its requisite plugins.

Finally to build, run `bundle exec jekyll build`, which outputs site assets at `_site` (already in .gitignore). Run `bundle exec jekyll serve` to build and serve. Access the site from the browser at the address shown in the console output, and stop serving with `ctrl+c`. You can change the address and port with the optional parameter `-H`.

## Deploying to the Preview Site

The GitHub action to deploy to the preview test site triggers upon commits to the `preview` branch. Always merge your commits to the preview branch for testing before pushing to production on the `main` branch.

## Deploying to Production Site

The GitHub action to deploy to the production site triggers upon commits to the `main` branch. Remember to test on the `preview` branch before pushing to the `main` branch. A review from a maintainer is always required before a request for a merge to `main` can be accepted.

## Writing Documentation

Our documentation repositories across all our products attempt to adhere to a standard format. Here are some ground rules:

1. Use `liquid` macro variables for hyperlinks, rather than using raw URLs. This allows you to test links on local builds and on the preview site. Raw URLs only point to pages on the official site and so do not allow us to test new pages, among other things. Define new macro variables judiciously in the `_config.yml`.
2. Store image assets in the `/assets/imgs/` directory. The other directories under `/assets/` are ignored.
3. Run linters and spell-checkers. We recommend [Code Spell Checker](https://github.com/streetsidesoftware/vscode-spell-checker) on VS Code -derived editors. **Use US English**.
4. Run link-checkers on local and preview sites. [Broken Link Checker](https://webextension.org/listing/broken-link-checker.html) for Firefox can check links recursively. Be sure to rate limit and blacklist recurring links to avoid spamming our web servers.
5. Do not use contractions, e.g. "can't", "you've", and "we've" [sic].
6. Use the first person (active voice), e.g. the pronouns we and you. Do not use the singular first person pronoun "I".
7. Avoid the use of the verb "to be" at all costs. Look for other, more descriptive verbs in its place. This also avoids the use of the passive voice, e.g. write "x initializes y" instead of "y **is** initialized by x".
8. Use the present tense by default.
9. Use gerunds for page and section titles, e.g. write "Getting Started" instead of "Get Started".
10. Use a `liquid` LSP (language server protocol) to check for macro syntax errors. [Liquid](https://github.com/panoply/vscode-liquid) works for VS Code -derived editors.

## Directory and File Structure

Dynamsoft Documentation is built using Jekyll. Each file/directory present in this repository serve the following functions:

### `_config.yml`

The `_config.yml` file controls Jekyll configurations like defining `liquid` macro variables. `liquid` macro variables get substituted for their assigned value when Jekyll compiles the site. Some variables like `url`, `baseurl`, and `defaults` are used by Jekyll itself for pre-defined purposes, while most variables like `useVersionTree`, `firstLevelUrl`, and `smile_icon` are used by Dynamsoft templates and scripts. A variable named `variable` defined here is accessible with the ``{{ site.variable }}`` syntax. Commonly-used links are typically added as variables here, but most of the existing variables should stay as they are defined, other than substituting in the actual product names for every instance of `{product-name}` and `Product Name`.

Note that variables defined in the front matter (the configuration at the beginning of markdown files) cannot use `liquid` macro variables as they are processed before `liquid` variables become available in compilation.