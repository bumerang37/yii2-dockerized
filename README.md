Yii 2 Dockerized
================

[![Latest Stable Version](https://poser.pugx.org/codemix/yii2-dockerized/v/stable.svg)](https://packagist.org/packages/codemix/yii2-dockerized)
[![Total Downloads](https://poser.pugx.org/codemix/yii2-dockerized/downloads.svg)](https://packagist.org/packages/codemix/yii2-dockerized)

A template for docker based Yii 2 applications.

 * Ephemeral container, configured via environment variables
 * Separated base image to configure all required PHP extensions and composer packages
 * Optional local configuration overrides for development/debugging (git-ignored)
 * Base scaffold code for login, signup and forgot-password actions
 * Flat configuration file structure


# How to use

First create a copy of our application template, for example with
composer:

```sh
composer create-project --no-install codemix/yii2-dockerized myproject
```

You could also just download the repo from github.

## 1. Build a base image

Your application will use its own base image which contains a bespoke
PHP runtime with all requried extensions and composer packages. You will
usually not have to update this image very often.

Before you build that image you should:

 * Set a name for your base image in `build/docker-compose.yml`
 * Optionally add more PHP extensions in `build/Dockerfile`

Then you can build the image:


```sh
cd myproject/base
docker-compose build
```

## 2. Update composer dependencies

During the first build the main composer packages e.g. for yii2 where
already added to your base image. Each time you need to add or update
packages first have to create an updated `composer.lock` and then rebuild
your base image:


```sh
cd myproject/base
# Add/udpate package information in build/composer.lock
docker-compose run --rm composer require codemix/yii2-localeurls
# Rebuild the base image
docker-compose build
```

## 3. Local development

During local development we will map the main project directory into the
base image you just built. As local environments may differ (e.g. use
different docker network settings) we usually keep `docker-compose.yml`
out of version control. Instead we provide a `docker-compose-example.yml`
with a reasonable example setup.

As the runtime configuration should happen with environment variables,
we use a `.env` file. We also keep this out of version control and
only include a `.env-example` to get developers started.


```sh
cd myproject
cp docker-compose-example.yml docker-compose.yml
cp .env-example .env
docker-compose up -d
docker-compose run --rm web ./yii migrate
```

It may take some minutes to download the required docker images. When
done, you can access the new app from [http://localhost:8080](http://localost:8080).

If you see an error about write permissions to `web/assets/` or `runtime/` it's because
the local file owner id is different from `1000` which is the `www-data` user in the container.
To fix this, try:

```
docker-compose exec chown www-data web/assets runtime
```
