# Dockerized Tools

[![License: WTFPL](https://img.shields.io/badge/License-WTFPL-brightgreen.svg)](http://www.wtfpl.net/about/)

My personal preference when developing is to have as few globally installed tools, libraries, or packages as reasonable.
This repository is a collection of tools that I use that expect to be globally installed, but have been mounted inside
a docker container instead. Each tool can be run by invoking the corresponding `docker run` command after the image has
been built. 

**Note**: As of December 2022, this repository is being assembled. Prior to this, my dockerization of tools has been
project related and focused on either one-time solutions to initial problems, or otherwise restricted to the exact
nature of the project in question. This repo represents my intention to stop doing one-off solutions or otherwise
recreating the wheel.

## Tools and Instructions

### Create React App
From this project root, run the following
```bash
cd create-react-app
docker build ./ -t create-react-app 
```

Then navigate to the folder in which you wish to create a new project and run
```bash
docker run -v ${PWD}:/output -e APP_NAME=boilerplate create-react-app
```
**APP_NAME**: Sets the name of the React app that is created

### Database Migrations
```bash
cd database-migrations
docker build ./ -t database-migrator
```

An example docker compose file partial consuming this image:
```yml
x-user-rdb: &rdb
  POSTGRES_USER: root
  POSTGRES_PASSWORD: password
  POSTGRES_DB: database
  POSTGRES_PORT: 5432
  POSTGRES_SCHEMA: public
  POSTGRES_HOST: rdb

services:
  rdb:
     image: postgres:15.1-alpine
     environment:
       <<: *rdb
     restart: always
     networks:
       - backend
     volumes:
       - rdb-vol:/var/lib/postgresql
     command: postgres -c 'max_connections=250'
     healthcheck:
       test: [ "CMD-SHELL", "pg_isready" ]
       interval: 1s
       timeout: 5s
       retries: 10

  rdb-migrator:
    image: database-migrator
    environment:
      <<: *rdb
      MIGRATION_COMMAND: up
    networks:
      - backend
    depends_on:
      rdb:
        condition: service_healthy
    volumes:
      - ./<path to migrations>/migrations:/migrations
```

**Note**: DO NOT omit the `MIGRATION_COMMAND` value. Goose will report a generic error that doesn't clearly indicate the issue if you leave this out. I have no shame in admitting that I spent at least 2 hour debugging this before I discovered the error that I introduced while recycling this docker image and associated script.

### OpenApi
From this project root, run the following
```bash
docker build ./openapi -t open-api-builder
```

To build a specification distributable version and docs page, run the following from within the project with the specification:
```bash
docker run -v ${PWD}/spec:/spec -v ${PWD}/docs:/docs -e SPEC_SERVICE_NAME=demo -e SPEC_FILE_NAME=main.yml open-api-builder
```

Output a template (example for `typescript-axios` template)
```bash
docker run --rm \
    -v "${PWD}/templates:/templates" \
    openapitools/openapi-generator-cli \
    author template \
    -g typescript-axios \
    -o /templates/typescript-axios
```
Refer to The OpenAPI [Generators List](https://openapi-generator.tech/docs/generators/) for the valid options for the `-g` argument.

Run generation with a specific template
```bash
docker run --rm \
    -v "${PWD}/docs/dist:/local" \
    -v "${PWD}/clients:/clients" \
    -v "${PWD}/templates:/templates" \
    openapitools/openapi-generator-cli \
    generate \
    -i /local/main.yml \
    -g typescript-axios \
    -t /templates/typescript-axios \
    -o /clients/typescript-axios
```

## Q and A

**Question**: Why don't you publish these to [Docker Hub](https://hub.docker.com/)?
Publishing these feels premature at this point in time. That is, however, a good idea.
