plugins {
    id 'org.springframework.boot' version '2.1.5.RELEASE'
    id 'java'
    id 'war'
    id 'idea'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.fleta'
version = '1.1.0'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}
dependencies {
}
