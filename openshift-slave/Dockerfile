FROM openshift/jenkins-slave-base-centos7

ARG user=jenkins
ARG group=jenkins
ARG uid=10000
ARG gid=10000
ARG slave_url=https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar
ARG VERSION=3.23
ARG AGENT_WORKDIR=/home/${user}/agent

ENV HOME /home/${user}

RUN groupadd -g ${gid} ${group}
RUN useradd -b $HOME -u ${uid} -g ${group} ${user}
LABEL Description="This is a base image, which provides the Jenkins agent executable (slave.jar)" Vendor="Jenkins project" Version="3.23"

RUN yum update -y && yum install -y --setopt=tsflags=nodocs epel-release && yum update -y \
  && yum install --setopt=tsflags=nodocs -y curl bash git openssh-clients openssl procps python python-devel ansible docker openrc gcc linux-headers musl-dev python-pip \
  && curl --create-dirs -sSLo /usr/share/jenkins/slave.jar ${slave_url} \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/slave.jar

RUN pip install setuptools ansible-lint flake8 molecule docker-py
RUN chown -R ${user}:${group} /home/${user}

USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

# ENV PATH="$PATH:~/.local/bin/"

VOLUME /home/${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/${user}
