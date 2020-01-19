# SonarScanner for Ant

**下载 SonarScanner 3.3** - 兼容 SonarQube 6.7及以上版本 (LTS)
By [SonarSource](https://www.sonarsource.com/) – GNU LGPL 3 – [JIRA](https://jira.sonarsource.com/browse/ANTTASK) – [源代码](https://github.com/SonarSource/sonar-scanner-ant)

[下载地址](https://binaries.sonarsource.com/Distribution/sonarqube-ant-task/sonarqube-ant-task-2.6.0.1426.jar)



SonarScanner for Ant使用Ant任务（task）讲SonarQube分析集成到Apache Ant构建脚本中。

SonarScanner for Ant是一个Ant任务（Task），它将ScannerScanner打包，它通过调用SonarScanner，并将所有命名符合`sonar.*`这个规则的属性作为参数传递给SonarScanner来执行扫描。这种做法并不是Ant中的典型做法，但好处在于能够第一时间使用新版插件或新版SonarQube引入的新的分析参数。因此，想要成功执行SonarScanner for Ant，要严格遵守如下的属性名称。



## 使用

在你的Ant构建脚本中为sonar定义一个新的Ant target

```xml
<!-- build.xml -->
<project name="My Project" default="all" basedir="." xmlns:sonar="antlib:org.sonar.ant">
...
  
<!-- Define the SonarQube global properties (the most usual way is to pass these properties via the command line) -->
<property name="sonar.host.url" value="http://localhost:9000" />
 
...
  
<!-- Define the SonarQube project properties -->
<property name="sonar.projectKey" value="org.sonarqube:sonarqube-scanner-ant" />
<property name="sonar.projectName" value="Example of SonarScanner for Ant Usage" />
<property name="sonar.projectVersion" value="1.0" />
<property name="sonar.sources" value="src" />
<property name="sonar.java.binaries" value="build" />
<property name="sonar.java.libraries" value="lib/*.jar" />
...
 
<!-- Define SonarScanner for Ant Target -->
<target name="sonar">
    <taskdef uri="antlib:org.sonar.ant" resource="org/sonar/ant/antlib.xml">
        <!-- Update the following line, or put the "sonarqube-ant-task-*.jar" file in your "$HOME/.ant/lib" folder -->
        <classpath path="path/to/sonar/ant/task/lib/sonarqube-ant-task-*.jar" />
    </taskdef>
 
    <!-- Execute SonarScanner for Ant Analysis -->
    <sonar:sonar />
</target>
```

执行如下命令：

```bash
ant sonar
```



## 示例项目

https://github.com/SonarSource/sonar-scanning-examples/tree/master/sonarqube-scanner-ant



## 故障排除

**启用Debug日志**

要启动debug日志，使用标准的Ant verbose参数

```bash
ant sonar -v
```

## 

## 