FROM powerdns/pdns-auth-46:4.6.0

USER root

RUN apt update && \
    apt install -y gnupg lsb-release wget python3-pip &&\
    pip3 install --no-cache-dir envtpl &&\
    wget https://dev.mysql.com/get/mysql-apt-config_0.8.18-1_all.deb &&\
    echo 4 | dpkg -i mysql-apt-config_0.8.18-1_all.deb &&\
    apt update &&\
    apt install -y mysql-client &&\
    apt clean

RUN rm mysql-apt-config_0.8.18-1_all.deb

COPY pdns.conf /etc/powerdns/pdns.conf

USER pdns

ENV PDNS_guardian=yes \
    PDNS_setuid=pdns \
    PDNS_setgid=pdns \
    PDNS_launch=gmysql

COPY pdns.conf.tpl /
COPY --chmod=0755 docker-entrypoint.sh /

ENTRYPOINT [ "/docker-entrypoint.sh" ]

CMD [ "/usr/local/sbin/pdns_server" ]