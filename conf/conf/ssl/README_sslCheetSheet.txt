# HOW SERVER KEY AND CERT CAN BE GENERATED
# Basing on CA crt and key of the QGIS Auth test suite:
# https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys
# other useful links are:
# Postgres troubleshooting matrix
# 	http://www.pontifier.com/?p=45
# How to convert .pem to .crt
# 	http://stackoverflow.com/questions/13732826/convert-pem-to-crt-and-key
#	e.g: openssl x509 -outform pem -in fra_cert.pem -out fra_cert.crt
#                                  ^^^ not der but pem  
# How to convert .pem into .key
# 	http://stackoverflow.com/questions/19979171/how-to-convert-pem-into-key
#	e.g: openssl rsa -outform der -in fra_cert.pem -out fra_key.key
#
# BTW cert.pem and key.pem can be used instead of extracting crt and key from pem
#
# most of info get from http://postgresql.nabble.com/SSL-auth-problem-td1898656.html

# generate server private key with 2048 bit 
openssl genrsa -out postgis_server_key.key 2048

# how to verify key. Would return RSA key ok
openssl rsa -noout -check -in postgis_server_key.key

# generate a request for the public key
# hitting return key to every answer => no pwd added
openssl req -new -key postgis_server_key.key -out postgis_server_key.req

# verify generated request: First line would generate "verify OK"
openssl req -in postgis_server_key.req -text -verify -noout

# generate certificate basing on request and signed by common CA with our client
openssl x509 \
	-req -in postgis_server_key.req \
	-CA ../certs/root_ca_cert.crt -CAkey ../private/root_ca_key.key -CAcreateserial
	-days 3500
	-out postgis_server_cert.crt

# how to verify and read certificate
method 1) openssl x509 -noout -text -in postgis_server_cert.crt
method 2) openssl verify -verbose -purpose any postgis_server_cert.crt

# how to verify that a cert is valid for a specified CA
# would produce the following result:
# <cert to verify>: OK
openssl verify -verbose -purpose any -CAfile <ca cert file> <cert to verify>

# client and server certificates has to be hashed and linked
# link and installation path depend on if certificate is for server or client
# more info:
# server: http://www.postgresql.org/docs/9.4/static/ssl-tcp.html
# client: http://www.postgresql.org/docs/9.4/static/libpq-ssl.html
openssl x509 -hash -in  <your cert> | head -n 1
ln -s <yout cert> <the value of the previous command>

