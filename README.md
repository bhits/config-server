# Configuration Server

The Configuration Server (config-server) provides support for externalized configuration in the Consent2Share (C2S) application, including the following C2S components:

+ Admin Portal UI
+ Patient Portal UI
+ Context Handler API
+ Document Segmentation Service API
+ Edge Server
+ Patient User API
+ Patient Consent Management API
+ Policy Enforcement Point API
+ Patient Health Record API
+ Provider Lookup Service API
+ Patient Registration API
+ Try My Policy API

The Configuration Server can serve the configurations from a central Git repository on file system or a remote repository on GitHub. The [default configuration](config-server/src/main/resources/application.yml) of this server also registers itself to [Discovery Server](https://github.com/bhits/discovery-server), so the other microservices can dynamically discover the Configuration Server at startup and load additional configurations. The Configuration Server is based on [Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/) project. Please see the [Spring Cloud Config Documentation](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html) for details.

## Build

### Prerequisites

+ [Oracle Java JDK 8 with Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
+ [Docker Engine](https://docs.docker.com/engine/installation/) (for building a Docker image from the project)
+ [C2S Config Data Repository](https://github.com/bhits/c2s-config-data/) (The default implementation of the server storage backend uses git)

### Commands

This is a Maven project and requires [Apache Maven](https://maven.apache.org/) 3.3.3 or greater to build it. It is recommended to use the *Maven Wrapper* scripts provided with this project. *Maven Wrapper* requires an internet connection to download Maven and project dependencies for the very first build.

To build the project, navigate to the folder that contains the `pom.xml` file using the terminal/command line.

+ To build a JAR:
    + For Windows, run `mvnw.cmd clean install`
    + For *nix systems, run `mvnw clean install`
+ To build a Docker Image (this will create an image with `bhits/config-server:latest` tag):
    + For Windows, run `mvnw.cmd clean package docker:build`
    + For *nix systems, run `mvnw clean package docker:build`

## Run

### Commands

This is a [Spring Boot](https://projects.spring.io/spring-boot/) project and serves the application via an embedded Tomcat instance, therefore there is no need for a separate application server to run this service.
+ Run as a JAR file: `java -jar config-server-x.x.x-SNAPSHOT.jar <additional program arguments>`
+ Run as a Docker Container: `docker run -d bhits/config-server:latest <additional program arguments>`

*NOTE: In order for this application to fully function as a microservice in the Consent2Share (C2S) application, it is also required to setup the dependency microservices and support level infrastructure. Please refer to the [Consent2Share Deployment Guide](https://github.com/bhits/consent2share/releases/download/2.0.0/c2s-deployment-guide.pdf) for instructions to setup the C2S infrastructure.*

## Configure

This application runs with a default configuration that is primarily targeted for the development environment. However, [Spring Boot](https://projects.spring.io/spring-boot/) supports several methods to override the default configuration to configure the application for a certain deployment environment.

Please see the [default configuration](config-server/src/main/resources/application.yml) for this application as a guidance and override the specific configuration per the environment as needed. Also, please refer to [Spring Boot Externalized Configuration](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) documentation to see how Spring Boot applies the order to load the properties and [Spring Boot Common Properties](http://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html) documentation to see the common properties used by Spring Boot.


### Examples for Overriding a Configuration in Spring Boot

#### Override a Configuration Using Program Arguments While Running as a JAR:

+ `java -jar config-server-x.x.x-SNAPSHOT.jar --server.port=80`

#### Override a Configuration Using Program Arguments While Running as a Docker Container:

+ `docker run -d bhits/config-server:latest --server.port=80`

+ In a `docker-compose.yml`, this can be provided as:
```yml
version: '2'
services:
...
  config-server.c2s.com:
    image: "bhits/config-server:latest"
    command: ["--server.port=80"]
...
```
*NOTE: Please note that these additional arguments will be appended to the default `ENTRYPOINT` specified in the `Dockerfile` unless the `ENTRYPOINT` is overridden.*

### Config Data Repository

The config-server is implemented using [Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/) project. The [default configuration](config-server/src/main/resources/application.yml) provided with this server serves the configuration from a local Git repository on file system (`file:/java/c2s-config-data`). Please follow the [Spring Cloud Config Documentation](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html) for alternative scenarios.

Currently, C2S utilizes the config-server to centralize the shared configuration in a single Git repository; however this can be extended to manage environment specific configurations. The default configuration data repository for development environment is located at [https://github.com/bhits/c2s-config-data](https://github.com/bhits/c2s-config-data).

#### Configure a Config Data Git Repository

`spring.cloud.config.server.git.uri` property can be overridden to load the configuration data from a different repository. If it is targeted to GitHub, it can be either `https` or `ssh` URI depending on the chosen authentication mechanism. File system reference can also be used (especially for development environment).

*Example:*

For Windows. `file:///C:/java/c2s-config-data` (or `file:/java/c2s-config-data` if `C:` is the default drive and the file is on the local file system).

For Unix `file://${user.home}/c2s-config-data` *...etc.*

####  Encryption and Decryption

There are two strategies for decryption of encrypted properties: config-server side and client side. C2S currently implements the client side strategy, meaning the encrypted configuration is served to the configuration clients as it is and the decryption is performed by the configuration client (ie: PHR API...etc.). The `encrypt.key` property in the `bootstrap.yml` file of the configuration clients contains the symmetric key for decryption.

For config-server side decryption, one must set `spring.cloud.config.server.encrypt.enabled=true` and `encrypt.key` in the config-server.

### Enable SSL

For simplicity in development and testing environments, SSL is **NOT** enabled by default configuration. SSL can easily be enabled following the examples below:

#### Enable SSL While Running as a JAR

+ `java -jar config-server-x.x.x-SNAPSHOT.jar --spring.profiles.active=ssl --server.ssl.key-store=/path/to/ssl_keystore.keystore --server.ssl.key-store-password=strongkeystorepassword`

#### Enable SSL While Running as a Docker Container

+ `docker run -d -v "/path/on/dockerhost/ssl_keystore.keystore:/path/to/ssl_keystore.keystore" bhits/config-server:latest --spring.profiles.active=ssl --server.ssl.key-store=/path/to/ssl_keystore.keystore --server.ssl.key-store-password=strongkeystorepassword`
+ In a `docker-compose.yml`, this can be provided as:
```yml
version: '2'
services:
...
  config-server.c2s.com:
    image: "bhits/config-server:latest"
    command: ["--spring.profiles.active=ssl","--server.ssl.key-store=/path/to/ssl_keystore.keystore", "--server.ssl.key-store-password=strongkeystorepassword"]
    volumes:
      - /path/on/dockerhost/ssl_keystore.keystore:/path/to/ssl_keystore.keystore
...
```

*NOTE: As seen in the examples above, `/path/to/ssl_keystore.keystore` is made available to the container via a volume mounted from the Docker host running this container.*

### Override Java CA Certificates Store In Docker Environment

Java has a default CA Certificates Store that allows it to trust well-known certificate authorities. For development and testing purposes, one might want to trust additional self-signed certificates. In order to override the default Java CA Certificates Store in a Docker container, one can mount a custom `cacerts` file over the default one in the Docker image as `docker run -d -v "/path/on/dockerhost/to/custom/cacerts:/etc/ssl/certs/java/cacerts" bhits/config-server:latest`

*NOTE: The `cacerts` references regarding volume mapping above are files, not directories.*

[//]: # (## Application Documentation)

[//]: # (## Notes)

[//]: # (## Contribute)

## Contact

If you have any questions, comments, or concerns please see [Consent2Share](https://bhits.github.io/consent2share/) project site.

## Report Issues

Please use [GitHub Issues](https://github.com/bhits/config-server/issues) page to report issues.

[//]: # (License)
