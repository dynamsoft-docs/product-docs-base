# Dynamsoft Documentation Repository Base

This repository contains the base set of files used to initialize a repository for solution documentation.  Here we explain how to use Jekyll to build a static site, serve it locally for testing, and deploy using GitHub actions to Dynamsoft FTP servers for both our **preview site** and **production site**.

- [Dynamsoft Documentation Repository Base](#dynamsoft-documentation-repository-base)
  - [Initializing the Repository](#initializing-the-repository)
  - [Building and Previewing Locally](#building-and-previewing-locally)
  - [Directory and File Structure](#directory-and-file-structure)
    - [`_config.yml`](#_configyml)

- [Dynamsoft Documentation Repository Base](#dynamsoft-documentation-repository-base)

## Initializing the Repository

First, clone the git repository for the product documentation, and then move all files in this repository in there. The contents of this README.md should be replaced with relevant content. Next, search for every instance of `{product-name}` and replace them with the actual product name in kebab case, and do the same for `{product-title}` and `Product Name` but both space-separated and title-cased. Finally, in `.github/workflows/main.yml`, change occurrences of `product-name` and `product-docs-base` accordingly. These are just placeholders and not liquid variables.

Now, the repository is set up for building, previewing, and compiling the site.

As the site is build with Jekyll, please read the official [Jekyll documentation](https://jekyllrb.com/docs/) when writing the documentation for help with syntax.

## Building and Previewing Locally

Building locally allows you to preview the site and check that it builds correctly before making too many commits and deploying (or failing to deploy) too many times on the preview site. Building locally requires a Ruby runtime (we highly recommend installing ruby with an environment manager like `mise` or `rvm`), and resources from [Docs-Template-Repo](https://github.com/dynamsoft-docs/Docs-Template-Repo). When running CI/CD, GitHub actions uses the resources from that repository and uses Jekyll to build the site.

First, install Ruby (at least version 3.2.2). Then, clone `Docs-Template-Repo`, and copy all files into the working project directory. Since these resources are only for local use, they are all included in `.gitignore` so they can be safely copied without polluting `git`.

In a console at the working directory, run `bundle install` to install the Ruby Gems found in the `Gemfile`. This should install all necessary tools like Jekyll and its requisite plugins.

Finally to build, run `bundle exec jekyll build`, which outputs site assets at `_site` (already in .gitignore). To build and serve in one go, just run `bundle exec jekyll serve`. Access the site from the browser at the address shown in the console output, and stop serving with `ctrl+c`. You can change the address and port with the optional parameter `-H`.

## Deploying to Preview Site

The GitHub action to deploy to the preview test site triggers upon commits to the `preview` branch.

## Deploying to Production Site

The GitHub action to deploy to the production site triggers upon commits to the `main` branch.

## Directory and File Structure

Dynamsoft Documentation is built using Jekyll. Each file/directory present in this repository serve the following functions:

### `_config.yml`

The `_config.yml` file controls Jekyll configurations like defining default page templates liquid macro variables. Some variables like `url`, `baseurl`, and `defaults` are used by Jekyll itself for pre-defined purposes, while most variables like `useVersionTree`, `firstLevelUrl`, and `smile_icon` are used by Dynamsoft templates and scripts. A variable named `variable` defined here is accessible with the ``{{ site.variable }}`` syntax. Commonly-used links are typically added as variables here, but most of the existing variables should stay as they are defined, other than substituting in the actual product names for every instance of `{product-name}` and `Product Name`.