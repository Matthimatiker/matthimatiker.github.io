# Page Management #

[![Build Status](https://travis-ci.org/Matthimatiker/matthimatiker.github.io.svg?branch=master)](https://travis-ci.org/Matthimatiker/matthimatiker.github.io)

## Setup ##

Install dependencies:

    bundler install

## Creation ##

Start Jekyll locally:

    bundle exec jekyll serve

Access the page at [http://localhost:4000](http://localhost:4000).

## Troubleshooting ##

### Error when building native extensions ##

If you experience errors during ``bundler install`` when building native extensions and you just updated MacOS, then try the following: 

    xcode-select --install
    sudo xcodebuild -license accept
    
Retry the installation afterwards.

## GitHub Pages documentation ##

- [Basics](https://help.github.com/categories/github-pages-basics)
- [Features](https://help.github.com/categories/github-pages-features)
- [Troubleshooting](https://help.github.com/categories/github-pages-troubleshooting)
- [Configuring builds](http://jekyllrb.com/docs/continuous-integration/)
