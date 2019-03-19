---
title: GrowBot Installation
---

**Prerequisites:** Linux knowledge, a Virtual Machine running Debian 9.8 (stretch)

These instructions will walk you through:

- Installing a text editor (vim) and version control system (git)
- Installing a web server (nginx)
- Setting up LetsEncrypt using certbot (for https, which is REQUIRED)
- Building and deploying `growbot-site`
- Setting up PostgreSQL
- Building and deploying `growbot-api`

These instructions will _not_ walk you through setting up growbot-landing,
which should work out of the box with GitHub Pages.

Please refer to GitHub Pages or the Jekyll website for help with setting up `growbot-landing`.

## Prepare your virtual machine

1. Run `apt update`
1. Install vim and git using `apt install -y vim git`
1. Fix vim by following the [_vim-sensible instructions_](https://github.com/tpope/vim-sensible)

## Set up the web server

## Get the webapp online

## Set up the database

## Deploy the API

## Verify everything works
