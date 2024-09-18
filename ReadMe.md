FROM maven:3.6.3-adoptopenjdk-8 AS build
ENV HOME=/usr/app
RUN mkdir -p $HOME
WORKDIR $HOME
ADD ./webapp/ $HOME/webapp/
ADD ./pom.xml $HOME

RUN mvn clean lutece:site-assembly install  -DskipTests
# change the name of the war file
RUN mv target/lutece*.war target/lutece.war
RUN ls -l target
FROM 1.10.3-jdk8-latest AS build_db
ENV HOME=/usr/app
RUN mkdir -p $HOME
WORKDIR $HOME
ADD ./target/ $HOME/target/
ADD ./pom.xml $HOME
RUN cd target/lutece-1.0.0-SNAPSHOT/WEB-INF/sql/ && ant
FROM tomcat:jre11 AS runtime
COPY --from=build /usr/app/target/*.war /usr/local/tomcat/webapps/
CMD ["catalina.sh", "run"]
