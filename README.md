# Page Management #

[![Build Status](https://travis-ci.org/Matthimatiker/matthimatiker.github.io.svg?branch=master)](https://travis-ci.org/Matthimatiker/matthimatiker.github.io)

## Setup ##

Install dependencies:

    bundler install

## Usage ##

## Start Local Server ##

Start Jekyll locally:

    bundle exec jekyll serve --drafts

Access the page at [http://localhost:4000](http://localhost:4000).

### Active Navigation ###

The active navigation item can be controlled via ``active_navigation`` variable in the front matter:

    ---
    active_navigation: "about"
    ---
    
If omitted, then the "blog" item will be active.

## Troubleshooting ##

### Error When Building Native Extensions ##

If you experience errors during ``bundler install`` when building native extensions and you just updated MacOS, then try the following: 

    xcode-select --install
    sudo xcodebuild -license accept
    
Retry the installation afterwards.

## References ##

- [Basics](https://help.github.com/categories/github-pages-basics)
- [Features](https://help.github.com/categories/github-pages-features)
- [Troubleshooting](https://help.github.com/categories/github-pages-troubleshooting)
- [Configuring builds](http://jekyllrb.com/docs/continuous-integration/)
- [Template Filters](http://jekyllrb.com/docs/templates/)
