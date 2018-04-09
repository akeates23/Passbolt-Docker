```
       ____                  __          ____          .-.
      / __ \____  _____ ____/ /_  ____  / / /_    .--./ /      _.---.,
     / /_/ / __ `/ ___/ ___/ __ \/ __ \/ / __/     '-,  (__..-`       \
    / ____/ /_/ (__  |__  ) /_/ / /_/ / / /_          \                |
   /_/    \__,_/____/____/_,___/\____/_/\__/           `,.__.   ^___.-/
                                                         `-./ .'...--`
  The open source password manager for teams                `'
  (c) 2018 Passbolt SARL
  https://www.passbolt.com
```
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/0de4eaf7426944769a70a2d727a9012b)](https://www.codacy.com/app/passbolt/passbolt_docker?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=passbolt/passbolt_docker&amp;utm_campaign=Badge_Grade)
[![Docker Pulls](https://img.shields.io/docker/pulls/passbolt/passbolt.svg?style=flat-square)](https://hub.docker.com/r/passbolt/passbolt/tags/)
[![GitHub release](https://img.shields.io/github/release/passbolt/passbolt_docker.svg?style=flat-square)](https://github.com/passbolt/passbolt_docker/releases)
[![license](https://img.shields.io/github/license/passbolt/passbolt_docker.svg?style=flat-square)](https://github.com/passbolt/passbolt_docker/LICENSE)
[![Twitter Follow](https://img.shields.io/twitter/follow/passbolt.svg?style=social&label=Follow)](https://twitter.com/passbolt)

# What is passbolt?

Passbolt is a free and open source password manager that allows team members to
store and share credentials securely.

# Usage

Users are encouraged to use [official docker image from the docker hub](https://hub.docker.com/r/passbolt/passbolt/).

## Start passbolt instance

Passbolt requires mysql to be running. The following example use mysql official
docker image with the default passbolt credentials.

```bash
$ docker run -e MYSQL_ROOT_PASSWORD=<root_password> \
             -e MYSQL_DATABASE=<mysql_database> \
             -e MYSQL_USER=<mysql_user> \
             -e MYSQL_PASSWORD=<mysql_password> \
             mysql
```

Then you can start passbolt just by providing the database container ip in the
`db_host` environment variable.

```bash
$ docker run --name passbolt \
             -e DATASOURCES_DEFAULT_HOST=<mysql_container_host> \
             -e DATASOURCES_DEFAULT_PASSWORD=<mysql_password> \
             -e DATASOURCES_DEFAULT_USERNAME=<mysql_user> \
             -e DATASOURCES_DEFAULT_DATABASE=<mysql_database> \
             -e APP_FULL_BASE_URL=https://mydomain.com \
             passbolt/passbolt:develop-debian
```

Once the container is running create your first admin user:

```bash
$ docker exec passbolt su -m -c "/var/www/passbolt/bin/cake passbolt register_user -u your@email.com -f yourname -l surname -r admin" -s /bin/sh www-data
```

This registration command will return a single use url required to continue the
web browser setup and finish the registration. Your passbolt instance should be
available browsing `https://yourdomain.com`

# Configure passbolt

## Environment variables reference

Passbolt docker image provides several environment variables to configure different aspects:

| Variable name                       | Description                      | Default value       |
| ----------------------------------- | -------------------------------- | ------------------- |
| APP_FULL_BASE_URL                   | Passbolt base url                | false |
| DATASOURCES_DEFAULT_HOST            | Database hostname                | localhost |
| DATASOURCES_DEFAULT_PORT            | Database port                    | 3306 |
| DATASOURCES_DEFAULT_USERNAME        | Database username                | '' |
| DATASOURCES_DEFAULT_PASSWORD        | Database password                | '' |
| DATASOURCES_DEFAULT_DATABASE        | Database name                    | '' |
| EMAIL_TRANSPORT_DEFAULT_CLASS_NAME  | Email classname                  | Smtp |
| EMAIL_DEFAULT_FROM                  | From email address               | you@localhost |
| EMAIL_DEFAULT_TRANSPORT             | Sets transport method            | default |
| EMAIL_TRANSPORT_DEFAULT_HOST        | Server hostname                  | localhost |
| EMAIL_TRANSPORT_DEFAULT_PORT        | Server port                      | 25 |
| EMAIL_TRANSPORT_DEFAULT_TIMEOUT     | Timeout                          | 30 |
| EMAIL_TRANSPORT_DEFAULT_USERNAME    | Username for email server auth   | null |
| EMAIL_TRANSPORT_DEFAULT_PASSWORD    | Password for email server auth   | null |
| EMAIL_TRANSPORT_DEFAULT_CLIENT      | Client                           | null |
| EMAIL_TRANSPORT_DEFAULT_TLS         | Set tls                          | null |
| EMAIL_TRANSPORT_DEFAULT_URL         | Set url                          | null |
| GNUPGHOME                           | path to gnupghome directory      | /home/www-data/.gnupg |
| PASSBOLT_KEY_LENGTH                 | Gpg desired key length           | 2048 |
| PASSBOLT_SUBKEY_LENGTH              | Gpg desired subkey length        | 2048 |
| PASSBOLT_KEY_NAME                   | Key owner name                   | Passbolt default user |
| PASSBOLT_KEY_EMAIL                  | Key owner email address          | passbolt@yourdomain.com |
| PASSBOLT_KEY_EXPIRATION             | Key expiration date              | 0, never expires |
| PASSBOLT_GPG_SERVER_KEY_FINGERPRINT | GnuPG fingerprint                | null |
| PASSBOLT_GPG_SERVER_KEY_PUBLIC      | Path to GnuPG public server key  | /var/www/passbolt/config/gpg/serverkey.asc |
| PASSBOLT_GPG_SERVER_KEY_PRIVATE     | Path to GnuPG private server key | /var/www/passbolt/config/gpg/serverkey_private.asc |
| PASSBOLT_REGISTRATION_PUBLIC        | Defines if users can register    | false |
| PASSBOLT_SSL_FORCE                  | Redirects http to https          | true |
| PASSBOLT_SECURITY_SET_HEADERS       | Send CSP Headers                 | true |
| SECURITY_SALT                       | CakePHP security salt            | __SALT__ |

For more env variables supported please check [default.php](https://github.com/passbolt/passbolt_api/blob/master/config/default.php)
For more env variables supported please check [app.default.php](https://github.com/passbolt/passbolt_api/blob/master/config/app.default.php)

### Configuration files

What if you already have a set of gpg keys and custom configuration files for passbolt?
It it possible to mount the desired configuration files as volumes.

* /var/www/passbolt/config/app.php
* /var/www/passbolt/config/passbolt.php
* /var/www/passbolt/config/gpg/serverkey.asc
* /var/www/passbolt/config/gpg/serverkey_private.asc
* /var/www/passbolt/app/webroot/img/public/images

### SSL certificate files

It is also possible to mount a ssl certificate on the following paths:

* /etc/ssl/certs/certificate.crt
* /etc/ssl/certs/certificate.key

### docker-compose

Usage:

```
$ docker-compose up
```

# Requirements:

* rng-tools or haveged are required on host machine to speed up entropy generation on containers.
This way gpg key creation on passbolt container will be faster.
* mysql >= 5.6
