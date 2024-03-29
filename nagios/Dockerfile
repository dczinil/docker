FROM debian:latest

ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && \
    apt install build-essential snmp libnet-snmp-perl gettext bc dc gawk openssl libssl-dev autoconf gcc libc6 make wget unzip apache2 php libapache2-mod-php7.4 libgd-dev libdbi-perl libdbd-mysql-perl iputils-ping openssh-client openssh-ser* -y && \
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

RUN mv /usr/local/nagios/etc/nagios.cfg /usr/local/nagios/etc/nagios.cfg$(date +"%d-%m-%y").bak && \
    cp /tmp/nagios/nagios.cfg /usr/local/nagios/etc/nagios.cfg && \
    chown nagios:nagios /usr/local/nagios/etc/nagios.cfg && \
    mv /etc/ssh/sshd_config /etc/ssh/sshd_config$(date +"%d-%m-%y").bak && \
    mv /tmp/nagios/sshd_config /etc/ssh/sshd_config && \
    chown nagios:users /etc/ssh/sshd_config && \
    mv /usr/local/nagios/etc/objects/commands.cfg /usr/local/nagios/etc/objects/commands.cfg$(date +"%d-%m-%y").bak && \
    mv /tmp/nagios/commands.cfg /usr/local/nagios/etc/objects/commands.cfg && \    
    chown nagios:users /usr/local/nagios/etc/objects/commands.cfg && \
    mv /tmp/nagios/mysqlmonitoring.cfg /usr/local/nagios/etc/objects/mysqlmonitoring.cfg && \
    chown nagios:users /usr/local/nagios/etc/objects/mysqlmonitoring.cfg && \
    mv /tmp/nagios/loop.sh /loop.sh

RUN /etc/init.d/apache2 start && \
    /bin/bash service apache2 start && \
    service apache2 force-reload && \
    service apache2 status

RUN service nagios force-reload && \
    service nagios status

WORKDIR /tmp/nagios/nagios-plugins-release-2.3.3

RUN ./tools/setup && \
    ./configure && \
    make && \
    make install && \
    chmod +x /loop.sh

RUN ./configure --prefix=/usr/local/nagios --with-nagios-user=nagios --with-nagios-group=nagios --with-perl=/usr/bin/perl && \
    make && \
    make install

RUN /etc/init.d/apache2 start && \
    service apache2 start && \
    service apache2 force-reload && \
    service apache2 status

RUN /etc/init.d/nagios start && \
    service nagios start && \
    service nagios force-reload && \
    service nagios status

CMD ["/loop.sh"]