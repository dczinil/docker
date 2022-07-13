FROM ubuntu:rolling
COPY ["loop.sh","/"]
RUN apt update 1> /dev/null && \
    apt install apache2 nginx nginx-extras apt-utils -y 1> /dev/null && \
    mv /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default.conf$(date +%d-%m-%y).bak && \
    mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default$(date +%d-%m-%y).bak
COPY ["000-default.conf","/etc/apache2/sites-available/"]
COPY ["ports.conf","/etc/apache2/"]
COPY ["default","/etc/nginx/sites-available/"]
RUN /etc/init.d/apache2 start && \
    /etc/init.d/nginx start && \
    a2dissite 000-default && \
    a2enmod rewrite headers env dir mime

RUN /etc/init.d/apache2 start && \
    /etc/init.d/nginx start
EXPOSE 80 8080
WORKDIR /
CMD [/loop.sh]