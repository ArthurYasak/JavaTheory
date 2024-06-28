# Classpath
The classpath is simply a list of directories, JAR files, and ZIP archives to search for class files. 
The runtime needs to know where to find your compiled classes so that’s what you’re providing here.
## Differences Between Classpath and Build Path
Finally, let’s explore the main difference between Classpath and Build path. 
While the JVM uses the classpath at runtime to find class files and libraries, IDEs use the build path during the build process to find dependencies.

Here’s a quick comparison between JVM usage of the Class path and IDEs’ usage of the Build path:
![Classpath_Build_path](https://github.com/ArthurYasak/JavaTheory/blob/a24c3206c48caa9df2fe024935536a6acdec10db/images/classpath%26jar/Classpath_Build_path.png)
