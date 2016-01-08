# Postgis-pki-datacontainers
PKI Enabled Postgis data-containers

#Step-by-step guide
1. git clone git@github.com:luipir/Postgis-pki-datacontainers.git

2. create a kartoza/postgis image.
* docker pull kartoza/postgis
 
3. create postgis pki enabled conf data container. This container contain postgis configuration to set SSL only connections and set server certs and CAs
cd docker/Community/Postgis-pki-datacontainers/conf
* build postgis-config-image:
* #> docker build -t postgis-config-image .

4. create the data container postgis-config-container exposing /etc/postgresql/9.4/main/ volume that will be mounted as persistent data container in the kartoza/postgis
* #> docker run -v /etc/postgresql/9.4/main/ --name postgis-config-container postgis-config-image

5. create postgis pki enabled pgdata data container. This container is the DB data repository. More than the standard "docker" role added by kartoza/postgis has been added the following roles:
"Fra", "Ptolemy", "Gerardus", "Nicholas" all belonging tothe "docker" role. This users correspondes to that having certificates in https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys.
cd docker/Community/Postgis-pki-datacontainers/pgdata
* build postgis-pgdata-image
** docker build -t postgis-pgdata-image .
* build the data container postgis-pgdata-container exposing /var/lib/postgresql/9.4/main volume that will be mounted as persistent data container in the kartoza/postgis
** docker run -v /var/lib/postgresql/9.4/main --name postgis-pgdata-container postgis-pgdata-image

6. Run the pki enabled postgis container
* #> docker run --rm --volumes-from postgis-pgdata-container --volumes-from postgis-config-container --name "postgis" -t kartoza/postgis

7. To connect to the server client have to set certificates
* configure client following this guide: https://github.com/boundlessgeo/docker/blob/master/Community/Postgis-pki-datacontainers/conf/README_HowtoSetupClientCert.md
