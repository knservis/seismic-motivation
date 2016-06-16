FROM java:8-jre-alpine

MAINTAINER Konstantinos Servis <knservis@gmail.com>

RUN wget http://files.concurrentinc.com/driven/2.1/driven-server/driven-tomcat-2.1.4.zip
RUN unzip driven-tomcat-2.1.4.zip
RUN rm driven-tomcat-2.1.4.zip

EXPOSE 8080

WORKDIR /driven-tomcat-2.1.4

CMD ["bin/catalina.sh", "run" ]

