
# Short Description
The Configuration Server provides support for externalized configuration in the Consent2Share application.

# Full Description

# Supported Source Code Tags and Current `Dockerfile` Link

[`0.4.0 (latest)`](https://github.com/bhits/config-server/releases/tag/0.4.0), [`0.3.0`](https://github.com/bhits/config-server/releases/tag/0.3.0)

[`Current Dockerfile`](../config-server/src/main/docker/Dockerfile)

For more information about this image, the source code, and its history, please see the [GitHub repository](https://github.com/bhits/config-server).

# What is Configuration Server?

The Configuration Server (config-server) provides support for externalized configuration in the Consent2Share application. The Configuration Server can serve the configurations from a central Git repository on file system or a remote repository like repository on GitHub. Consent2Share provides a [`Default Configuration Data Git Repository`]( https://github.com/bhits/c2s-config-data).

For more information and related downloads for Consent2Share, please visit [Consent2Share](https://bhits.github.io/consent2share/).

# How to Use This Image

## Start a Configuration Server Instance

Be sure to familiarize yourself with the repository's [README.md](https://github.com/bhits/config-server) file before starting the instance.

`docker run  --name config-server -d bhits/config-server:latest <additional program arguments>`

*NOTE: In order for this API to fully function as a microservice in the Consent2Share application, it is required to setup the dependency microservices and the support level infrastructure. Please refer to the Consent2Share Deployment Guide in the corresponding Consent2Share release (see [Consent2Share Releases Page](https://github.com/bhits/consent2share/releases)) for instructions to setup the Consent2Share infrastructure.*
 
## Configure

The Spring profiles `docker` are activated by default when building images.

This API can run with the default configuration which is from two places: `bootstrap.yml`, `application.yml`. Both `bootstrap.yml` and `application.yml` files are located in the class path of the running application.

Also, [Spring Boot](https://projects.spring.io/spring-boot/) supports other ways to override the default configuration to configure the API for a certain deployment environment. 

The following is an example to override the default security user password:

`docker run -d bhits/config-server:latest --security.user.password=strongpassword`

## Using a custom configuration file

To use a custom `application.yml`, mount the file to the docker host and set the environment variable `spring.config.location`.

`docker run -v "/path/on/dockerhost/C2S_PROPS/config-server/application.yml:/java/C2S_PROPS/config-server/application.yml" -d bhits/config-server:latest --spring.config.location="file:/java/C2S_PROPS/config-server/"`

## Configure a Config Data Git Repository

`spring.cloud.config.server.git.uri` is the location of property resources, whose default value is `file:/java/c2s-config-data`. 

Example to mount the [c2s-config-data]( https://github.com/bhits/c2s-config-data) Git repository on docker host to the Configuration Server container: 

`docker run -v "/path/on/dockerhost/C2S_PROPS/c2s-config-data:/java/c2s-config-data" -d bhits/config-server:latest"`

## Environment Variables

When you start the Configuration Server image, you can edit the configuration of the Configuration Server instance by passing one or more environment variables on the command line. 

### JAR_FILE

This environment variable is used to setup which jar file will run. You need to mount the jar file to the root of container.

`docker run --name config-server -e JAR_FILE="config-server-latest.jar" -v "/path/on/dockerhost/config-server-latest.jar:/config-server--latest.jar" -d bhits/config-server:latest`

### JAVA_OPTS 

This environment variable is used to setup a JVM argument, such as memory configuration.

`docker run --name config-server -e "JAVA_OPTS=-Xms512m -Xmx700m -Xss1m" -d bhits/config-server:latest`

### DEFAULT_PROGRAM_ARGS 

This environment variable is used to setup an application argument. The default value is: "--spring.profiles.active=docker".

`docker run --name config-server -e DEFAULT_PROGRAM_ARGS="--spring.profiles.active=ssl,docker" -d bhits/config-server:latest`

# Supported Docker Versions

This image is officially supported on Docker version 1.13.0.

Support for older versions (down to 1.6) is provided on a best-effort basis.

Please see the [Docker installation documentation](https://docs.docker.com/engine/installation/) for details on how to upgrade your Docker daemon.

# License

View [license](https://github.com/bhits/config-server/blob/master/LICENSE) information for the software contained in this image.

# User Feedback

## Documentation
 
Documentation for this image is stored in the [bhits/config-server](https://github.com/bhits/config-server) GitHub repository. Be sure to familiarize yourself with the repository's README.md file before attempting a pull request.

## Issues

If you have any problems with or questions about this image, please contact us through a [GitHub issue](https://github.com/bhits/config-server/issues).

