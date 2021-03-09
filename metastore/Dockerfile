FROM openjdk:8-jre

ENV HADOOP_VERSION=3.2.0
ENV METASTORE_VERSION=3.0.0
ENV HADOOP_HOME=/opt/hadoop-${HADOOP_VERSION}
ENV HIVE_HOME=/opt/apache-hive-metastore-${METASTORE_VERSION}-bin
ARG RDB_CONN=mysql-connector-java-8.0.19

WORKDIR /opt

RUN set -ex \
    && curl -L https://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz | tar zxf - \
    && curl -L https://archive.apache.org/dist/hive/hive-standalone-metastore-${METASTORE_VERSION}/hive-standalone-metastore-${METASTORE_VERSION}-bin.tar.gz | tar zxf - \
## mysql connecter and jar
    && curl -L https://dev.mysql.com/get/Downloads/Connector-J/${RDB_CONN}.tar.gz \
        | tar -zxv --strip-components 1 -C ${HIVE_HOME}/lib/ -f - ${RDB_CONN}/${RDB_CONN}.jar \
## aws jar softlink
    && ln -s ${HADOOP_HOME}/share/hadoop/tools/lib/aws-java-sdk-bundle-*.jar ${HIVE_HOME}/lib/ \
    && ln -s ${HADOOP_HOME}/share/hadoop/tools/lib/hadoop-aws-${HADOOP_VERSION}.jar ${HIVE_HOME}/lib/

COPY metastore-site.xml ${HIVE_HOME}/conf
COPY entrypoint.sh .

RUN set -ex \
    && groupadd -r hive --gid=1000 \
    && useradd -r -g hive --uid=1000 -d ${HIVE_HOME} hive \
    && chown hive:hive -R ${HIVE_HOME} \
    && chown hive:hive entrypoint.sh \
    && chmod +x entrypoint.sh

USER hive
EXPOSE 9083

CMD ["sh", "entrypoint.sh"]

# https://github.com/arempter/hive-metastore-docker/blob/master/Dockerfile