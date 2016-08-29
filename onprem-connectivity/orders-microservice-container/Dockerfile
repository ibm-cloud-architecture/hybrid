FROM registry.ng.bluemix.net/ibmliberty

ARG db_name=orders
ARG db_ip
ARG db_port=3306
ARG db_user=orders_dbuser
ARG db_passwd=Pass4OrdersUs3r

ENV DB_NAME ${db_name}
ENV DB_IP ${db_ip}
ENV DB_PORT ${db_port}
ENV DB_USER ${db_user}
ENV DB_PASSWD ${db_passwd}

ENV HTTP_PORT=9080

COPY wlp/usr/servers/hybrid-ordersapi-ng/server.xml /config/server.xml

# update the HTTP PORT
RUN sed -i -e 's/\${port}/'${HTTP_PORT}'/g' /config/server.xml


# rewrite server.xml for local execution
RUN sed -i -e '/<\/server>/d' /config/server.xml

# remove bluemix-only liberty runtime features so the error messages do not show up in the logs
RUN sed -i -e '/<featureManager>/,/<\/featureManager>/d' /config/server.xml
RUN echo "  <featureManager>" >> /config/server.xml
RUN echo "    <feature>javaee-7.0</feature>" >> /config/server.xml
RUN echo "  </featureManager>" >> /config/server.xml

# add basicRegistry
RUN echo "  <basicRegistry id=\"basic\" realm=\"BasicRealm\"/>" >> /config/server.xml

RUN echo "    <applicationManager autoExpand=\"true\"/>" >> /config/server.xml
RUN echo "    <library id=\"MySQLLib\">" >> /config/server.xml
RUN echo "      <fileset dir=\"\${shared.resource.dir}/mysql\" includes=\"*.jar\"/>" >> /config/server.xml
RUN echo "    </library>" >> /config/server.xml
RUN echo "" >> /config/server.xml
RUN echo "    <!-- Declare the Worklight Server project database -->" >> /config/server.xml
RUN echo "    <dataSource id=\"DefaultDataSource\" jndiName=\"jdbc/mydbdatasource\" transactional=\"false\">" >> /config/server.xml
RUN echo "        <jdbcDriver libraryRef=\"MySQLLib\"/>" >> /config/server.xml
RUN echo "        <properties databaseName=\"${DB_NAME}\" createDatabase=\"create\" serverName=\"${DB_IP}\" portNumber=\"${DB_PORT}\" user=\"${DB_USER}\" password=\"${DB_PASSWD}\" />" >> /config/server.xml
RUN echo "    </dataSource>" >> /config/server.xml

RUN echo "</server>" >> /config/server.xml

# copy dependencies
COPY wlp/usr/servers/hybrid-ordersapi-ng/apps/JavaOrdersAPI.war /config/apps/JavaOrdersAPI.war

# install mysql jdbc driver
RUN apt-get install libmysql-java
RUN mkdir -p /opt/ibm/wlp/usr/shared/resources/mysql
RUN chmod 777 /opt/ibm/wlp/usr/shared/resources/mysql
RUN cp /usr/share/java/mysql-connector-java-5.1.38.jar /opt/ibm/wlp/usr/shared/resources/mysql/
RUN chmod 755 /opt/ibm/wlp/usr/shared/resources/mysql/*.jar

