FROM openjdk:8-jdk-alpine

ARG user=jenkins
ARG group=jenkins
ARG uid=10000
ARG gid=10000
ARG slave_url=https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar
ARG VERSION=3.23
ARG AGENT_WORKDIR=/home/${user}/agent

ENV HOME /home/${user}
RUN addgroup -g ${gid} ${group}
RUN adduser -h $HOME -u ${uid} -G ${group} -D ${user}
LABEL Description="This is a base image, which provides the Jenkins agent executable (slave.jar)" Vendor="Jenkins project" Version="3.23"

RUN apk add --update --no-cache --allow-untrusted curl bash git openssh-client openssl procps python3 python3-dev ansible docker openrc gcc linux-headers musl-dev\
  && python3 -m ensurepip \
  && curl --create-dirs -sSLo /usr/share/jenkins/slave.jar ${slave_url} \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/slave.jar \
  && apk del curl

# RUN rc-update add docker boot && service docker start

RUN pip3 install setuptools ansible-lint flake8 molecule docker-py

USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

# ENV PATH="$PATH:~/.local/bin/"

VOLUME /home/${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/${user}
