FROM library/centos:centos7
MAINTAINER mziccard@redhat.com

RUN yum update -y -q && yum upgrade -y -q && \
    curl -LO 'http://download.oracle.com/otn-pub/java/jdk/7u51-b13/jdk-7u51-linux-x64.rpm' -H 'Cookie: oraclelicense=accept-securebackup-cookie' && \
    rpm -i jdk-7u51-linux-x64.rpm && \
    rm jdk-7u51-linux-x64.rpm
    
ENV JAVA_HOME=/usr/java/default \
    PATH=$PATH:$JAVA_HOME/bin
    
RUN curl http://mirrors.whoishostingthis.com/apache/hbase/1.2.4/hbase-1.2.4-bin.tar.gz -o /tmp/hbase-1.2.4-bin.tar.gz
RUN useradd -ms /bin/bash hbase

USER hbase
RUN tar xvfz /tmp/hbase-1.2.4-bin.tar.gz -C /home/hbase

WORKDIR /home/hbase/hbase-1.2.4
RUN mkdir -p /home/hbase/data /home/hbase/zookeeper /home/hbase/logs

COPY conf/hbase-site.xml conf/
RUN bin/hbase thrift start > logs/thrift.log 2>&1 &
RUN bin/hbase rest start > logs/rest.log 2>&1 &
# REST API
EXPOSE 8080
# REST Web UI at :8085/rest.jsp
EXPOSE 8085
# Thrift API
EXPOSE 9090
# Thrift Web UI at :9095/thrift.jsp
EXPOSE 9095
# HBase's Embedded zookeeper cluster
EXPOSE 2181
# HBase Master web UI at :16010/master-status;  ZK at :16010/zk.jsp
EXPOSE 16010
CMD bin/hbase master start 2>&1 | tee logs/hbase-master.log
