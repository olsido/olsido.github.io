---
layout: post
title:  "Installing Jekyll on MacOS Catalina"
date:   2020-10-30
categories: Jekyll MacOS Catalina Ruby Gem
---
Jekyll installation on MacOS Catalina is not as intuitive as it is written in widely available documentation.

MacOS Catalina comes with all prerequisites for Jekyll pre-bundled (see [Jekyll Installation](https://jekyllrb.com/docs/installation/)).

The versions match those in the documentation, however after doing all the steps in [Jekyll on MacOS](https://jekyllrb.com/docs/installation/macos/), I got stuck with the following command:

```shell
gem install --user-install bundler jekyll
```

It was erroring out and asking me to look various log files, from which I got this main error complaining about OpenSSL:

_Package configuration for openssl is not found_

I tried installing / reinstalling various OpenSSL versions to no avail, and finally what helped was just installing the latest version of `ruby`:

```shell
curl -sSL https://get.rvm.io | bash -s stable
rvm requirements
rvm install 2.7.2
```

That got me past the installation.

Next, GitHub says we should update _github-pages_ often, because they are actively working on it (see [Testing your GitHub Pages site locally with Jekyll](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/testing-your-github-pages-site-locally-with-jekyll)). They suggest to execute this command:

```shell
bundle update github-pages
```

However the output from the command suggested that _github-pages_ is not installed:

_Could not find gem 'github-pages'._

So I had to install it. To do that - open `Gemfile` in your project root and add the following lines (if they don't exist):

```shell
source 'https://rubygems.org'
gem 'github-pages'
gem 'rdiscount'
```

(RDiscount converts documents in Markdown syntax to HTML.)

After you update Gemfile - you can run:

```shell
bundle install
```

At this point I was able to run Jekyll and create this first post on my new blog! :-)

```shell
bundle exec jekyll serve
```
\
**P.S**. Something to note also is that some people claim the following environment variable settings helped them. I did them, not sure if it was part of the solution, but sure worth considering if you are fighting with a similar issue:

```shell
export PATH="/usr/local/opt/ruby/bin:$PATH"
export LDFLAGS="-L/usr/local/opt/ruby/lib"
export CPPFLAGS="-I/usr/local/opt/ruby/include"
export PKG_CONFIG_PATH="/usr/local/opt/ruby/lib/pkgconfig"
```