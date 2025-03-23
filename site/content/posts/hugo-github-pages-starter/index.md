+++
title = "Building and hosting your own Hugo blog on GitHub"
date = "2025-03-12"
description = "A guide to building and hosting a basic blog using Hugo on your personal GitHub account and GitHub Pages"
tags = [
    "hugo",
    "blog",
    "github",
    "github-pages",
]
categories = [
    "hugo",
]
series = ["Hugo Blog"]
+++

## Introduction

I wanted to find an easy way to host some blog content that was free and all managed in Github. After some research I came across [Hugo](https://gohugo.io) which works great, but a lot of the tutorials out there are very in-depth as Hugo does cover a lot of static site use cases, so I decided to write up a quick guide on my experience in setting this up for anyone else that just wants to get up and running quickly(ish). 

This guide in particular uses a Tailwind CSS theme (ironically called [Tailwind](https://themes.gohugo.io/themes/hugo-theme-tailwind/)) as I also didn't want to bother with NPM and Dart-Sass and all that crap to just make a simple blog. 

## TLDR

- Create a GitHub account
- Create a new public repo called **username**.github.io replacing username your GitHub username 
- Clone my [repo](https://github.com/commitconfirmed/hugo-git-pages-starter) into your repo
- Update the `~/site/hugo.toml` file with your own personal details
- Update the `~/site/content/about/index.md` file with your own personal details
- See the `~/site/content/posts/` directory for an example blog bost. 
  - Create a new folder and `index.md` file to make your own blog post
- Update your repos Settings/Pages to deploy using Github Actions and create a workflow using the provided `~/.github/workflows/hugo.yml` file 
  - See the Hugo [Documentation](https://gohugo.io/host-and-deploy/host-on-github-pages/) for detailed instructions
- Commit some changes and push to Main and your blog should now be available at https://username.github.io

Supports Local, Docker Compose or GitHub Codespaces development.

## Step by Step guide

### Github

To start, you'll of course need your own [Github account](https://github.com/signup). To utilize your accounts own .github.io domain to host and display your Hugo site you will need to create a new public repository called **username**.github.io 

For example on my commitconfirmed username, I needed to use [commitconfirmed.github.io](https://github.com/commitconfirmed/commitconfirmed.github.io)

### Initial Setup

Now, using your development environment of choice clone your newly created repository and create an empty "site" directory, which is where we will be building our Hugo site. Also create a .gitignore file with the below content (which will be used later on to ignore some of the files that are automatically created from going into your repo)

```bash
# Hugo gitignores
/site/resources/
/site/public/
/site/themes/
.hugo_build.lock
```

Commit & push these changes to check that your development environment is working properly. 

### Hugo installation

From here, we have several paths to install and run Hugo based on your own personal development preferences:

- Locally on your own machine
- Locally on your own machine, using Docker Compose
- Remotely using GitHub Codespaces

#### Locally

<details>
<summary>Expand / Collapse</summary>
Follow the installation instructions for your operating system on the Hugo Website https://gohugo.io/installation/

Once installed, navigate to the previously created site directory and execute `"hugo new site ."` to create the initial content and layout of your site. From here you can also execute `"hugo server"` and follow the URL presented to make sure the basics are all working. If everything is all good you can do another git commit & push.
</details>

#### Docker Compose

<details>
<summary>Expand / Collapse</summary>
If using docker, create a Dockerfile file and docker-compose.yml file in the base directory of your repository with the below content (or simply copy these files in my basic-github-hugo-blog repo). You can change the HUGO_VERSION environment variable to the latest of whatever you want.

```bash
~/Dockerfile
~/docker-compose.yml
```

<u>Dockerfile</u>

```Dockerfile
# Local development environment for Hugo
FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV HUGO_VERSION=0.145.0

RUN apt-get update -y && apt-get install -y \
wget \
&& apt-get autoremove -y \
&& apt-get clean -y \
&& rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    dpkg -i hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    rm hugo_extended_${HUGO_VERSION}_linux-amd64.deb

RUN mkdir -p /site

WORKDIR /site

ENV HUGO_CACHEDIR=/cache
ENV PATH="/var/hugo/bin:$PATH"

# Local testing if needed without compose
#COPY ./site /site
#EXPOSE 1313
#CMD ["hugo", "server", "--bind", "0.0.0.0", "--disableFastRender"]
```

<u>docker-compose.yml</u>

```yaml
services:
  hugo:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - ./site:/site
    ports: 
      - 1313:1313
    command: hugo server --bind 0.0.0.0 --disableFastRender
```

From here, execute `"docker compose up"` to start up your hugo development server. You will notice that it will fail the first time because we have not created a new Hugo site yet.

> output here

To fix this, we can simply navigate to the site directory `"cd site"` and execute `"hugo new site ."` inside our container which will create the initial files and reflect them back to our local machine as we are using the Docker Compose volume mounting functionality. From here you can quit out of the container and execute `"docker compose down"` and `"docker compose up"` again and we should now have the development server up and running: 

```bash
hugo-1  | Built in 52 ms
hugo-1  | Environment: "development"
hugo-1  | Serving pages from disk
hugo-1  | Web Server is available at http://localhost:1313/ (bind address 0.0.0.0)
hugo-1  | Press Ctrl+C to stop
```

Once you are happy with the setup, perform a git commit & push to save the files created by the Hugo new site initialization. 
</details>

#### Codespaces

<details>
<summary>Expand / Collapse</summary>
If you would prefer not to install anything at all locally, you can do *all* of your development these days on a machine in the cloud from the comfort of a web brower using [github codespaces](https://github.com/features/codespaces) 

Note that this is also free, for up to 60 hours a month (as of the time this was written) which should be more than enough unless you are a blogging machine or using codespaces for other projects.

In your [codespaces settings](https://github.com/settings/codespaces) I would recommend setting an idle time and any other personal preferences. Also while it should be set to $0 as default, it is worth checking your [budget settings](https://github.com/settings/billing/budgets) to make sure you don't get charged if you go over your 60 hours a month.

Now that the boilerplate is out of the way, to set up a codespaces environment with a Hugo server create the following folders and files in the base of your repository.

```bash
~/.devcontainer/devcontainer.json
~/.devcontainer/Dockerfile
~/.vscode/tasks.json
```

With the below contents:

<u>devcontainer.json</u>

```json
{
    "name": "Hugo Dev Container",
    "build": {
        "dockerfile": "Dockerfile"
    },
    "customizations": {
        "vscode": {
            "settings": {
                "terminal.integrated.defaultProfile.linux": "zsh"
            },
            "extensions": [
                "ms-azuretools.vscode-docker",
                "vscode.git"
            ]
        }
    },
    "forwardPorts": [1313],
    "postCreateCommand": "git submodule update --init --recursive"
}
```

<u>Dockerfile</u>

```Dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu

ENV DEBIAN_FRONTEND=noninteractive
ENV SHELL=/usr/bin/zsh
ENV HUGO_VERSION 0.145.0

RUN apt-get update -y && apt-get install -y \
wget \
&& apt-get autoremove -y \
&& apt-get clean -y \
&& rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    dpkg -i hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    rm hugo_extended_${HUGO_VERSION}_linux-amd64.deb

ENV HUGO_CACHEDIR=/cache
ENV PATH="/var/hugo/bin:$PATH"
```

<u>tasks.json</u>

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Run Hugo Server",
            "type": "shell",
            "command": "cd site && sudo hugo server --bind 0.0.0.0 --disableFastRender",
            "problemMatcher": [],
            "isBackground": true
        }
    ]
}
```

Perform a git commit & push so these files are uploaded to our repository. From here navigate to [codespaces](https://github.com/codespaces), select "New Codespace" and then select your repository. The rest of the settings should default to something like the below:

- Branch: Main
- Dev container configuration: Hugo Dev Container
- Region: (your region)
- Machine Type: 2-Core

If everything looks good, click "Create Codespace", which should open up a new browser tab with a web version of Visio Studio Code with your repo ready to go.

You may see an error on first setup as we haven't installed a theme yet, but we will cover that next. As with the other methods, from the provided terminal enter the site directory `"cd site"` and initialize your site using the `"sudo hugo new site ."` command (you don't need to do this if you've used one of the other methods already)

From here click the top bar or press <kbd><kbd>CTRL</kbd>+<kbd>SHIFT</kbd>+<kbd>P</kbd></kbd> and select/type "Run Task" and then "Run Hugo Server" which should start up your Hugo server.

In the ports tab you can simply click the globe icon next to the forwarded port and that should open up an additional tab which will display your Hugo website.

Once you are done, you can press <kbd><kbd>CTRL</kbd>+<kbd>SHIFT</kbd>+<kbd>P</kbd></kbd> and type "Stop current codespace" to stop the codespace, or just close the tab and then stop the codespace from the [codespaces](https://github.com/codespaces) page. 
</details>

### Tailwind theme installation

Now that your Hugo environment(s) is setup, you can install the Tailwind theme. To do this navigate to the root directory of our Hugo side (~/site) and add the Tailwind theme as a Git submodule

```bash
git submodule add https://github.com/tomowang/hugo-theme-tailwind.git themes/tailwind
```

If this has been done successfully, you should now have a ` ~/.gitmodules ` file created with the below content

<u>.gitmodules</u>

```bash
[submodule "site/themes/tailwind"]
	path = site/themes/tailwind
	url = https://github.com/tomowang/hugo-theme-tailwind.git
```

To use this theme, we will add it to our hugo.toml file along with a few other things to set up our site.

### Hugo TOML configuration

When you first ran `"hugo new site ."` a barebones ` hugo.toml ` file should have been created. Edit this file and apply the tailwind theme, as well as set up the initial content of your blog.

<u>hugo.toml</u>

```toml
baseURL = "https://yoursite.github.io"
title = "my blog"
author = "Your Name"
copyright = "Your Name"
pagination.pagerSize = 10
languageCode = "en"
theme = "tailwind"
enableRobotsTXT = true
enableEmoji = true

[markup]
  _merge = "deep"

[params]
  keywords = "some, keywords, here"
  subtitle = "blog subtitle"
  contentTypeName = "posts"
  showAuthor = true

  [params.author]
  name = "Your Name"
  email = "user@example.com"

  [params.header]
    logo = "logo.svg"
    title = " site title"

  [params.footer]
    since = 2025
    poweredby = true

    [[params.social_media.items]]
      enabled = true
      title = 'Bluesky'
      icon = 'brand-bluesky'
      link = 'https://bsky.app/profile/yourname.bsky.social'
  
    [[params.social_media.items]]
      enabled = true
      title = 'LinkedIn'
      icon = 'brand-linkedin'
      link = 'https://www.linkedin.com/in/yourname'
  
    [[params.social_media.items]]
      enabled = true
      title = 'Github'
      icon = 'brand-github'
      link = 'https://github.com/yourname'

[menu]

  [[menu.main]]
    identifier = "post"
    name = "Posts"
    pageRef = "/posts"
    weight = 10

  [[menu.main]]
    identifier = "about"
    name = "About"
    pageRef = "/about"
    weight = 20

[taxonomies]
category = "categories"
tag = "tags"
series = "series"
```

You can see a full list of site configuration options in the [Hugo Documentation](https://gohugo.io/configuration/) and the theme settings in the [Theme Documentation](https://themes.gohugo.io/themes/hugo-theme-tailwind/)

Save the file. If your hugo server is running, it should automatically detect that the file has changed and it will automatically rebuild the site

```bash
hugo-1  | Change of config file detected, rebuilding site (#1).
hugo-1  | 2025-03-22 09:35:00.246 +0000
hugo-1  | Web Server is available at http://localhost:1313/ (bind address 0.0.0.0)
hugo-1  | Rebuilt in 90 ms
```

Browse to your web server and you will see that the theme should now be applied with the details in your `hugo.toml` file.

### Creating Posts & About section

To create a new blog post, all you need to do is create a new folder in `~/site/content/posts/` and create an `index.md` file. Hugo uses a concept called front matter to populate the metadata of your post across your site. A full list of fields you can use is available [here](https://gohugo.io/content-management/front-matter/). A basic `index.md` file with front matter and content is below:

<u>index.md</u>

```
+++
title = "Building and hosting your own Hugo blog on GitHub"
date = "2025-03-12"
description = "A guide to building and hosting a basic blog using Hugo on your personal GitHub account and GitHub Pages"
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
+++

## Blog header

blog text
```

Once saved, Hugo will again automatically rebuild the site for you and you should see your blog posts, and assosiated tags/categories/series on the front page.

```bash
hugo-1  | Change detected, rebuilding site (#2).
hugo-1  | 2025-03-22 10:32:17.248 +0000
hugo-1  | Source changed /posts/introduction/index.md
hugo-1  | Web Server is available at http://localhost:1313/ (bind address 0.0.0.0)
hugo-1  | Total in 56 ms
```

There are several examples of posts provided in the Tailwind repository [here](https://github.com/tomowang/hugo-theme-tailwind/tree/main/exampleSite/content/post) to suit your own needs.

### Hosting on GitHub Pages

Once you are happy with your content and how everything looks, it's finally time to deploy your site! There are plenty of [deployment options](https://gohugo.io/host-and-deploy/) but for this guide we will use GitHub pages as it is free, comes with a domain name already and will be automatically updated every time we do a commit & push. 

For this we will follow the [provided guide](https://gohugo.io/host-and-deploy/host-on-github-pages/) from Hugo with a few tweaks.

First, append the below to your `hugo.toml` file as per the guide:

```toml
[caches]
  [caches.images]
    dir = ':cacheDir/images'
```

Next, create a `"~/.github/workflows"` directory and create a `hugo.yml` file inside it, with the contents in the [guide](https://gohugo.io/host-and-deploy/host-on-github-pages/). 

For this to work with our setup, we need to make a couple of tweaks. First, update the "Build with Hugo" step to include the --source as our ./site

```yaml
      - name: Build with Hugo
        run: |
          hugo \
            --gc \
            --minify \
            --source ./site \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
```

Second, as we don't use Dart Sass we can remove that step from our file

```yaml
      - name: Install Dart Sass
        run: sudo snap install dart-sass
```

Finally, update the "Upload artifact" step to also reference our /site directory

```yaml
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./site/public
```

Commit and push these changes. 

- Update your repos Settings/Pages to deploy using Github Actions and create a workflow using the `~/.github/workflows/hugo.yml` file 
  - See the Hugo [Documentation](https://gohugo.io/host-and-deploy/host-on-github-pages/) for detailed instructions
- Commit some changes and push to Main and your blog should now be available at https://username.github.io

## Extras

### Giscus

Hugo and this theme support comments using giscus. To enable this follow the instructions at https://giscus.app/

When installing the app set it to your username.github.io repository. Once done just enter your repository and it should validate

<img src=./images/hugo-giscus-1.png alt="Giscus Settings #1">

You can ignore the rest of the settings and scroll down to the enable giscus section where you can retrieve your repo, repoID and categoryID

<img src=./images/hugo-giscus-2.png alt="Giscus Settings #1">

Insert the below into your `hugo.toml` params section, replacing the repo, repoID and categoryID with your own

```toml
  [params.giscus]
    enabled = true
    repo = "username/username.github.io"
    repoID = "R_#####"
    category = "Announcements"
    categoryID = "DIC_####"
    mapping = "pathname"
    strict = "0"
    reactionsEnabled = "1"
    emitMetadata = "0"
    inputPosition = "bottom"
    lang = "en"
```

### Google Analytics

Hugo and this theme support Google Analytics. To enable this create an account at https://analytics.google.com/ then set up a tag for your github.io site. Once setup you'll have a G-XXXXXXXXXX tag which you can put into your `hugo.toml` file

<u>hugo.toml</u>

```toml
[services]
  [services.googleAnalytics]
    ID = "G-XXXXXXXXXX"
```

