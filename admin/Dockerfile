FROM fedora:33

RUN curl -sSL -o /etc/yum.repos.d/yarn.repo https://dl.yarnpkg.com/rpm/yarn.repo \
  && dnf -y --setopt=install_weak_deps=False install \
  python3-ldap \
  python3-mysql \
  python3-pip \
  python3-setuptools \
  python3-xmlsec \
  uwsgi \
  yarn \
  && dnf clean all

RUN mkdir -p /opt/powerdns-admin \
  && curl -sSL https://github.com/ngoduykhanh/PowerDNS-Admin/archive/refs/tags/v0.2.4.tar.gz \
  | tar -xzC /opt/powerdns-admin --strip 1 \
  && sed -i '/python-ldap/d' /opt/powerdns-admin/requirements.txt \
  && sed -i '/mysqlclient/d' /opt/powerdns-admin/requirements.txt \
  && chown -R root: /opt/powerdns-admin

WORKDIR /opt/powerdns-admin

RUN pip3 install --no-cache-dir envtpl python-dotenv \
  && pip3 install -r requirements.txt --no-cache-dir

ENV FLASK_APP=/opt/powerdns-admin/powerdnsadmin/__init__.py

COPY config.py.tpl /
COPY run.py .

RUN envtpl < /config.py.tpl > /opt/powerdns-admin/config.py \
  && sed -i '/SQLALCHEMY_DATABASE_URI/d' /opt/powerdns-admin/config.py

RUN yarn install --pure-lockfile --production \
  && yarn cache clean \
  && flask assets build \
  && chown -R uwsgi: /opt/powerdns-admin/powerdnsadmin/static/.webassets-cache

RUN dnf -y --setopt=install_weak_deps=False install \
  mariadb \
  uwsgi-plugin-python3 \
  && dnf clean all

EXPOSE 9494

COPY docker-entrypoint.sh /
COPY pdns-admin.ini /etc/uwsgi.ini
RUN chown uwsgi: /etc/uwsgi.ini

ENTRYPOINT [ "/docker-entrypoint.sh" ]

CMD [ "/usr/sbin/uwsgi", "--ini", "/etc/uwsgi.ini" ]