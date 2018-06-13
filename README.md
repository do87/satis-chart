# Satis Helm Chart

## Introduction

This chart is used for setting up a [Satis](https://github.com/composer/satis) server on kubernetes
[Satis](https://github.com/composer/satis) is a static Composer repository generator, so this chart can be used to mirror packagist.org amongst other things

This chart is using [composer/satis](https://hub.docker.com/r/composer/satis/) (which is based on `php:7-alpine`)
And `nginx:1.13.12-alpine` to serve the static files

## Setup

### Basic auth

If you wish to protect your Satis repository with basic authentication, first create an auth file:

    htpasswd -c auth satis

and add it to your namespace:

    kubectl create secret generic satis-basic-auth --from-file=auth

- Uncomment the annotations related to basic auth in the values.yaml file

### Deploy with Helm

    helm install do87/satis-chart


## Configuration

Important values to configure / override


| Parameter                                   | Description                         | Default                                    |
| ------------------------------------------  | ----------------------------------  | -------------------------------------------|
| `nginx.resources`                           | nginx Container resources           | {}                                         |
| `nginx.config`                              | nginx Config file value             | refer to values file                       |
| `satis.resources`                           | satis Container resources           | {}                                         |
| `satis.config`                              | satis Config file (satis.json)      | refer to values file                       |
| `ingress.enabled`                           | is ingress enabled?                 | false                                      |
| `ingress.hosts`                             | ingress hosts                       | satis.domain.com                           |
| `ingress.annotations`                       | annotations                         | refer to values file                       |
| `persistent.enabled`                        | is persistency enabled?             | false                                      |

## Test deployment

After your server is up, you may test it with the following `composer.json` (assuming `satis.config` hasn't been changed)

    {
      "name": "company/service-name",
      "license": "proprietary",
      "type": "project",
      "require": {
        "phpunit/phpunit": "*"
      },
      "repositories": [
        {
          "type": "composer",
          "url": "https://username:password@satis.domain.com"
        },
        {
          "packagist": false
        }
      ]
    }

- Don't forget to update `https://username:password@satis.domain.com`
- If you don't use https, you will need to [configure composer to allow http](https://stackoverflow.com/questions/36151172/secure-http-flag-in-a-composer-json-doesnt-work)
