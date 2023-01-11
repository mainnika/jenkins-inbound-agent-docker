# syntax = docker/dockerfile:1.2
FROM registry.access.redhat.com/ubi8/ubi

RUN set -eux; \
    dnf makecache; \
    dnf install -yq java-11-openjdk; \
    dnf install -yq git-lfs curl fontconfig; \
    dnf clean all; \
    rm -rf /var/cache/yum

ARG VERSION=4.11.2
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000

RUN groupadd -g ${gid} ${group}
RUN useradd -c "Jenkins user" -d /home/${user} -u ${uid} -g ${gid} -m ${user}

ARG AGENT_WORKDIR=/home/${user}/agent

RUN set -eux; \    
    curl --create-dirs -fsSLo /usr/share/jenkins/agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar; \
    chmod 755 /usr/share/jenkins; \
    chmod 644 /usr/share/jenkins/agent.jar; \
    ln -sf /usr/share/jenkins/agent.jar /usr/share/jenkins/slave.jar

COPY ../../jenkins-agent /usr/local/bin/jenkins-agent
RUN set -eux; \
    chmod +x /usr/local/bin/jenkins-agent; \
    ln -s /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-slave

ENV LANG C.UTF-8

ENV JAVA_HOME=/usr/lib/jvm/jre-11-openjdk
ENV PATH "${JAVA_HOME}/bin:${PATH}"

USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

WORKDIR /home/${user}

LABEL \
    org.opencontainers.image.vendor="Jenkins project" \
    org.opencontainers.image.title="Jenkins Agent Base Docker image" \
    org.opencontainers.image.description="This is a base image, which allows connecting Jenkins agents via JNLP protocols" \
    org.opencontainers.image.version="${VERSION}" \
    org.opencontainers.image.url="https://www.jenkins.io/" \
    org.opencontainers.image.source="https://github.com/mainnika/jenkins-inbound-agent-docker" \
    org.opencontainers.image.licenses="MIT"

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
