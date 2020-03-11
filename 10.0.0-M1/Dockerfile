# Red Hat UBI 8.1 based container with Java and Tomcat
ARG BASE_REGISTRY=registry.access.redhat.com
ARG BASE_IMAGE=ubi8/ubi
ARG BASE_TAG=8.1

FROM ${BASE_REGISTRY}/${BASE_IMAGE}:${BASE_TAG}

LABEL name="Apache Tomcat" \
      maintainer="Beth Gray <egray@corgidev.com>" \
      vendor="The Apache Software Foundation" \
      version="10.0.0-M1" \
      release="10" \
      summary="Apache Tomcat container" \
      description="This is an Apache Tomcat container built on a Red Hat UBI8.1 base. Apache Tomcat is an open-source implementation of the Java Servlet, JavaServer Pages, Java Expression Language and WebSocket technologies."

# Prepare environment 
ENV JAVA_HOME /usr/java/latest
ENV CATALINA_HOME /opt/tomcat 
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin:$CATALINA_HOME/scripts

# Java Variables
ENV JAVA_VERSION 13.0.2
ENV JAVA_BUILD 13.0.2+8
ENV JAVA_DL_HASH d4173c853231432d94f001e99d882ca7
ENV JAVA_RPM jdk-${JAVA_VERSION}_linux-x64_bin.rpm
ENV JAVA_256_HASH e2214a723d611b4a781641061a24ca6024f2c57dbd9f75ca9d857cad87d9475f

# TOMCAT Variables
ENV TOMCAT_MAJOR 10
ENV TOMCAT_VERSION 10.0.0-M1
ENV TOMCAT_TARBALL apache-tomcat-${TOMCAT_VERSION}.tar.gz
ENV TOMCAT_256_HASH a28a99e941564c9ae05b09c035531c1a62057bcf580af0b11c01ae92564c967139ec2510b1338869567e94847b653ee883766efdfcb582fb22c7e365579aa594

# Prep infrastructure and install JDK
RUN yum -y update && \
 yum -y install wget && \
 yum -y install tar

 # Obtain and verify Java RPM before installing it
RUN wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn-pub/java/jdk/${JAVA_BUILD}/${JAVA_DL_HASH}/${JAVA_RPM} && \
    #echo "${JAVA_256_HASH} ${JAVA_RPM}" | sha256sum - c \
    yum -y localinstall jdk* && \
    rm ${JAVA_RPM}

# Obtain and verify Apache Tomcat Tar file before installing it
RUN wget https://downloads.apache.org/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/${TOMCAT_TARBALL} && \
    #echo "${TOMCAT_256_HASH} ${TOMCAT_TARBALL}" | sha256sum -c \
    tar -xvf ${TOMCAT_TARBALL} && \
    rm apache-tomcat*.tar.gz && \
    mv apache-tomcat* ${CATALINA_HOME} && \
    chmod +x ${CATALINA_HOME}/bin/*sh

# Create Apache Tomcat admin user using script
COPY /scripts ${CATALINA_HOME}/scripts
# ADD create_admin_user.sh $CATALINA_HOME/scripts/create_admin_user.sh
# ADD tomcat.sh $CATALINA_HOME/scripts/tomcat.sh
RUN chmod +x $CATALINA_HOME/scripts/*.sh

# Create Apache Tomcat user
RUN groupadd -r tomcat && \
 useradd -g tomcat -d ${CATALINA_HOME} -s /sbin/nologin  -c "Tomcat user" tomcat && \
 chown -R tomcat:tomcat ${CATALINA_HOME}

WORKDIR /opt/tomcat

EXPOSE 8080
#EXPOSE 8009

USER tomcat
#CMD ["tomcat.sh"]
CMD [ "/opt/tomcat/bin/catalina.sh", "run" ]