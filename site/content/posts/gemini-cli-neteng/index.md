+++
title = "Gemini CLI for Network Engineers"
date = "2025-07-20"
description = "A guide to using the Gemini CLI application to enhance your day to day Network Engineering tasks"
tags = [
    "ai",
    "artificial intelligence",
    "llm",
    "mcp",
    "cli",
    "gemini",
]
showComments = "true"
robots = "all"
draft = "true"
+++

## Introduction

This article will show you how to get started with the Google Gemini CLI product and integrate it into your shell of choice.  

## Installation

Simply follow the instructions on the [gemini-cli](https://github.com/google-gemini/gemini-cli) repo. I'm not much of a node.js user, so [fnm](https://github.com/Schniz/fnm) suited my needs to install this since it's more minimal.

Commands are also below (you may need to `source` your shell of choice after performing the `fnm install` command):

```bash
sudo apt-get install curl unzip
curl -o- https://fnm.vercel.app/install | bash
fnm install 22
node -v
npm -v
npm install -g @google/gemini-cli
gemini
```

From here, you can pick a theme and then sign in using your personal google account. 

## Usage

TBD
