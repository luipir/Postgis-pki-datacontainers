# Howto setup a certified psql connection to a PKI enabled postgres server
Client and server certificates are get from QGIS pki testdata:
* [1] https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys

To allow psql to use client certificate you have to install cetts in `~/.postgresql` directory.

In case useing pgAdmin3 you can set certs directly in the connection setting

Server certificate are:
* `localhost_ssl_cert.pem`
* `localhost_ssl_key.pem`

and Server CA:
* `chains_subissuer-issuer-root_issuer2-root2.pem`

CA is set to allow certified connection to all client certs in [1](https://github.com/qgis/QGIS/tree/master/tests/testdata/auth_system/certs_keys)

## HOWTO CONFIGURE CLIENT TO CONNECT AS USER `Fra`
`Fra` user has cert and key:
* `fra_cert.pem`
* `fra_key.pem`

and certified CommonName (CN) as `Fra`. CN can be verified dumping certificates
as text using command:
* $> `openssl x509 -taxt -in <yout cert>`

to allow psql to connect using `Fra` certificate, copy cert and key in `~/.postgresql`:
* `fra_cert.pem` in `~/.postgresql/postgresql.crt`
* `fra_key.pem` in `~/.postgresql/postgresql.key`

give correct permission to `~/.postgresql/postgresql.key`:
* $> `chmod 0600 ~/.postgresql/postgresql.key`

      equivalent to:

* $> `chmod go-rwx ~/.postgresql/postgresql.key`
* $> `chmod u+rw ~/.postgresql/postgresql.key`

More strictly you can give `0400` permission equivalent to `u+r` only

Then copy common CA in `~/.postgresql`:
* `chains_subissuer-issuer-root_issuer2-root2.pem` as `~/.postgresql/root.crt`

## HOWTO CONNECT USING PSQL AND USER `Fra`
connect to PKI enabled postgresql server with the following command:
* $> `psql -U Fra -h <serve ip or hostname> -d <db name>`
      
      or specifying connection mode

* $> `psql -U Fra postgresql://<serve ip or hostname>:5432/<db name>?sslmode=verify-ca`

In both cases **NO PWD** would be asked to the client.

Connection sslmode can be `verify-ca` or `require`.

Sslmode `verify-full` need that server hostname would be the same name specified in sever cert CommonName (CN). In this case  would be `localhost`.

Beaware that `-u` option in psql command HAVE to specify the same string specified in the client certificate CommonName (CN) respecting Upper and Lower cases.

Obviously server have to be configured to have `Fra` user with login permission. 

### SSL Connection troubleshoting
please refer to the following link:
* http://www.pontifier.com/?p=45


