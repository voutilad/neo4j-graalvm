FROM oracle/graalvm-ce:20.3.0-java11

ENV NEO4J_SHA256=0f074e2d84c265fbb066ad648b52c62c623d1f1b18b9d4d04356d46726853c96 \
    NEO4J_TARBALL=neo4j-community-4.2.1-unix.tar.gz \
    NEO4J_EDITION=community \
    NEO4J_HOME="/var/lib/neo4j"
ARG NEO4J_URI=https://dist.neo4j.org/neo4j-community-4.2.1-unix.tar.gz
ARG TINI_SHA256="12d20136605531b09a2c2dac02ccee85e1b874eb322ef6baf7561cd93f93c855"
ARG TINI_URI="https://github.com/krallin/tini/releases/download/v0.18.0/tini"
ARG GOSU_SHA256="0f25a21cf64e58078057adc78f38705163c1d564a959ff30a891c31917011a54"
ARG GOSU_URI="https://github.com/tianon/gosu/releases/download/1.12/gosu-amd64"

RUN groupadd --gid 7474 --system neo4j && \
    adduser --uid 7474 --system --no-create-home --home "${NEO4J_HOME}" -g neo4j neo4j

COPY ./local-package/* /tmp/

RUN yum install -y curl wget hostname jq \
    && curl -L --fail --silent --show-error ${TINI_URI} > /sbin/tini \
    && curl -L --fail --silent --show-error ${GOSU_URI} > /sbin/gosu \
    && echo "${TINI_SHA256}  /sbin/tini" | sha256sum -c --strict --quiet \
    && echo "${GOSU_SHA256}  /sbin/gosu" | sha256sum -c --strict --quiet \
    && chmod +x /sbin/tini /sbin/gosu \
    && curl --fail --silent --show-error --location --remote-name ${NEO4J_URI} \
    && echo "${NEO4J_SHA256}  ${NEO4J_TARBALL}" | sha256sum -c --strict --quiet \
    && tar --extract --file ${NEO4J_TARBALL} --directory /var/lib \
    && mv /var/lib/neo4j-* "${NEO4J_HOME}" \
    && rm ${NEO4J_TARBALL} \
    && mv "${NEO4J_HOME}"/data /data \
    && mv "${NEO4J_HOME}"/logs /logs \
    && chown -R neo4j:neo4j /data \
    && chmod -R 777 /data \
    && chown -R neo4j:neo4j /logs \
    && chmod -R 777 /logs \
    && chown -R neo4j:neo4j "${NEO4J_HOME}" \
    && chmod -R 777 "${NEO4J_HOME}" \
    && ln -s /data "${NEO4J_HOME}"/data \
    && ln -s /logs "${NEO4J_HOME}"/logs \
    && mv /tmp/neo4jlabs-plugins.json /neo4jlabs-plugins.json \
    && rm -rf /tmp/* \
    && yum clean all

ENV PATH "${NEO4J_HOME}"/bin:$PATH

WORKDIR "${NEO4J_HOME}"

VOLUME /data /logs

COPY docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 7474 7473 7687

ENTRYPOINT ["/sbin/tini", "-g", "--", "/docker-entrypoint.sh"]
CMD ["neo4j"]
