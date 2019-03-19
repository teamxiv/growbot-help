---
title: GrowBot Installation
---

# GrowBot Installation

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

1. Run `apt install -y nginx`
1. Create the `growbot` nginx config file inside `/etc/nginx/sites-available`
  1. `$ vim /etc/nginx/site-available/growbot`
  1. Paste the following content:
      ```nginx
      server {
              listen 80;

              server_name app.growbot.tardis.ed.ac.uk;

              location / {
                      root /srv/growbot;
              }
      }

      server {
              listen 80;

              server_name api.growbot.tardis.ed.ac.uk;

              location / {
                      proxy_pass http://localhost:8080;
              }

              location /stream {
                      proxy_pass http://localhost:8080;
                      proxy_set_header Upgrade $http_upgrade;
                      proxy_set_header Connection $http_connection;
                      proxy_set_header Origin '';
                      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              }
      }
      ```

## Get the webapp online

## Set up the database

## Deploy the API

## Set up HTTPS



## Verify everything works
