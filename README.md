# config-repo-example
config-repo-example


## Spring Config Server 설정 예시

- build.gradle
```
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.5.3'
	id 'io.spring.dependency-management' version '1.1.7'
}

group = 'com.polarbookshop'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17)
	}
}

repositories {
	mavenCentral()
}

ext {
	set('springCloudVersion', "2025.0.0")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-config-server'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}
```

- application.yml 설정
```yml
server:
  port: 8888
  tomcat:
    connection-timeout: 2s
    keep-alive-timeout: 15s
    threads:
      max: 50
      min-spare: 5


spring:
  application:
    name: config-service
  cloud:
    config:
      server:
        git:
          uri: https://github.com/junoade/config-repo-example
          default-label: main
          timeout: 5
          clone-on-start: true
          force-pull: true
          search-paths: catalog-service
```

- @EnableConfigServer 설정

## Spring cloud COnfig Client 의 요청 예시

HTTP GET localhost:8888/catalog-service/prod
```json
{
    "name": "catalog-service",
    "profiles": [
        "prod"
    ],
    "label": null,
    "version": "137894ee9d1217740b9fb8535a89f0400f54566b",
    "state": "",
    "propertySources": [
        {
            "name": "https://github.com/junoade/config-repo-example/catalog-service/application-prod.yml",
            "source": {
                "server.port": 9001,
                "server.tomcat.connection-timeout": "2s",
                "server.tomcat.keep-alive-timeout": "15s",
                "server.tomcat.threads.max": 50,
                "server.tomcat.threads.min-spare": 5,
                "polar.greeting": "Welcome to the production catalog from the config server"
            }
        }
    ]
}

```
