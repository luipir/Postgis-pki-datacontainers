FROM busybox
MAINTAINER Luigi Pirelli<lpirelli@boundlessgeo.com>

# create postgres user and groups for the data container
# guid and uid are set the same for the kartooza/postgis container
RUN addgroup -g 111 postgres \
    && adduser -H -D -G postgres -u 106 postgres

# create the volume path where to store configuration
RUN mkdir -p /etc/postgresql/9.4/main

# populate configuration
ADD ./conf /etc/postgresql/9.4/main

# change owner to all copied files
RUN chown -R postgres:postgres /etc/postgresql

# check permission to the server key
RUN chmod 0400 /etc/postgresql/9.4/main/ssl/private/localhost_ssl_key.pem

# specify the volume to export 
# I'm not sure it's necessary because I've always to set -v in the data-container
VOLUME /etc/postgresql/9.4/main/
# set running user. hopefully sould set the user when mounting /etc/postgresql/9.4/main/ as volume from other containers
USER postgres
