# Docker data container to PKI enable a postgis container
This data-container is configured to setup pki for kartooza/postgis container

It use server certificate and CA from:
* [1] https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys

all certificats and keys are in the ssl path.

## How to build data container
Build configuration base image to create data-container:
* `cd` in the path where is located "Dockerfile"
* execute $> `docker build -t postgis-config-image .`

This will generate an image tagged as `postgis-config-image`

## Create configuration data-container instance
Create the data-container exposing `/etc/postgresql/9.4/main` to be used in other containers with `--volumes-from` option:
* $> `docker run -v /etc/postgresql/9.4/main/ --name postgis-config-container postgis-config-image`

## Run postgis instance using PKI configuration from data container
Run postgis getting config data from the volume of the data-container
* $> `docker run --rm -v <your permanent DBDATA >:/var/lib/postgresql/9.4/main --volumes-from postgis-config-container	--name "postgis" -p 25432:5432 -t kartoza/postgis`

## Enable client to connect using SSL certificates
Detailes can be found in the following document:
* https://github.com/boundlessgeo/docker/blob/master/Community/Postgis-pki-datacontainers/conf/README_HowtoSetupClientCert.md


