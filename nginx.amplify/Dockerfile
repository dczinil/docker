FROM ubuntu:rolling
WORKDIR /tmp/amplify
RUN apt update && \
    apt install curl vim python2.7 -y 1> /dev/null && \
    COPY ["stub_status.conf","/etc/nginx/conft.d/"]
COPY ["install.sh","/tmp/amplify"]
RUN kill -HUP 'cat /var/run/nginx.pid' &&\
    /etc/init.d/nginx start
WORKDIR /tmp/aplify
RUN chmod +x install.sh && \
    API_KEY='4d499cbb8242514d680a04b0a9f52fb3' sh ./install.sh -y && \
    service amplify-agent start && \
    /etc/init.d/nginx reload