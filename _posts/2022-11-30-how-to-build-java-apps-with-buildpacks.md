---
layout: post
title:  "How to build your Java Apps with Paketo Buildpacks - Cloud Native Tool #001"
author: alin
categories: [ "Cloud Native Tool" ]
image: https://i.postimg.cc/s3Pn7XLV/buildpacks.webp
featured: false
hidden: false
---

Buildpacks transform your application source code into container images without using Dockerfiles and provides production-ready buildpacks for Java apps

Some nice features:
- **Multi language** -> Supports more than one programming language family.
- **Bill of Materials** -> Insights into the contents of the app image through standard build-time SBOM
- **Minimal app image** -> Image contains only what is necessary.
- **Rebasing** -> Instant updates of base images without re-building


## 1. Create a test application
Go to [https://start.spring.io/](https://start.spring.io/) and create the app
![App](/assets/images/create-app.webp)

Create a very simple application which exposes a single route `/` with `Welcome to Buildpacks!`
```java
package ro.dragomiralin.buildpacks;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.servlet.function.RouterFunction;
import org.springframework.web.servlet.function.RouterFunctions;
import org.springframework.web.servlet.function.ServerResponse;

@SpringBootApplication
public class BuildpacksApplication {

	public static void main(String[] args) {
		SpringApplication.run(BuildpacksApplication.class, args);
	}

	@Bean
	RouterFunction<ServerResponse> routerFunction() {
		return RouterFunctions.route()
				.GET("/", request -> ServerResponse.ok().body("Welcome to Buildpacks!"))
				.build();
	}

}
```

## 2. Install Buildpacks CLI
Cloud Native Buildpacks provides a CLI called `pack`
```shell
# Ubuntu
$ sudo add-apt-repository ppa:cncf-buildpacks/pack-cli
$ sudo apt-get update
$ sudo apt-get install pack-cli
```
```shell
# macOS
$ brew install buildpacks/tap/pack
```
```shell
# Windows by Chocolatey
$ choco install pack --version=0.27.0
```

## 2. Package
You do not need a Dockerfile, but you do need a Docker daemon, either locally or remotely through the `DOCKER_HOST` environment variable.

### 2.1. Package application using Buildpacks CLI
A `builder` is an image that contains all the components necessary to execute a build. 

```bash
$ pack build demo-service-by-cli --builder docker.io/paketobuildpacks/builder:base --env BP_JVM_VERSION=17
```
```
Successfully built image demo-service-by-cli
```

```bash
$ docker ps
REPOSITORY                                TAG                        IMAGE ID       CREATED         SIZE
demo-service-by-cli                       latest                     0975feeb2a14   42 years ago    276MB
```
#### Run it
```bash
$ docker run --rm -p 8080:8080 demo-service-by-cli
```
```bash
$ curl localhost:8080
Welcome to Buildpacks!
```

### 2.2. Package application using Maven
You can use the Spring Boot build plugin for Maven. The plugin create an OCI image (the same format as one created by docker build) by using Cloud Native Buildpacks. [Maven Buildpack](https://github.com/paketo-buildpacks/maven)

```bash
$ mvn spring-boot:build-image -Dspring-boot.build-image.imageName=demo-service-by-maven
```
```bash
$ docker ps
```

#### Run it
```bash
$ docker run --rm -p 8080:8080 demo-service-by-maven
REPOSITORY                                TAG                        IMAGE ID       CREATED         SIZE
demo-service-by-maven                     latest                     0975feeb2a14   42 years ago    276MB
```
```bash
$ curl localhost:8080
Welcome to Buildpacks!
```

References:
- [Cloud Native Buildpacks Docs](https://buildpacks.io/docs)
- [Spring Boot Docker - Build Plugins](https://spring.io/guides/topicals/spring-boot-docker/)






