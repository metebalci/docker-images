FROM ubuntu:22.04

LABEL org.opencontainers.image.authors  = "Mete Balci <metebalci@gmail.com>"
LABEL org.opencontainers.image.licenses = "GPL-3.0-or-later"
LABEL org.opencontainers.image.source   = "https://github.com/metebalci/docker-images"
LABEL org.opencontainers.image.url      = "https://github.com/metebalci/docker-images"
LABEL org.opencontainers.image.title    = "OpenLDAP 2.5.17"

RUN apt-get update

# install required packages
# wget to download source package
# build-essential for compiler tools
# file is used during ./configure
# gnutls and sasl2 is required for building
# make uses soelim which is in groff-base
RUN apt-get -y install wget build-essential file libgnutls28-dev libsasl2-dev groff-base

# download the source package to opt and extract
WORKDIR /opt
RUN wget https://www.openldap.org/software/download/OpenLDAP/openldap-release/openldap-2.5.17.tgz
RUN tar zxvf openldap-2.5.17.tgz

# configure, build, test and install
WORKDIR /opt/openldap-2.5.17
RUN ./configure --prefix=/opt/openldap --enable-slapd --enable-crypt --enable-memberof --with-cyrus-sasl --with-tls=gnutls
RUN make depend
RUN make
# make test takes time, no need to run this everytime during development
# RUN make test
RUN make install

# ldap=389, ldaps=636
EXPOSE 389
EXPOSE 636

WORKDIR /opt/openldap
# without any -d, slapd forks
ENTRYPOINT ["/opt/openldap/libexec/slapd", "-d", "-1", "-h", "ldap://0.0.0.0:389"]
