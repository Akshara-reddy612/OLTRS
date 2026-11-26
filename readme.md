
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]

mvn clean package
docker build -t my-java-app:1.0 .
docker run -d --name my-java-container -p 8081:8080 my-java-app:1.0
docker tag my-java-app:1.0 YOUR_USERNAME/my-java-app:1.0
docker push YOUR_USERNAME/my-java-app:1.0

FROM tomcat:9.0-jdk21-temurin
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]

mvn clean package
docker build -t my-war-app:1.0 .
docker run -d --name my-war-container -p 8081:8080 my-war-app:1.0
docker tag my-war-app:1.0 YOUR_USERNAME/my-war-app:1.0
docker push YOUR_USERNAME/my-war-app:1.0
