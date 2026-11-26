
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


compose:
services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress-container
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass123
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db
    volumes:
      - wordpress_data:/var/www/html

  db:
    image: mysql:8.0
    container_name: mysql-container
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass123
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  wordpress_data:
  mysql_data:
docker compose up -d
localhost/8082


node-mongo
services:
  nodeapp:
    build: .
    container_name: node-container
    ports:
      - "3000:3000"
    environment:
      MONGO_URL: mongodb://mongo:27017/mydb
    depends_on:
      - mongo

  mongo:
    image: mongo:latest
    container_name: mongo-container
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:

flask-mysql
services:
  flask:
    build: .
    container_name: flask-container
    ports:
      - "5000:5000"
    environment:
      DB_HOST: db
      DB_USER: admin
      DB_PASSWORD: admin123
      DB_NAME: flaskdb
    depends_on:
      - db

  db:
    image: mysql:8.0
    container_name: flask-mysql-container
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: flaskdb
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin123
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:


fast-mysql
services:
  fastapi:
    build: .
    container_name: fastapi-container
    ports:
      - "8000:8000"
    environment:
      DB_HOST: db
      DB_NAME: fastapidb
      DB_USER: admin
      DB_PASSWORD: admin123
    depends_on:
      - db

  db:
    image: postgres:16
    container_name: fastapi-postgres-container
    environment:
      POSTGRES_DB: fastapidb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
