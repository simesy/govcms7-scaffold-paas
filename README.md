# GovCMS Project Scaffolding

## Known Issues

* This repository is still a Work-in-Progress, and may be subject to slight alterations

## Requirements and Preliminary Setup

* [Docker](https://docs.docker.com/install/) - Follow documentation at https://docs.amazee.io/local_docker_development/local_docker_development.html to configure local development environment.

* [Mac/Linux](https://docs.amazee.io/local_docker_development/pygmy.html) - Make sure you don't have anything running on port 80 on the host machine (like a web server):

        gem install pygmy
        pygmy up

* [Windows](https://docs.amazee.io/local_docker_development/windows.html):    

        git clone https://github.com/amazeeio/amazeeio-docker-windows amazeeio-docker-windows; cd amazeeio-docker-windows
        docker-compose up -d; cd ..

* [Ahoy (optional)](http://ahoy-cli.readthedocs.io/en/latest/#installation) - The commands are listed in `.ahoy.yml` all include their docker-compose versions for use on Windows, or on systems without Ahoy.

## Project Setup

1. Checkout project repo and confirm the path is in Docker's file sharing config (https://docs.docker.com/docker-for-mac/#file-sharing):

        Mac/Linux: git clone https://www.github.com/govcms/govcms7-scaffold-paas.git {INSERT_PROJECT_NAME} && cd $_
        Windows:   git clone https://www.github.com/govcms/govcms7-scaffold-paas.git {INSERT_PROJECT_NAME}; cd {INSERT_PROJECT_NAME}

2. Build and start the containers:

        Mac/Linux:  ahoy up
        Windows:    docker-compose up -d

3. Install GovCMS:

        Mac/Linux:  ahoy install
        Windows:    docker-compose exec -T test drush si -y govcms

4. Login to Drupal:

        Mac/Linux:  ahoy login
        Windows:    docker-compose exec -T test drush uli

## Commands

Additional commands are listed in `.ahoy.yml`, or available from the command line `ahoy -v`

## Databases

The GovCMS projects have been designed to be able to import a nightly copy of the latest `master` branch database in two ways:

1: Using the GitLab container registry nightly backup
* these instructions are for https://projects.govcms.gov.au/{org}/{project}/container_registry
* add a GitLab Personal Access Token with `read_registry` scope (profile/personal_access_tokens)
* `docker login gitlab-registry-production.govcms.amazee.io` (and use the PAT created above as the password)
* `ahoy up` (or the docker-compose equivalent)
* to refresh the db with a newer version, run `ahoy up` again

2: Use the backups accessible via the UI
* head to https://ui-lagoon.govcms.amazee.io/backups?name={project}-master
* click "Prepare download" for the most recent mysql backup you want - note that you will have to refresh the page to see when it is complete
* download that backup into your project folder
* `ahoy mysql-import` to import the dump you just saved

## Development

* The entire docroot folder will be mounted and served by the web-server
* Tests specific to your site can be committed to the `/tests` folders
* Do not make changes to `docker-compose.yml`, `lagoon.yml`, `.gitlab-ci.yml` or the Dockerfiles under `/.docker` - these will result in your project being unable to deploy to GovCMS PaaS

## Stage File Proxy

Stage File Proxy is already configured for use in both local development and cloud development environments. To enable:
  - Add the `stage_file_proxy` module to your codebase
  - Uncomment the relevant lines in `.docker/scripts/govcms-deploy`

This will ensure SFP is enabled on non-prod environments in Lagoon.

