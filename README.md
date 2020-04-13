## Docker PHP

*These are simple PHP images that contain dependencies used in @ditceuma projects and maintained by Mark Team.*

### Supported Versions

- PHP 7.2: `DEPRECATED (The image based in PHP 7.2 version, will be not supported)`
- PHP 7.3

### How to use this image

This image was created with the purpose of allowing PHP applications to be able to connect with various types of relational databases, being them:

- MySQL
- PostgreSQL
- Oracle
- MicrosoftSQL Server
- SQLite

It also supports `Apache2` and other services, such as connecting to `SMB` servers.

**Tools**

- curl
- wget
- git
- vim
- tzdata
- zip
- unzip
- gnupg
- openssl

**Composer Tools**

- phpunit
- php_codesniffer
- php-cs-fixer
- phpmd
- behat
- phpstan

You can download this imagem in [Dockerhub](https://hub.docker.com/r/markteam/docker-php).

Or use it in `docker-compose.yml` file, as in the example below:

```
version: '3.5'
services:
  app:
    image: markteam/docker-php
    container_name: you-app-container-name
    volumes:
      - .:/var/www/app
    ports:
      - "8003:80"
```
