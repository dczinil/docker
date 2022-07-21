FROM debian:latest

ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && \
    apt install build-essential snmp libnet-snmp-perl gettext bc dc gawk openssl libssl-dev autoconf gcc libc6 make wget unzip apache2 php libapache2-mod-php7.4 libgd-dev libdbi-perl libdbd-mysql-perl -y && \
    rm -rf /var/lib/apt/lists/*
## apt install --no-install-recommends


WORKDIR /tmp/nagios
COPY ["./nagios-core/.","/tmp/nagios"]
WORKDIR /tmp/nagios/nagioscore-nagios-4.4.6
RUN echo ServerName localhost >> /etc/apache2/apache2.conf && \
    useradd nagios && \
    useradd nagiosadmin && \
    usermod -aG nagiosadmin,www-data nagios && \
    usermod -aG nagios,www-data nagiosadmin

RUN ./configure --with-httpd-conf=/etc/apache2/sites-enabled && \
    make all && \
    make install && \
    make install-config && \
    make install-daemoninit && \
    make install-commandmode && \
    make install-webconf && \
    a2enmod rewrite && \
    a2enmod cgi && \
    htpasswd -bc /usr/local/nagios/etc/htpasswd.users nagiosadmin nagiosadmin

RUN /etc/init.d/apache2 start || \
    service apache2 start || \
    service apache2 force-reload && \
    service apache2 status

RUN /etc/init.d/nagios start || \
    service nagios start || \
    service apache2 force-reload && \
    service nagios status

WORKDIR /tmp/nagios/nagios-plugins-release-2.3.3

RUN ./tools/setup && \
    ./configure && \
    make && \
    make install && \
    service apache2 start || \
    /etc/init.d/apache2 start && \
    service nagios start || \
    /etc/init.d/nagios start && \
    chmod +x /tmp/nagios/loop.sh
CMD ["/tmp/nagios/loop.sh"]