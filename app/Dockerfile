FROM maven:3.6.0-jdk-8 as static_code_analisys 

ARG BRANCH_NAME
ARG SONAR_TOKEN
ARG SONAR_SERVER 
ARG SONAR_PORT=9000 

COPY src /usr/src/app/src  
COPY pom.xml /usr/src/app  
COPY settings.xml /root/.m2/settings.xml 

RUN echo ${SONAR_TOKEN} ${SONAR_SERVER} 

RUN mvn -f /usr/src/app/pom.xml clean install -DskipTests=true \
  && mvn -f /usr/src/app/pom.xml sonar:sonar \
    -Dsonar.host.url=http://${SONAR_SERVER}:${SONAR_PORT} \
    -Dsonar.login=${SONAR_TOKEN} \
    -Dsonar.branch=${BRANCH_NAME} || exit 0 

FROM maven:3.6.0-jdk-8 as build 

COPY src /usr/src/app/src  
COPY pom.xml /usr/src/app  
COPY settings.xml /root/.m2/settings.xml 

RUN mvn -f /usr/src/app/pom.xml surefire:test \
  && mvn -f /usr/src/app/pom.xml clean package -DskipTests=true

FROM openjdk:8-jdk-alpine
VOLUME /tmp
COPY --from=build /usr/src/app/target/spring-vault-demo-1.0.jar app.jar 
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]