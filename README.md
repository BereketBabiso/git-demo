=========git commands=============
1. Install the git installer
2. set user name just to explicitly inform git that anything add to git is users
   command: git config --global user.name "Bereket Babiso"
3. set email address as well for same reason as part(2)
   comman: git config --global user.email "bereketb.yetera@gmail.com"

4. git init MyProject1 // creates a git master project in the git directory
5. cd MyProject1  // changes diretory to MyProject1
6. ls -a //to show whether a .git file exists in the directory
7. touch filename.filetype(for ex README.md) // to create a new file
8. vi README.md // this opens the vim text editor to write of README.md , once done writing
    		// click ESC and type :wq to close the vim editor
9. git add  README.md // adds the file to the working branch
10. git commit -m "commit message" // commits the change to the working branch
11. git branch // shows the available branches
12. git status // shows the status on the working branch
13. ls  // list all the files in the branch
14. git checkout -b develop // creates a new develop branch from the master branch
15. git checkout -b feature-1.0 // creates a new feature from the current working develop branch
16. 
17. git push -u origin master  // push the master branch
18. git push -u origin feature-1.0 // push the feature branch
19. git push -u origin develop // push the develop branch
20. git add -p , then select "y" ======> this is to stage hunk


====================================================================
1. git clone https://github.ford.com/SNITHY11/AccountService.git
2. git branch
3. git checkout -b feature/CapUserIdManager
4. git branch
5. git add .
6. git commit -m "update on CAP User ID Manager"
7. push -u origin feature/CapUserIdManager

8. gradlew clean build b


============================================
buildscript {
    ext {
        springBootVersion = '2.0.6.RELEASE'
    }
    repositories {
        // maven { url "http://www.nexus.ford.com/content/groups/public/" }
        maven { url "https://plugins.gradle.org/m2/" }
        maven { url "https://repo.spring.io/plugins-release" }
        mavenCentral()
        maven { url "https://plugins.gradle.org/m2/" }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
        classpath "org.sonarsource.scanner.gradle:sonarqube-gradle-plugin:2.6.2"
        classpath("gradle.plugin.com.gorylenko.gradle-git-properties:gradle-git-properties:1.4.17")
        classpath "io.spring.gradle:dependency-management-plugin:1.0.3.RELEASE"
//        classpath 'org.jfrog.buildinfo:build-info-extractor-gradle:3.1.1'
        classpath 'org.unbroken-dome.gradle-plugins:gradle-testsets-plugin:1.4.2'
    }
}

apply plugin: 'java'
apply plugin: 'groovy'
apply plugin: 'org.springframework.boot'
apply plugin: 'org.unbroken-dome.test-sets'
apply plugin: 'checkstyle'
apply plugin: 'jacoco'
apply plugin: 'findbugs'
apply plugin: "org.sonarqube"
//apply from: 'nexus.gradle'
apply plugin: 'com.gorylenko.gradle-git-properties'
apply plugin: "io.spring.dependency-management"

repositories {
    maven {
        url "https://repo.spring.io/plugins-release"
    }
    maven { url "http://www.nexus.ford.com/content/groups/public/" }
    mavenCentral()
}

dependencyManagement {
    imports {
        mavenBom "io.pivotal.spring.cloud:spring-cloud-services-dependencies:2.0.1.RELEASE"
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.RELEASE"
    }
}

group = 'com.ford.sca.cap.vehicle.delete'
version = '1.0.0'
description = """Delete Vehicle Service Application"""
archivesBaseName = 'DeleteVehiclesService'
sourceCompatibility = 1.8
targetCompatibility = 1.8

bootJar {
    baseName = 'deleteVehicleService'
    version = '1.1.0-SNAPSHOT'
}

gitProperties {
    gitPropertiesDir = new File("${project.rootDir}/src/main/resources")
}

task hasAuthenticationProperty {
    doLast {
        if (!project.hasProperty("azureAD") && !project.hasProperty("pcfUAA")) {
            throw new Error("Please specify authentication configuration as either azureAD or pcfUAA")
        }
    }
}
check.dependsOn(hasAuthenticationProperty)

checkstyle {
    toolVersion = '8.3'
    showViolations = false
    configFile = new File('google_check_style.xml')
    maxWarnings = 0
    sourceSets = [sourceSets.main]
}

jacocoTestReport {
    reports {
        xml.enabled false
        csv.enabled false
        html.destination file("${buildDir}/reports/jacoco")
    }
    afterEvaluate {
        classDirectories = files(classDirectories.files.collect {
            fileTree(dir: it, exclude: [
                    '**/*transport**', '**/*config**', '**/*controller*', '**/*statics/Flags*'
                    , '**/*statics/RequestMode*', '**/*vehicle/MaintainVehicleServiceLauncher*',
                    '**/*vehicle/util/LoggerBuilder*', '**/*vehicle/util/AccountServiceAspects*',
                    '**/*vehicle/util/Constants*', '**/*vehicle/service/ServiceHelper*',
                    '**/*vehicle/service/statics/NotificationConstants*','**/*vehicle/util/AuditActivityUtil*',
                    '**/*vehicle/domain*','**/*vehicle/executor*','**/*vehicle/datastore*','**/*vehicle/integration*',
                    '**/*vehicle/service/ruleengines*',
            ])
        })
    }
}
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.85
            }
        }
        rule {
            enabled = false
            element = 'CLASS'
            includes = ['org.gradle.*']

            limit {
                counter = 'LINE'
                value = 'TOTALCOUNT'
                maximum = 0.3
            }
        }
    }
}
check.dependsOn jacocoTestReport
/*check.dependsOn jacocoTestCoverageVerification
jacocoTestCoverageVerification.dependsOn jacocoTestReport*/

findbugs {
    ignoreFailures = false
    toolVersion = "3.0.1"
    sourceSets = [sourceSets.main]
    reportsDir = file("$project.buildDir/reports/findbugs")
    effort = "max"
    excludeFilter = file("$projectDir/findbugs_filter.xml")
}

tasks.withType(FindBugs) {
    reports {
        xml.enabled = false
        html.enabled = true
    }
}


sonarqube {
    properties {
        property "sonar.projectName", "DeleteVehicleService"
        property "sonar.projectKey", "com.ford.sca.cap:DeleteVehicleService-New"
        property "sonar.projectVersion", "1.0.0-SNAPSHOT"
        property "sonar.sources", "src/main/java"
        property "sonar.language", "java"
        property "sonar.binaries", "build/classes"
        property "sonar.tests", "src/test/java"
        properties["sonar.sourceEncoding"] = 'UTF-8'
        properties["sonar.java.junit.reportsPath"] = 'build/test-results/test'
        properties["sonar.jacoco.reportPath"] = 'build/reports/jacoco/coverage.xml'
        properties["sonar.java.coveragePlugin"] = 'jacoco'
    }
}

dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    compile('org.springframework.boot:spring-boot-starter-web-services')
    compile('org.springframework.boot:spring-boot-starter-data-redis')
    compile("io.pivotal.spring.cloud:spring-cloud-services-starter-config-client:1.6.1.RELEASE")
    compile('org.springframework.cloud:spring-cloud-starter-oauth2')
    compileOnly 'com.ford.cloudnative:activedirectory-spring-oauth2-service:1.1.3-RELEASE'
    if (project.hasProperty("azureAD")) {
        compile 'com.ford.cloudnative:activedirectory-spring-oauth2-service:1.1.3-RELEASE'
    }
    compile("io.pivotal.spring.cloud:spring-cloud-sso-connector:2.0.0.RELEASE")
    compile("org.springframework.boot:spring-boot-starter-amqp")
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('javax.validation:validation-api:1.1.0.Final')
    compile('org.hibernate:hibernate-validator:5.4.2.Final')
    compile files('./lib/mssql-jdbc-7_0_0-jre8.jar')
    compile group: 'io.springfox', name: 'springfox-swagger2', version: '2.8.0'
    compile group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.8.0'
    compile group: 'org.springframework.retry', name: 'spring-retry'
    compile('org.springframework.cloud:spring-cloud-starter-sleuth:1.3.0.RELEASE')
    compile("org.springframework.cloud:spring-cloud-stream-binder-rabbit:1.2.1.RELEASE")
    compile("org.aspectj:aspectjweaver")
//    compile("com.zaxxer:HikariCP:3.0.0")
    compileOnly(group: 'org.projectlombok', name: 'lombok', version: '1.18.2')

    //Test
    testCompile('org.springframework.boot:spring-boot-starter-test')
}

sourceSets {
    main {
        java {
            if (project.hasProperty("azureAD")) {
                exclude 'com/ford/sca/cap/**/PcfUaaConfig.java'
                exclude 'com/ford/sca/cap/**/SecurityConfig.java'
            } else {
                exclude 'com/ford/sca/cap/**/AzureADConfig.java'
            }
        }
    }
    functionalTest {
        java {
            compileClasspath += main.output + test.output
            runtimeClasspath += main.output + test.output
            srcDir file('src/functionalTest/java')
        }
        resources.srcDir file('src/functionalTest/resources')
    }
}

configurations {
    functionalTestCompile.extendsFrom testCompile
    functionalTestRuntime.extendsFrom testRuntime
}

tasks.withType(Test) {
    beforeTest { desc ->
        println "Preparing to execute test [${desc.className}].${desc.name}"
    }
    afterTest { desc, result ->
        println "Executed the test [${desc.className}].${desc.name} and the result is ${result.resultType}"
    }
    reports.html.destination = file("${reporting.baseDir}/${name}")
}

task functionalTest(type: Test) {
    description = "Functional testing for the process"
    group = "Test"
    testClassesDir = sourceSets.functionalTest.output.classesDir
    classpath = sourceSets.functionalTest.runtimeClasspath
    outputs.upToDateWhen { false }
}



