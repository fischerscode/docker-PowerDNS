ARG VERSION=
FROM powerdns/pdns-auth-45:latest

USER root

# Reusable layer for base update - Should be cached from builder
RUN apt-get update && apt-get -y dist-upgrade && apt-get clean

# Ensure python3 and jinja2 is present (for startup script), and sqlite3 (for db schema), and tini (for signal management),
#   and vim (for pdnsutil edit-zone) 

RUN apt update && \
    apt install -y gnupg lsb-release wget python3-pip &&\
    pip3 install --no-cache-dir envtpl &&\
    wget https://dev.mysql.com/get/mysql-apt-config_0.8.18-1_all.deb &&\
    echo 4 | dpkg -i mysql-apt-config_0.8.18-1_all.deb &&\
    apt update &&\
    apt install -y mysql-client &&\
    apt clean

COPY pdns.conf /etc/powerdns/pdns.conf

# https://dev.mysql.com/get/mysql-apt-config_0.8.18-1_all.deb

USER pdns

# ENV VERSION=4.4 \
ENV    PDNS_guardian=yes \
    PDNS_setuid=pdns \
    PDNS_setgid=pdns \
    PDNS_launch=gmysql

EXPOSE 53 53/udp

COPY pdns.conf.tpl /
COPY --chmod=0755 docker-entrypoint.sh /

ENTRYPOINT [ "/docker-entrypoint.sh" ]

CMD [ "/usr/local/sbin/pdns_server" ]