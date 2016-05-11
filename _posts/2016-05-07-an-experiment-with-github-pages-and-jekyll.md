---
layout: post
title:  "An experiment with GitHub Pages and Jekyll"
date:   2016-05-07 18:55:23 -0400
categories:
comments: true
disqus_identifier: 9091FAB9-E88D-4EEE-BBA3-C1A0D213C82D
tags: jekyll github disqus
---

# An experiment with GitHub Pages and Jekyll

TLDR; It wasn't too bad; it took just under an hour to get a blog site with comments up and running.

This site is generated from static files manipulated with a simple text editor.
To help facilitate the creation of these static files, jekyll, a ruby-based static
blog-centric site generator, is tasked for the job.

Being in an OSX environment a few things are required to get going. You will need
a recent version of `ruby` installed. You can install the latest version with `brew`.

You will need to install `bundler` with `gem install bundler`.

We start off with creating a new directory for the site and initializing a `git`
repository within it.

```bash
mkdir dk-site; cd dk-site; git init
```

The next thing we need to do is create a `Gemfile` and configure our dependencies.

```bash
echo -e "source 'https://rubygems.org'\ngem 'github-pages', group: :jekyll_plugins" >> Gemfile
```

We should now have a `Gemfile` with two lines in it. We continue with `bundle install`.

Once the installation is completed we will create a bare structure for a jekyll site.

```bash
bundle exec jekyll new . --force
```

A `git status` should now show a bunch of new untracked files and directories.

GitHub pages implements jekyll and is perfect for hosting static content. You
can create a new GitHub username and then by simply creating a repository with the name
`github_username.github.io`, GitHub will automatically regenerate and publish
your site every time you push changes to the repository.

Start customizing the site by editing the `_config.yml` file.

```bash
vi _config.yml
```

In typical YAML-formatted key-value pairs, the main configurable variables are
contained within this file; with options such as title, email, description,
baseurl, url, twitter_username and github_username.

```YAML-formatted
title: darekkowalski.github.io
email: darek@
description: >
    Darek Kowalski
baseurl: ""
url: "https://darekkowalski.github.io"
twitter_username: darekkowalski
github_username: darekkowalski

markdown: kramdown
```

Now we can fire up a local server and navigate to http://localhost:4000 to see
what we have so far.

```bash
bundle exec jekyll serve
```

Lets add all these files to our master branch and push them up to GitHub.

```git
git remote add origin https://github.com/darekkowalski/darekkowalski.github.io.git
git add * -- .gitignore hits on _site
git push --set-upstream origin master

```
