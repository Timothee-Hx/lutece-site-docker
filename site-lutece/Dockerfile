FROM maven:3.6.3-adoptopenjdk-8 AS build
ENV HOME=/usr/app
RUN mkdir -p $HOME
WORKDIR $HOME
ADD ./webapp/ $HOME/webapp/
ADD ./pom.xml $HOME

RUN mvn clean lutece:site-assembly install  -DskipTests
# change the name of the war file
RUN mv target/lutece*.war target/lutece.war && ls -l target
RUN mv target/lutece*.jar target/lutece.jar && ls -l target
RUN mv target/lutece*.zip target/lutece.zip && ls -l target

FROM frekele/java:jdk8 AS build_db
ENV HOME=/usr/app
RUN mkdir -p $HOME
WORKDIR $HOME
COPY --from=build /usr/app/target $HOME
COPY --from=build /usr/app/pom.xml $HOME
RUN cd target/lutece-1.0.0-SNAPSHOT/WEB-INF/sql/ && ant
FROM tomcat:8.5.57-jdk8-openjdk
COPY --from=build /usr/app/target/* /usr/local/tomcat/webapps/
# copy maven from the build (in case you need to run maven commands in the container)
COPY --from=build /usr/share/maven /usr/share/maven
COPY --from=build /root/.m2 /root/.m2
# copy the database

# Make Maven available in the runtime's PATH
ENV PATH=$PATH:/usr/share/maven/bin
WORKDIR /usr/local/tomcat
EXPOSE 8080
# Start Tomcat and tail logs
CMD ["sh", "-c", "catalina.sh run & touch /usr/local/tomcat/logs/catalina.out && tail -f /usr/local/tomcat/logs/catalina.out"]
