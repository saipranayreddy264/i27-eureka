From openjdk:18
RUN mkdir /opt/i27
WORKDIR /opt/i27
ARG JAVA_SOURCE
COPY ["${JAVA_SOURCE}", "/opt/i27/i27-eureka.jar"]
CMD ["java", "jar", "/opt/i27/i27-eureka.jar"]