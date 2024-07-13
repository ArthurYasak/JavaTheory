## What is jar-file?
A jar file is simply a type of archive used to package java class files and associated resources for distribution.

### Create jar file by hand.
- Compile all java classes to new directory (ex. target\):
  
javac path\to\java\Class.java path\to\another\Class.java -d target\

- Create JAR (c - create; f - file; m - manifest; -C - (2 parameters) Change to the specified directory and include the following file:

jar -cfm fileName.jar MANIFEST.MF(txt) -C target\ . src\main\resources\ .

- Launch jar:

java -jar fileName.jar

### From Spring Boot Maven Plugin documentation(https://docs.spring.io/spring-boot/docs/2.3.0.RELEASE/maven-plugin/reference/html/):
## Packaging Executable Archives
The plugin can create executable archives (jar files and war files) that contain all of an application’s dependencies and can then be run with java -jar.
Packaging an executable archive is performed by the repackage goal, as shown in the following example:
```xml
<build>
 <plugins>
  <plugin>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-maven-plugin</artifactId>
   <version>2.3.0.RELEASE</version>
   <executions>
    <execution>
     <goals>
      <goal>repackage</goal>
     </goals>
    </execution>
   </executions>
  </plugin>
 </plugins>
</build>
```
