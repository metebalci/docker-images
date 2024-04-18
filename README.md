
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

To use, bind mount /opt/openldap/etc/openldap/slapd.conf to your slapd.conf, and mount TLS (e.g. /tls) and data directory (e.g. /opt/openldap/var/openldap-data) as defined in slapd.conf

The default entrypoint is `/opt/openldap/libexec/slapd -d -1 -h ldap://0.0.0.0:389`. Override it to change debug levels and ports.

Sample for compose:

```
services:
  ldap:
    image: metebalci/openldap:2.5.17
    hostname: ldap
    user: root    
    entrypoint:
      - /opt/openldap/libexec/slapd
      - -d
      - 224
      - -h
      - ldap://0.0.0.0:389 ldaps://0.0.0.0:636
    ports:
      - 389:389
      - 636:636
    volumes:
      - type: bind
        source: /mnt/containers/openldap/etc/openldap/slapd.conf
        target: /opt/openldap/etc/openldap/slapd.conf
      - type: bind
        source: /mnt/containers/openldap/var/openldap-data
        target: /opt/openldap/var/openldap-data
      - type: bind
        source: /etc/letsencrypt/archive/mydomain
        target: /tls
    deploy:
      replicas: 1
```
