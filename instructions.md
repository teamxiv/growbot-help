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

## Prepare your virtual machine

These instructions assume your virtual machine is running Debian 9.

1. Run `$ apt update`
1. Install vim and git using `$ apt install -y make vim git`
1. Fix vim by following the [_vim-sensible instructions_](https://github.com/tpope/vim-sensible)

## Set up the web server

1. Run `$ apt install -y nginx`
1. Create the `growbot` nginx config file inside `/etc/nginx/sites-available`
  1. `$ vim /etc/nginx/site-available/growbot`
  1. Paste the following content:
      ```nginx
      server {
              listen 80;

              server_name growbot.tardis.ed.ac.uk;

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
1. Run `$ nginx -s reload` to reload the nginx configuration

## Get the webapp online


## Set up the database

1. On the remote machine, navigate to the source code directory (`cd ~/growbot-api`)
1. Install PostgreSQL 11 (see [these instructions](https://tecadmin.net/install-postgresql-server-on-ubuntu/))
1. Edit `/etc/postgresql/11/main/pg_hba.conf` (the exact path may be different depending on the PostgreSQL version)
  1. Scroll down to the bottom
  1. Change the last column for `"local" Unix domain socket connections` to `trust`
  1. Change the last column for `127.0.0.1/32` to `trust`
  1. Change the last column for `::1/128` to `trust`
1. Run `service postgresql restart`
1. Create the default superuser account
  1. Switch to the postgres user, `$ su postgres`
  1. Create the `root` postgres superuser, `$ createuser -s root`
  1. Return back to the `root` user, hit `ctrl+d`
1. Run `psql postgres` to open a postgres shell
  1. Execute `create role growbot with login;` to create a `growbot` "role" that is able to log in (so it's basically a user).      This user has no password for convenience.
  1. Hit `ctrl+d` to leave the postgres shell
1. Run `make reset_schema` to create a database, give our `growbot` user admin permissions on `db growbot_dev`, and load the database schema.

## Deploy the API

1. On your local machine, make sure Go is set up (you should have `GOROOT` and `GOPATH` set up appropriately).

    Don't have Go? Please follow the [Go installation instructions].

    You should also add `$GOPATH/bin` to your `$PATH`. This is so that any self-built binaries (such as [`goimports`](https://godoc.org/golang.org/x/tools/cmd/goimports)) are easy to run.

1. Already have Go set up? Make sure `go version` says you are running go 1.11 or later. 
1. Run `go get github.com/teamxiv/growbot-api`.
1. Navigate to `$GOPATH/src/github.com/teamxiv/growbot-api/cmd/growbot-api` and run `$ GOOS=linux go build` to cross-compile a binary for your Linux VM.
1. On the remote machine, run `git clone https://github.com/teamxiv/growbot-api.git` to clone `growbot-api` to `~/growbot-api`.
1. On your local machine, use `scp growbot-api remote:growbot-api/` to send the cross-compiled binary to `~/growbot-api/growbot-api` on the server named `remote`. Replace `remote` with the details of your remote server.
1. On your remote machine, within `~/growbot-api`, copy `config.example.yml` to `config.yml`
1. In a screen session, run `config=config.yml ./growbot-api`, and your API should be deployed on `api.growbot.tardis.ed.ac.uk` via nginx!

## Set up HTTPS



## Verify everything works

1. `https://api.growbot.tardis.ed.ac.uk` should say "404 Not Found" and nothing else. HTTPS should work correctly.
1. `https://growbot.tardis.ed.ac.uk` should have the landing page. Click `Register` and test out registration.
1. If registration works correctly and you are logged in, everything is working correctly.
