# See https://hub.docker.com/r/phusion/baseimage/tags/
FROM phusion/baseimage:bionic-1.0.0-arm64

ENV SEAFILE_SERVER=seafile-server SEAFILE_VERSION=8.0.3

RUN apt-get update --fix-missing

# Utility tools
RUN apt-get install -y vim htop net-tools psmisc wget curl git

# For suport set local time zone.
RUN export DEBIAN_FRONTEND=noninteractive && apt-get install tzdata -y

# Nginx
RUN apt-get install -y nginx

#
RUN apt-get install -y libmysqlclient-dev libmemcached-dev zlib1g-dev

# Python3
RUN apt-get install -y python3 python3-pip python3-setuptools python3-wheel
RUN python3 -m pip install --upgrade pip && rm -r /root/.cache/pip

RUN pip3 install --timeout=3600 click termcolor colorlog pymysql \
    django==2.2.* && rm -r /root/.cache/pip

RUN pip3 install --timeout=3600 future mysqlclient Pillow pylibmc captcha jinja2 \
    sqlalchemy django-pylibmc django-simple-captcha pyjwt && \
    rm -r /root/.cache/pip


# Scripts
COPY scripts_7.1 /scripts
COPY templates /templates
COPY services /services
RUN chmod u+x /scripts/*

RUN mkdir -p /etc/my_init.d && \
    rm -f /etc/my_init.d/* && \
    cp /scripts/create_data_links.sh /etc/my_init.d/01_create_data_links.sh

RUN mkdir -p /etc/service/nginx && \
    rm -f /etc/nginx/sites-enabled/* /etc/nginx/conf.d/* && \
    mv /services/nginx.conf /etc/nginx/nginx.conf && \
    mv /services/nginx.sh /etc/service/nginx/run


# Seafile
WORKDIR /opt/seafile

# seafile-server-8.0.3-bionic-arm64v8.tar.gz
# https://github.com/haiwen/seafile-rpi/releases/download/v8.0.3/seafile-server-8.0.3-bionic-arm64v8.tar.gz

RUN mkdir -p /opt/seafile/ && cd /opt/seafile/ && \
    wget https://github.com/haiwen/seafile-rpi/releases/download/v${SEAFILE_VERSION}/seafile-server-${SEAFILE_VERSION}-bionic-arm64v8.tar.gz && \
    tar -zxvf seafile-server-${SEAFILE_VERSION}-bionic-arm64v8.tar.gz && \
    rm -f seafile-server-${SEAFILE_VERSION}-bionic-arm64v8.tar.gz

# For using TLS connection to LDAP/AD server with docker-ce.
RUN find /opt/seafile/ \( -name "liblber-*" -o -name "libldap-*" -o -name "libldap_r*" -o -name "libsasl2.so*" \) -delete


EXPOSE 80


CMD ["/sbin/my_init", "--", "/scripts/start.py"]
