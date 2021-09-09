---
Host: static.grinnell.edu
Service: landing
URL: https://static.grinnell.edu
---

This document should be used to launch the `static-landing-page`, service name `landing` on host `static.grinnell.edu` as part of a `docker-traefik2-acme-host` stack.

> Note that this process should be started only AFTER the `acme` service!

## Command Sequence

```
cd ~/host/landing
docker-compose up -d; docker-compose logs
```

# static-landing-page

This project defines the landing page for Grinnell's `static.Grinnell.edu` server.

## Deploying this Site

As of 10-Jun-2020, this site is intended to be deployed using my [docker-traefik2-acme-host](https://github.com/McFateM/docker-traefik2-acme-host) approach.  A `docker container run` command is no longer used to launch [the site](https://static.grinnell.edu/) on Grinnell College's `static.Grinnell.edu` server.

### Now Using Docker-Compose

With the introduction of [Traefik v2.x](https://traefik.io) this site now relies solely on files, most importantly `docker-compose.yml`, and a single `docker-compose up -d` command, to execute a one-time application launch. On Grinnell's `static.grinnell.edu` server, after the host has been initialized (see [README.md](https://github.com/McFateM/docker-traefik2-acme-host)), the whole command sequence, executed as _administrator_, looked like this:

```
cd ~/host
git clone --recursive https://github.com/McFateM/static-landing-page landing
cd landing
docker-compose up -d
```

Since the above commands have already been run once, there should be no need to do it again. However, the pertinent portions of the process can now be specified like so:

```
cd ~/host/landing
git pull  # assumes the git remote is origin -> https://github.com/McFateM/static-landing-page
docker-compose up -d
```

## Local Development

It is recommended that you clone (or fork and clone) this repository to an OS X workstation where [Hugo](https://gohugo.io) is installed and running an up-to-date version.

My typical workflow for local development is:

```
cd ~/GitHub/
git clone https://github.com/McFateM/static-landing-page
cd static-landing-page
git checkout -b <new-branch-name>
atom .
hugo server
```

The `atom .` command opens the project in my [Atom](https://atom.io) editor, and `hugo server` launches a local instance of the site and provides a link to that site if there are no errors.  This local site will respond immediately to any changes made in Atom.

# Updating the Production Server

You can use a `./push-update.sh` command to push your changes into production.  Study the `./push-update.sh` script and corresponding `push-update-Dockerfile` configuration to see all that it does.

# An Even Easier Update

Not long ago I added the _Atom Shell Commands_ package to my _Atom_ config, added a command named **Push a Static Update**, and pointed that command at the _push_update.sh_ script that is now part of this project.

<!--
That _bash_ script, does just a few things, and it reads like this:

```bash
#!/bin/bash
cd ~/Projects/static-landing-page
perl -i.bak -lpe 'BEGIN { sub inc { my ($num) = @_; ++$num } } s/(build = )(\d+)/$1 . (inc($2))/eg' config.toml
docker image build -t landing-update .
docker login
docker tag landing-update mcfatem/stati-landing:latest
docker push mcfatem/static-landing:latest
```
The `perl...` line runs a text substitution that opens the project's `config.toml` file, parses it looking for a string that matches `build = ` followed by an integer.  The substitution increments that interger by one and puts the result back into an updated `config.toml` file.  The result is eventually the `Build 14`, or whatever number, that you see just below the site title and descriptions.

The rest of the _push_update.sh_ script is responsible for building a new docker image, logging in to _Docker Hub_, tagging the new image as the `:latest` version of _static-landing_, and pushing that new tagged image to my _Docker Hub_ account where _Watchtower_ can do its thing.
-->
