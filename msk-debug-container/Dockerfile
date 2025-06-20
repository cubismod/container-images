FROM registry.access.redhat.com/ubi9/openjdk-21@sha256:b92d685a56854474dfef0dfe0975003e761dcbefe707fa3e0ca7d0d74a77863d
ARG KAFKA_VERSION
ARG KAFKA_VERSION_PATCH

LABEL konflux.additional-tags=${KAFKA_VERSION}
ENV \
    KAFKA_VERSION_FULL=${KAFKA_VERSION}.${KAFKA_VERSION_PATCH} \
    PATH=$PATH:/opt/kafka/bin \
    MSK_CONFIG=/client.properties

USER root
RUN microdnf --setopt=install_weak_deps=0 --setopt=tsflags=nodocs install -y \
    # for envsubst command
    gettext \
    # for tar
    gzip \
    # to enable customizing the client properties
    vim \
    && microdnf clean all

RUN curl -sfL https://archive.apache.org/dist/kafka/${KAFKA_VERSION_FULL}/kafka_2.13-${KAFKA_VERSION_FULL}.tgz -o /tmp/kafka.tgz && \
    tar xf /tmp/kafka.tgz -C /opt && \
    rm /tmp/kafka.tgz && \
    ln -s /opt/kafka_2.13-${KAFKA_VERSION_FULL} /opt/kafka

RUN mkdir -p /licenses && ln -snf /opt/kafka/LICENSE /licenses/kafka

COPY client.properties.template start.sh /
RUN touch /${MSK_CONFIG} && chmod 666 /${MSK_CONFIG}

# Switch back to the default user
USER ${USER}
CMD ["/start.sh"]
