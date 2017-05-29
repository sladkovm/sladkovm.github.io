---
layout: post
title:  "GitHub-Pages-Blog-with-Jekyll"
date:   2016-11-02 15:20:00 +0100
categories: jekyll
comments: true
---

Static blogs are all the rage nowadays and the easiest and the most popular way to create one is to reuse the *GitHub Pages* functionality in combination with *Jekyll* static site generator.

In this article I will go through the pain of creating a blog myself. To the large extent it follows the [official tutorial](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/), but spiced with my own comments.


## Expected outcome

At the end of this article there will be a *static* web blog running at the address:

```
https://user_name.github.io
```

where *user_name* is the name of the *GitHub* user account.

The *static* nature of the blog will allow submitting new posts by simply writing them locally in the *markdown* and than performing the holy trinity of *GitHub* commands `add,  commit, push`. GitHub will do all the heavy lifting in rendering and serving the content in *.html*.


## Glossary

`GitHub Page` - it is not a blog yet, but merely a static website served directly from *GitHub*. To turn it into blog, the blog generator, for example *Jekyll*, is required.

`Project GitHub Page` - each project (a.k.a. repository) on *GitHub* can have a *Page*. The *page* will be served from the special *branch* `gh-pages`, which allows it to coexist happily with other content of the repository. Automatically assigned *url* for such pages is `https://user_name.github.io/project_name/`.

`User GitHub Page` - the special type of repository, one per *GitHub* account, that will serve the page content directly from the `master` branch. The public *url* for such page is `https://user_name.github.io/`.

`Ruby` - well, a support for *ruby* programming language, in which *Jekyll* is written. I'm on Mac OS which comes with preinstalled *ruby*. To check the version I did simply run `> ruby --version`. In my case it was  *ruby v.2.0.0*. To be more specific I run *OS X El Capitan a.k.a. v10.11.6* and the *native* ruby turned out to be off-limit for installing any extra packages (typical permission denied problem). I had to install additional version of *ruby* using the version control manager *rbenv*.

`rbenv` - a version control manager for *ruby* installations that allows more than one release to be present on the system. Installation process is quite straightforward - [manual](https://github.com/rbenv/rbenv).

`Bundler` - *gem* dependencies manager. In practice it is used both for installing all required *Ruby* packages, for example *Jekyll*, and for running all things *Ruby* locally. To install *bundler* simply run `> gem install bundler`. If the installation fails on the permission rights - the *rbenv* is to the rescue.

`Jekyll` - static site generator that allows turning a *GitHub Page* into a static blog. In fact the *GitHub Pages* do use *Jekyll* behind the hood, which makes it a match made in heaven.


## How to create a user GitHub Page?

It is very simple in fact. I've created a repository with a name `user_name.github.io` by following the official [GitHub manual](https://help.github.com/articles/create-a-repo/)

The `user_name` is a name of my *GitHub* account followed by `.github.io`. Together this combination constitutes the public *url* from which the page will be served... prepended by `https://` of course.

The *GitHub Pages* repository can be cloned now to the local machine.


## Install Jekyll site generator

Since I've already installed *Bundler* (see the [Glossary](## Glossary)) I will use it to install *Jekyll* packages.

Step 1:

Create a file with a name *Gemfile* in the repository folder. This file contains installation instructions for *Bundler*.

Step 2:

Add the following content to the *Gemfile*:

```
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
```
For this job I did use *nano* in the Mac OS terminal, but it is a free world so use whatever you want, for example - [*Atom*](https://atom.io/) or any other text editor, even *Vim* or *emacs* if you are handy with their peculiar workflow - I'm personally too young to grow up with it and too old to learn it now ;).

Step 3:

Install *Jekyll* site generator by running `> bundle install`


## Generate Jekyll site files

Step 1:

Go into the the repository folder and from there run `> bundle exec jekyll new . --force`. This will generate a Jekyll site files in the repository folder.

Step 2:

Remove all the references to original *jekyll* packages in the *Gemfile* and replace them with references to *GutHub* specific *github-pages* - open the *Gemfile*, remove the line `"jekyll", "3.2.1"` and uncomment the line `gem "github-pages", group :jekyll_plugins`.


## Build the Jekyll site locally

Simply run `bundle exec jekyll serve`.

As it says, the site is now accessible at `http://127.0.0.1:4000/`.

What it does not says is that *css* styles did not apply and there is just a bare *html* page with a very *90's* look.

To fix this issue I had to edit *Jekyll* configuration file `_config.yml` - essentially remove `example.com` from the `url:` field, but this is a subject of a separate story.

## Bring the site online - push it to the GitHub remote

In order to bring this semi-functional site online simply I did the holy *GitHub* trinity magic:

```
> git add .
> git commit -m 'fresh Jekyll install'
> git push
```

And the page was indeed served at `https://user_name.github.io`... without *css* styles applied.

## Update: github-pages 102 gem for jekyll 3.3.0

Starting from *github-pages 102* based on the *jekyll 3.3.0* the *css* isues is gone and `_config.yml` is configured properly at the install.
