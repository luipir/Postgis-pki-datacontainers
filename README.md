# PKI Enabled Postgis data-containers

## Step-by-step guide
Clone repo
* `#> git clone git@github.com:luipir/Postgis-pki-datacontainers.git`

create a kartoza/postgis image.
* `#> docker pull kartoza/postgis`

Create postgis pki enabled conf data container.

This data container contains postgis configuration to set SSL only connections and sets server certs and CAs
* `#> cd Postgis-pki-datacontainers/conf`
* build postgis-config-image:

        #> docker build -t postgis-config-image .

Create the data container postgis-config-container exposing /etc/postgresql/9.4/main/ volume that will be mounted as persistent data container in the kartoza/postgis
* `#> docker run -v /etc/postgresql/9.4/main/ --name postgis-config-container postgis-config-image`

Create postgis pki enabled pgdata data container.

This container is the DB data repository. More than the standard "docker" role added by kartoza/postgis has been added the following roles:

"Fra", "Ptolemy", "Gerardus", "Nicholas" all belonging to the "docker" role. This users correspond to that having certificates in https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys.
* `#> cd Postgis-pki-datacontainers/pgdata`
* build postgis-pgdata-image

        #> docker build -t postgis-pgdata-image .

* build the data container postgis-pgdata-container exposing /var/lib/postgresql/9.4/main volume that will be mounted as persistent data container in the kartoza/postgis

        #> docker run -v /var/lib/postgresql/9.4/main --name postgis-pgdata-container postgis-pgdata-image

Run the pki enabled postgis container. OSX Users have follow this [1]:
* `#> docker run --rm --volumes-from postgis-pgdata-container --volumes-from postgis-config-container --name "postgis" -t kartoza/postgis`

To connect to the server client have to set certificates.
Configure client following this guide:
* https://github.com/luipir/Postgis-pki-datacontainers/blob/master/conf/README_HowtoSetupClientCert.md

[1] Notes for OSX users
Run the pki enabled postgis container:
* `#> docker run -P --rm --volumes-from postgis-pgdata-container --volumes-from postgis-config-container --name "postgis" -t kartoza/postgis` 

In OSX and Win, docker runs in a thiny virtualbox machine called docker-machine, The `-P` option says to docker to run the image exposing the ports to the external world.
The default docker-machine is called `default` and it's IP address can be obtained with the following command:
* `#> docker-machine ip default`

This will be the IP address of the Postgis Db running using docker.
The port will be not the default `5432`, but that exposed by the docker-machine with the `-P` option. To obtain the port used to connect to db, show the port exposed by the docker-machine for the postgis container. Use the following command:
* `#> docker port postgis`
