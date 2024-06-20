Create postgres.Dockerfile with environment in order to be run database scripts:

    FROM postgres:15    
    ENV POSTGRES_PASSWORD=75sdQL57
    ENV POSTGRES_USER=postgres
    ENV POSTGRES_DB=credit_base   
    COPY /src/main/resources/db/credit_base.sql /docker-entrypoint-initdb.d/credit_base.sql
    EXPOSE 5432

Dockerfile with server app:

    FROM openjdk:17
    COPY /target/credit-service-3.2.2.jar app.jar
    ENTRYPOINT ["java", "-jar", "app.jar"]

To create executable .jar file you need spring-boot-maven-plugin: 
```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```
Compose.yaml file with volume to store database data when container will delete:
```yaml
  services:
    credit-service:
      build:
        context: .
        dockerfile: Dockerfile
      ports:
        - "5000:5000"
      depends_on:
        - postgres
    postgres:
      build:
        context: .
        dockerfile: postgres.Dockerfile
      ports:
        - "5432:5432"
      volumes:
        - postgres-data:/var/lib/postgresql/data
      environment:
        POSTGRES_USER: postgres
        POSTGRES_DB: credit_base
        POSTGRES_PASSWORD: 75sdQL57
    volumes:
      postgres-data:
```
