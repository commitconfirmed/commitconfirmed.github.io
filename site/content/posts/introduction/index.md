+++
title = "Building your own Hugo site on github"
date = "2025-03-12"
description = "A modern guide to building and hosting a basic blog using Hugo on your personal github account"
tags = [
    "hugo",
    "blog",
    "github",
    "github-pages",
]
categories = [
    "hugo",
]
series = ["Hugo"]
aliases = ["/building"]
+++

## Introduction

Test

Test in codespaces number 2

## TLDR

Just clone my basic-hugo-github-blog and modify to your own personal preferences

## Step by Step guide

### Github

To start, you'll of course need your own [Github account](https://github.com/signup). To utilize your accounts own .github.io domain to host and display your Hugo site you will need to create a new public repository called **username**.github.io 

For example on my commitconfirmed username, I needed to use [commitconfirmed.github.io](https://github.com/commitconfirmed/commitconfirmed.github.io)

### Initial Setup

Now, using your development environment of choice clone your newly created repository and create a "site" directory, which is where we will be building our Hugo site. Also create a .gitignore file with the below content (which will be used later on to ignore some of the files that are automatically created from going into your repo)

```bash
# Hugo gitignores
/site/resources/
/site/public/
/site/themes/
.hugo_build.lock
```

