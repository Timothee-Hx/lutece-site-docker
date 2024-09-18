FROM maven:alpine as build
ENV HOME=/usr/app
RUN mkdir -p $HOME
WORKDIR $HOME
ADD . $HOME
RUN mvn verify clean --fail-never

ADD . $HOME
RUN mvn lutece:site-assembly
# change the name of the war file
RUN mv target/my-lutece-site*.war target/my-lutece-site.war
RUN mvn antrun:run

FROM tomcat:jre11 as runtime
COPY --from=build /usr/app/target/*.war /usr/local/tomcat/webapps/
ENTRYPOINT java -jar /app/my-lutece-site.war
EXPOSE 8080
