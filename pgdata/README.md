# Docker data container to store PGDATA with pki configured users
This data-container is configured to setup PGDATA for kartooza/postgis container

It is configured with PKI enabled users available here:
* [1] https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys

## How to build data container
Build configuration base image to create data-container:
* `cd` in the path where is located "Dockerfile"
* execute $> `docker build -t postgis-pgdata-image .`

This will generate an image tagged as `postgis-pgdata-image`

## Create configuration data-container instance
Create the data-container exposing `/var/lib/postgresql/9.4/main` to be used in other containers with `--volumes-from` option:
* $> `docker run -v /var/lib/postgresql/9.4/main --name postgis-pgdata-container postgis-pgdata-image`

## Run postgis instance using PKI configuration from data container
Run postgis getting config data from the volume of the data-container
* $> `docker run --rm --volumes-from postgis-pgdata-container --volumes-from postgis-config-container --name "postgis" -p 25432:5432 -t kartoza/postgis`

## Enable client to connect using SSL certificates
Detailes can be found in the following document:
* https://github.com/boundlessgeo/docker/blob/master/Community/Postgis-pki-datacontainers/conf/README_HowtoSetupClientCert.md
