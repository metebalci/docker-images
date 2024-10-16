
# docker-images

The images are published to dockerhub, with `-` replaced with `:`.

## launchpad-focal

It is to build packages for launchpad on focal.

## openldap-2.5.17

OpenLDAP 2.5.17 built from source on Ubuntu 22.04 with:

- slapd, `--enable-slapd`
- crypt, `--enable-crypt`
- memberof, `--enable-memberof`
- sasl, `--with-cyrus-sasl` (`libsasl2-dev`)
- tls with gnutls, `--with-tls=gnutls` (`libgnutls28-dev`)

To use, bind mount /opt/openldap/etc/openldap/slapd.conf to your slapd.conf, and mount TLS (e.g. /etc/letsencrypt/...) and data directory (e.g. /opt/openldap/var/openldap-data) as defined in slapd.conf

The entrypoint is `/opt/openldap/libexec/slapd` and CMD is set to default params `-d -1 -h ldap://0.0.0.0:389`. Override CMD to change debug levels and ports.

Example run:

```
docker run \
  --name openldap \
  --hostname ldap \
  --restart=unless-stopped \
  -p 389:389 \
  -p 636:636 \
  -v /mnt/containers/openldap/etc/openldap/slapd.conf:/opt/openldap/etc/openldap/slapd.conf \
  -v /mnt/containers/openldap/var/openldap-data:/opt/openldap/var/openldap-data \
  -v /etc/letsencrypt/live/example.com:/etc/letsencrypt/live/example.com \
  -v /etc/letsencrypt/archive/example.com:/etc/letsencrypt/archive/example.com \
  metebalci/openldap:2.5.17 \
  -d 224 \
  -h "ldap://0.0.0.0:389 ldaps://0.0.0.0:636"
```
