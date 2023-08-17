## Dockerfile for Packaging a Java Application

This Dockerfile is used to package a Java application into a Docker image.

### Base Image

The image is built from the `openjdk:8u151-jdk-alpine3.7` base image. This provides a lightweight environment for running Java applications.

### Exposed Port

The Docker container exposes port `8070` to allow communication with the application.

### Application Home Directory

The environment variable `APP_HOME` is set to `/usr/src/app`, which will serve as the application's home directory within the container.

### Copying Application JAR

The built JAR file `shopping-cart-0.0.1-SNAPSHOT.jar` from the `target` directory is copied to the container's `APP_HOME` directory.

### Working Directory

The working directory is set to `APP_HOME`, ensuring that subsequent commands are executed within this directory.

### Entry Point

The `ENTRYPOINT` instruction specifies the command to be executed when the container starts. In this case, it launches the Java application by executing the JAR file with the command:

```bash
exec java -jar app.jar
```

This Dockerfile allows you to package and run your Java application within a Docker container, providing a consistent and isolated environment for your application to execute.
Remember to customize the Dockerfile and commands as needed for your specific application.
