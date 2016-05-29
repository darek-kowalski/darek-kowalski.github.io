---
layout: post
title:  "An experiment with GitHub Pages, Jekyll and Travis CI"
date:   2016-05-29 17:17:17 -0400
categories:
comments: true
disqus_identifier: 9091FAB9-E88D-4EEE-BBA3-C1A0D213C82D
tags: jekyll github disqus travis-ci
---

# An experiment with GitHub Pages, Jekyll and Travis CI.

This site is generated from static files manipulated with a simple text editor.
To help facilitate the creation of these static files, jekyll, a ruby-based static
blog-centric site generator, is tasked for the job.

Being in an OS X environment a few things are required to get going. You will need
a recent version of `ruby` installed. You can install the latest version with `brew` or some other means.

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
contained within this file; with options such as `title`, `email`, `description`,
`baseurl`, `url`, `twitter_username` and `github_username`.

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

Now we can fire up a local jekyll server and navigate to http://localhost:4000 to see
what we have so far. We should see a very empty bare-bones static website.

```bash
bundle exec jekyll serve
```

One really cool feature that the internal jekyll server offers, is that it supports auto-regeneration.
When it detects changes to source files, it will rebuild the file and make the generated
output available immediately.

The bare jekyll site structure is made up of a few directories and files. A `.gitignore` is created
to skip the `_site` directory, as well as the `.sass-cache` and `.jekyll_metadata` directories.

In the root directory we have a `Gemfile` defining our Ruby dependencies and a `_config.yml` file with our jekyll variables. Aside from those configuration files we have `feed.xml`, `index.html` and `about.md`.

The static files are broken down into `_includes`, `_layouts`, `_sass`, `_posts` and `css` directories.
`_includes` contains our header.html and footer.html as well as SVG assets for the Twitter and Github icons.
`_layouts` contains our html templates files: `default.html`, `page.html` and `post.html`.
`_posts` contains this very posts markdown file.
`_sass` contains scss markup to define our site styles: `_base.scss`, `_layout.scss`, `_syntax-highlighting.scss` and `main.scss`.

Lets create a `README.md` and edit some files before committing to our master branch and pushing up to GitHub.

```git
git remote add origin https://github.com/darekkowalski/darekkowalski.github.io.git
git add .gitignore Gemfile _config.yml feed.xml index.html about.md README.md
git add _includes _layouts _posts _sass css
git commit -m "Added base jekyll site structure"
git push --set-upstream origin master
```

GitHub Pages will now process our files and make the `_site` directory available at https://darekkowalski.github.io - The `_site` directory is where jekyll stores generated output.

Our first post looks a little bare, so lets integrate Disqus, a popular comment platform, into our post pages.
We start by creating an account on Disqus and configuring it for our site.

In order to get our post pages uniquely identified as relevant and separate comment threads
we need to supply Disqus with some specific variables. With jekyll it is possible to add
variables to the top portion of asset files. Namely, the "front matter" is delimited by lines
with three dashes and found at the top of the files. Within this "front matter" variables
such as layout, title, date, categories and tags are made configurable.

To implement Disqus we will add three new variables into our "front matter". For added control,
we will add a boolean variable named `comments` with a value of true for this first post.

Also, a variable named `disqus_identifier` with a unique value. It is really up to you what you
provide here, but I opted for a randomly generated UUID. Some opt to just use the post title.

The last variable is `tags` and contains a space separated list of tags relevant to the post content.

```
 comments: true
 disqus_identifier: 9091FAB9-E88D-4EEE-BBA3-C1A0D213C82D
 tags: jekyll github disqus
```

Now we will add the generated javascript from Disqus into our `_layouts\post.html` template file and
use the `disqus_identifier` variable defined in the posts "front matter" to configure the engine. Also,
we will create a condition to only include the commenting engine when our `comments` variable is set to true.

```
{% if page.comments %}
<div id="disqus_thread"></div>
<script>
var disqus_config = function () {
    this.page.url = '{{ page.url | prepend: site.url  }}';
    this.page.identifier = '{{ page.disqus_identifier }}';
};
(function() {
    var d = document, s = d.createElement('script');
    s.src = '//darekkowalski.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
{% endif %}
```

Now we will add comment counts to our main post listing index.html file within the `site.posts` loop.

```
+<li>
+   <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }} &middot; <span class="disqus-comment-count" data-disqus-identifier="{{ post.disqus_identifier }}"></span></span>
+   <h2><a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h2>
+</li>
```

Adding the specific `disqus-comment-count` class into a span element along with `data-disqus-identifier="{{ post.disqus_identifier }}"` enables Disqus to populate the element with a comment count value for the
specific disqus_identifier post.

At the bottom of the index.html we will add the Disqus comment count javascript asset.

```
<script id="dsq-count-scr" src="//darekkowalski.disqus.com/count.js" async></script>
```

If you're not already running a local jekyll server and refreshing your browser as you make these changes,
fire up another `bundle exec jekyll serve` and check if the Disqus comment section appears in the post.

We should now see a Disqus comment block on our post page and a comment count on our home page.

If everything is working as expected, commit and push these changes up to GitHub.

Cool, our site looks a lot more like a collaborative blog site now. Let's implement continuous
integration builds with Travis now! If you don't already have an account with travis-ci you will need to create one at this point and configure your GitHub repository.  

We will use `jekyll build` and the Ruby gem `html-proofer` to validate a successful build of our site each time
we push our master branch to GitHub.

First we need to update our `Gemfile` to include `html-proofer` as a dependency.

```
+gem 'html-proofer'
```

Next, we'll create a `build` directory and a shell script named `cibuild` to test our builds.

```
#!/usr/bin/env bash
set -e # halt script on error

bundle exec jekyll build
bundle exec htmlproofer ./_site --disable-external
```

To tell travis-ci what it needs to do, we create a `.travis.yml` file in the root directory.

```
language: ruby
rvm:
- 2.1

before_script:
 - chmod +x ./build/cibuild

script: ./build/cibuild

env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true

sudo: false
```

Here we tell travis-ci that we are using ruby and defining instructions for our build.
Notice we make our `cibuild` shell script executable before execution.

Lets commit these changes push it up to GitHub and check-in with travis-ci to see results of our build.

Eek, our build failed. https://travis-ci.org/darekkowalski/darekkowalski.github.io/builds

It complains about a `Gemfile.lock` not being there for assistance, so lets push up our generated file.

```
You are trying to install in deployment mode after changing
your Gemfile. Run `bundle install` elsewhere and add the
updated Gemfile.lock to version control.
```

Now we have a problem with date formats in our vendor directory.

```
1.09s$ ./build/cibuild
Configuration file: /home/travis/build/darekkowalski/darekkowalski.github.io/_config.yml
            Source: /home/travis/build/darekkowalski/darekkowalski.github.io
       Destination: /home/travis/build/darekkowalski/darekkowalski.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
             ERROR: YOUR SITE COULD NOT BE BUILT:
                    ------------------------------------
                    Invalid date '<%= Time.now.strftime('%Y-%m-%d %H:%M:%S %z') %>': Document 'vendor/bundle/ruby/2.1.0/gems/jekyll-3.0.5/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb' does not have a valid date in the YAML front matter.
```

We need to tell jekyll to skip the vendor directory when building our site in this environment,
by adding the following line to our `_config.yml` file.

```
exclude: [vendor]
```

The next build continues and begins to validate our generated html files. It fails on resolving
self referential anchor tags.

```
Running ["ScriptCheck", "LinkCheck", "ImageCheck"] on ["./_site"] on *.html...
Ran on 4 files!
- ./_site/2016/05/07/an-experiment-with-github-pages-and-jekyll.html
  *  linking to internal hash # that does not exist (line 25)
- ./_site/about/index.html
  *  linking to internal hash # that does not exist (line 26)
- ./_site/index.html
  *  linking to internal hash # that does not exist (line 26)
htmlproofer 3.0.5 | Error:  HTML-Proofer found 3 failures!
```

For our purposes I think it is safe to ignore these errors; we achieve this by editing our `cibuild` script and
appending `--url-ignore "#"` to our `htmlproofer` command. Perhaps using `--verbose --allow_hash_href` is deprecated now.

```
+bundle exec htmlproofer ./_site --disable-external --url-ignore "#"
```

Finally we have a successful build with ~0 exits~!

```
Running ["ScriptCheck", "LinkCheck", "ImageCheck"] on ["./_site"] on *.html...
Ran on 4 files!
HTML-Proofer finished successfully.
The command "./build/cibuild" exited with 0.
Done. Your build exited with 0.
```

To share our build status we will include a build badge in our `README.md` file.

```
[![Build Status](https://travis-ci.org/darekkowalski/darekkowalski.github.io.svg?branch=master)](https://travis-ci.org/darekkowalski/darekkowalski.github.io)
```

Looking good! Now lets implement some visitor tracking with Google Analytics. Maybe Piwik later?
After creating an account and site profile lets put the javascript in a separate
file named `analytics.html` within our `_include` directory... and then include it in
our `default.html` template, right after our `footer.html` include.

Now lets add a custom 404 page by creating a `404.html` file in our root directory.

```
---
title: 404
---
four oh four.
```

After pushing that up, notice that the 404 page works as expected, however it appears in the
sites top navigation bar. To change that we need to remove the title variable and replace it with
permalink instead.

```
-title: 404
+permalink: /404.html
```

That works. Now it is time to write this article and share it!

But before we share it, lets implement a custom top-level domain by creating a
`CNAME` file in the root directory with the domain name on the first line.

We will need to update our domains DNS records to point to GitHub servers. This can be
achieved by defining one of `ALIAS`, `ANAME` or `A` records.

For this sites purposes the apex domain `darek.dk` will use A records pointing to `192.30.252.153`
and `192.30.252.154`.

Now its time to share http://darek.dk and the self-referential first post! Do we get https with a
custom domain here? Can we utilize letsencrypt? How will we handle pull requests? Godspeed!

EDIT: There is an easter egg in this post. Funny by-product of static site generation. Can you see it?
