# SonarScanner for Maven

By [SonarSource](https://www.sonarsource.com/) – GNU LGPL 3 – [JIRA](https://jira.sonarsource.com/browse/MSONAR) – [源代码](https://github.com/SonarSource/sonar-scanner-maven) 

当前版本: **SonarScanner for Maven 3.6.0.1398**

我们推荐使用SonarScanner作为Maven项目的默认分析工具。

用户可通过一个常规的Maven目标(goal)来执行SonarQube分析。任何情况下，只要有Maven可用，SonarQube分析就可以执行。例如：在开发者本地构建、持续集成服务器上执行SonarQube时无需单独手工下载和配置一个SonarQube Runner。Maven构建已经包含了很多SonarQube成功分析一个项目所需要的信息。基于这些信息，分析过程已经被预先配置刚好，明显减少了手工配置。

## 前提
- Maven 3.x
- 至少使用SonarQube服务器支持的最低Java版本

## 全局设定
编辑 [settings.xml](http://maven.apache.org/settings.html) 文件来设置插件的前缀，如有需要也可以设定SonarQube服务器的URL。
该文件位置： `$MAVEN_HOME/conf` 或 `~/.m2`。

例如：
```xml
<settings>
    <pluginGroups>
        <pluginGroup>org.sonarsource.scanner.maven</pluginGroup>
    </pluginGroups>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!-- Optional URL to server. Default value is http://localhost:9000 -->
                <sonar.host.url>
                  http://myserver:9000
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```
## 分析
分析一个Maven项目，就是在主项目的`pom.xml`文件所在目录下执行一个Maven goal(目标)：`sonar:sonar`。
```bash
mvn clean verify sonar:sonar
```
在某些情况下你可能想要将`sonar:sonar`作为一个独立步骤执行。对于多模块项目，记得第一步先执行`install`。
```bash
mvn clean install
mvn sonar:sonar
```
如果不想使用最新版本的sonar-maven-plugin插件，请指定版本号：
```bash
mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar
```
如果要获取覆盖率信息，您需要在分析执行前先生成覆盖率报告。

## 配置分析
大多数分析属性(analysis properties)会从你的项目信息中读取(pom.xml)。如果想要覆盖掉默认值或指定更多参数，请参考分析参数([Analysis Parameters](http://localhost:9000/documentation/analysis/analysis-parameters/))中的各个属性，在pom.xml文件的`properties`部分增加配置。如：
```xml
<properties>
  <sonar.buildString> [...] </sonar.buildString>
</properties>
```

## 示例项目
https://github.com/SonarSource/sonar-scanning-examples/tree/master/sonarqube-scanner-maven

## 在分析中排除一个模块
- 在要排除的模块，在该模块的pom.xml中配置属性 `<sonar.skip>true</sonar.skip>`
- 使用build profiles来排除模块(例如集成测试)。
- 使用Advanced Reactor Options(例如 “-pl”)，例如：`mvn sonar:sonar -pl !module2`

## 如何解决Maven插件的版本问题
推荐指定Maven插件的版本。
```xml
<build>
  <pluginManagement>
    <plugins>
      <plugin>
        <groupId>org.sonarsource.scanner.maven</groupId>
        <artifactId>sonar-maven-plugin</artifactId>
        <version>3.6.0.1398</version>
      </plugin>
    </plugins>
  </pluginManagement>
</build>
```

## 故障排除
### 如果你遇到java.lang.OutOfMemoryError
设定环境变量 `MAVEN_OPT`。
Unix/Linux环境：`export MAVEN_OPTS="-Xmx512m"`
Windows环境不要使用双引号：`set MAVEN_OPTS=-Xmx512m`
