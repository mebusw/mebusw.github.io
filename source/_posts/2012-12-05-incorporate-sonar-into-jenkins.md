title: 将Sonar引入 Jenkins
tags:
  - ci
  - sonar
  - jenkins
categories:
    - agile
    - engineering  
---

Sonar是一个集成的代码质量工具，主要用于Java，但现在也可以通过支持多种其他语言，例如javascript, C#等。

Jenkins是最流行的持续集成工具。

有两种方式在Jenkins Job中建立Sonar步骤： Standalone sonar runner 或 sonar-maven-plugin

(本文假设你已经安装了Jenkins)

<!--more-->

## 安装 Sonar

1.  Need to install a sonar instance, download from [http://www.sonarsource.org/downloads](http://www.sonarsource.org/downloads)
2.  start sonar service. E.g on a 64bit linux server, use:
``` [sh]
$ /bin/linux-x86-64/sonar.sh start
```
3.  the you can access http://127.0.0.1:9000  as Sonar URL. **Note: By default the built-in H2 database does not support other server to access this sonar instance remotely by the IP/URL, unless you manually config it to use your own database instance like mysql.**
4.  if you want to analyze javascript other than JAVA, download a sonar plugin:  [http://docs.codehaus.org/display/SONAR/JavaScript+Plugin](http://docs.codehaus.org/display/SONAR/JavaScript+Plugin)
5.  download and install sonar-runner if you don't use sonar-maven-plugin:  [http://docs.codehaus.org/display/SONAR/Installing+and+Configuring+Sonar+Runner](http://docs.codehaus.org/display/SONAR/Installing+and+Configuring+Sonar+Runner)
6.  config sonar in Jenkins:  http://127.0.0.1:8080/jenkins/configure



## A javascript project with standalone runner



1.  add a post-build step of "Invoke Standalone Sonar Analysis"
2.  in the Project properties field
3.  assume you already use Cobertura running unit tests


``` [xml]
\# required metadata
sonar.projectKey=
sonar.projectName=
sonar.projectVersion=1.0.0
\# path to source directories (required)
sonar.sources=
\# Uncomment this line to analyse a project which is not a java project.
\# The value of the property must be the key of the language.
sonar.language=javascript
```

## A JAVA project with standalone runner

1.  add a post-build step of "Invoke Standalone Sonar Analysis"
2.  in the **Project properties field**
3.  assume you already use Cobertura running unit tests, so to reuse the report

``` [xml]
\# required metadata
sonar.projectKey=
sonar.projectName=
sonar.projectVersion=1.0.0
\# path to source directories (required)
sonar.sources=
\# Uncomment this line to analyse a project which is not a java project.
\# The value of the property must be the key of the language.
sonar.language=java
sonar.dynamicAnalysis=reuseReports
sonar.surefire.reportsPath=
sonar.core.codeCoveragePlugin=cobertura
sonar.cobertura.reportPath=/cobertura/coverage.xml
```

## A Java(Maven) project with sonar-maven-plugin

1.  add below properties in **pom.xml**  (assume using emma but not reuse the report.  For other tool like jacoco, cobertura, see [http://docs.codehaus.org/display/SONAR/Code+Coverage+by+Unit+Tests](http://docs.codehaus.org/display/SONAR/Code+Coverage+by+Unit+Tests) )
2.  add a post-build action of "Sonar"

``` [xml]
UTF-8
java
<!-- Tells Sonar to reuse existing reports -->
true
<!-- Tells Sonar where the unit tests execution reports are -->
reports/junit
<!-- Tells Sonar to use Emma as the code coverage tool -->
emma
<!-- Tells Sonar where the unit tests code coverage reports are -->
reports/emma
```

## 更多例子:

[https://github.com/SonarSource/sonar-examples](https://github.com/SonarSource/sonar-examples)