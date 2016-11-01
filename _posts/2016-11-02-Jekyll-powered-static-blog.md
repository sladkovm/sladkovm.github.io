---
layout: post
title:  "Jekyll powered static blog on GitHub pages"
date:   2016-11-02 00:41:29 +0100
categories: jekyll update
---


# Jekyll powered static blog on GitHub pages

### Create repository and clone it locally

Create repo with the name `github_account_name.github.io`

This will make the blog to be served from exactly the same domain, which, in my opinion, esthetically looks nice:

`https://github_account_name.github.io/`

If, on the other hand, you will chose to use the name different from the abovementioned, the blog will be served from ugly address like:

`https://github_account_name.github.io/repo_name/`

When creating the repo do not forget to include the `.gitignore` file with configuration `Jekyll`.

The content of the `.gitignore` will be following:

```
_site
.sass-cache
.jekyll-metadata
```

Now you are ready to clone the repo locally

```
> git clone repo.git
```

### Install Jekyll

In the repo directory create file `Gemfile` and edit it in the text editor e.g. `nano`

```
> cd repo
> touch Gemfile
> nano Gemfile
```

Add content to the file

```
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
```

run:

```
> bundle install
```

### [Optional] Update the `rubygems`

If during the *bundle install* the following message popped up:

```
Rubygems 2.0.14.1 is not threadsafe,
so you gems will be installed one at a time.
Upgrade to Rubygems 2.1.0 or higher to enable
parallel gem installation.
```

it is probably a good idea to update the `rubygems` installed on the system.

```
> sudo gem install rubygems-update
> sudo update_rubygems
> sudo gem update --system
```

### Generate Jekyll site files

```
> bundle exec jekyll new . --force
```

Open in the text editor `Gemfile` and remove (or comment) the line:

```
gem "jekyll", "3.2.1"
```

Uncomment the line:

```
gem "github-pages", group :jekyll_plugins
```

Btw, if you will try to run `bundle update github-pages` at this moment - it won't work. Proceed to the next step.

### Build the local Jekyll site

Run:

```
> bundle exec jekyll serve
```

The site will render at `http://127.0.0.1:4000/`, but the `.css` styles wont' be applied.

If you check the console for error you will most likely see the following:

```
Failed to load resource:
the server responded with a status of 404
http://example.com/css/main.css (Not Found)
```

Well, it loos like it is a time to get rid of this `http://example.com/` ...

### Basic edit of `_config.yml`

This is the file, where most of the configuration magic takes place:
