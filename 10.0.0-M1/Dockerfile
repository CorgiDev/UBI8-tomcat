# Red Hat UBI 8.1 based container with Java and Tomcat
ARG BASE_REGISTRY=registry.access.redhat.com
ARG BASE_IMAGE=ubi8/ubi
ARG BASE_TAG=8.1

FROM ${BASE_REGISTRY}/${BASE_IMAGE}:${BASE_TAG}

LABEL name="Apache Tomcat" \
      maintainer="Beth Gray <egray@corgidev.com>" \
      vendor="The Apache Software Foundation" \
      version="10.0.0-M7" \
      release="10" \
      summary="Apache Tomcat container" \
      description="This is an Apache Tomcat container built on a Red Hat UBI8.1 base. Apache Tomcat is an open-source implementation of the Java Servlet, JavaServer Pages, Java Expression Language and WebSocket technologies."

# Prepare environment 
ENV JAVA_HOME /usr/java/latest
ENV CATALINA_HOME /opt/tomcat 
ENV PATH $PATH:$JAVA_HOME/bin:${CATALINA_HOME}/bin:${CATALINA_HOME}/scripts
ENV JAVA_VERSION 14.0.1
ENV JAVA_BUILD 14.0.1+7
ENV JAVA_DL_HASH 664493ef4a6946b186ff29eb326336a2
ENV JAVA_RPM jdk-${JAVA_VERSION}_linux-x64_bin.rpm
ENV JAVA_SHA256 36A7DF03CAC6EF62933B59B63C045D65351865421A9133D43CEB83D683A2BDFF
ENV TOMCAT_MAJOR 10
ENV TOMCAT_VERSION 10.0.0-M7
ENV TOMCAT_TARBALL apache-tomcat-${TOMCAT_VERSION}.tar.gz
ENV TOMCAT_SHA512 a0fd7472c94285dbb86e1273828ddaa395b8e7e3c2e7d8da901fca7969c0e19fef08452d288500f4ff69bca7ed0d4e3a6382a706dc0328c9e2e9114bd86a84af

WORKDIR /opt/

# Prep infrastructure and install JDK
RUN yum -y update && \
 yum -y --nogpgcheck install wget tar && \
 yum clean all

# Obtain, verify, and install Java via rpm
RUN wget -q -O /opt/${JAVA_RPM} --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn-pub/java/jdk/${JAVA_BUILD}/${JAVA_DL_HASH}/${JAVA_RPM} && \
    echo "${JAVA_SHA256} ${JAVA_RPM}" | sha256sum -c - && \
    yum -y localinstall jdk* && \
    rm ${JAVA_RPM}

# Obtain, verify, and install Apache Tomcat via tar file
RUN wget -q -O /opt/${TOMCAT_TARBALL} https://archive.apache.org/dist/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/${TOMCAT_TARBALL} && \
    echo "${TOMCAT_SHA512} ${TOMCAT_TARBALL}" | sha512sum -c - && \
    tar -xvf ${TOMCAT_TARBALL} && \
    rm apache-tomcat*.tar.gz && \
    mv apache-tomcat* ${CATALINA_HOME} && \
    chmod +x ${CATALINA_HOME}/bin/*sh && \
    # Make a directory for some personal Tomcat scripts
    mkdir ${CATALINA_HOME}/scripts/ && \
    # Create Apache Tomcat user
    groupadd -r tomcat && \
    useradd -g tomcat -d ${CATALINA_HOME} -s /sbin/nologin  -c "Tomcat user" tomcat && \
    chown -R tomcat:tomcat ${CATALINA_HOME} && \
    # Disable ctrl+alt+del burst action to prevent a security vulnerability from being an issue.
    echo CtrlAltDelBurstAction=none >> /etc/systemd/system.conf

# Create Apache Tomcat admin user using script
ADD /scripts/create_admin_user.sh /tomcat/scripts/create_admin_user.sh
ADD /scripts/tomcat.sh /tomcat/scripts/tomcat.sh
RUN chmod +x /tomcat/scripts/*.sh

WORKDIR /opt/tomcat

EXPOSE 8080
#EXPOSE 8009

USER tomcat
#CMD ["tomcat.sh"]
CMD [ "/opt/tomcat/bin/catalina.sh", "run" ]