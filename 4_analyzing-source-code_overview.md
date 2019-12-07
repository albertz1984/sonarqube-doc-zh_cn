# 分析代码
## 概述
SonarQube平台安装完成后，可安装扫描器（Scanner）来开始创建项目了。可根据构建工具，从如下选择最适合的扫描器：

- Gradle - SonarScanner for Gradle
- MSBuild - SonarScanner for MSBuild
- Maven - use the SonarScanner for Maven
- Jenkins - SonarScanner for Jenkins
- Azure DevOps - SonarQube Extension for Azure DevOps
- Ant - SonarScanner for Ant
- anything else (CLI) - SonarScanner

```
我们不建议在运行SonarQube分析的机器上运行杀毒软件，这可能引起一些未知的问题。
```
## 代码分析会输出什么？
SoanrQube能够分析20多种编程语言。分析结果包含质量度量和问题（即issue，指代码中违反编码规则的情况）。尽管如此，哪些内容会被纳入分析，取决于不同的语言。
- 对于所有语言，“blame”数据会被从版本控制系统自动导入。SonarQube自带Git和SVN支持。其他版本控制系统需要通过安装插件来实现支持。
- 对于所有语言，会对源代码进行静态分析（如Java文件，COBOL程序等）
- 对于特定的语言，会对编译后的目标码进行静态分析（如Java中.class文件，C#中的.dll文件等）。

## 会分析所有文件吗？
默认情况下，只有语言分析器能识别的文件会在分析过程中被载入项目。例如：如果你的SonarQube实例只安装了SonarJava和SonarJS，所有.java和.js文件将被加载，但.xml文件会被忽略。

## What about branches and pull requests?（Skipped）

## 代码分析过程中会发生什么？
在分析过程中，从服务器请求数据，并对文件进行分析。分析结果以报告的形式回传到服务器，并在服务器端进行处理。

分析报告会在队列中被依次处理，因此分析刚刚完成时，可能在短时间内还无法看到分析结果。你可以在项目首页看到一个图标，表示分析正在进行中。

![IMG](https://docs.sonarqube.org/7.9/images/backgroundTaskProcessingInProgress.jpeg)

分析完成后图标消失。但如果分析失败，图标将会变成下面这样：

![IMG](https://docs.sonarqube.org/7.9/images/backgroundTaskProcessingFailedIcon.jpeg)

## FAQ
### Q.如何解决分析错误：java.lang.OutOfMemoryError: GC overhead limit exceeded.

A.这表明你的项目太大，或太复杂。用默认分配的内存无法完成分析。你需要给分析进程分配更大的堆（使用```-Xmx[内存大小]```）。有些持续集成平台可能提供一个配置项来让你制定必要的值。例如：在Jenkins作业中使用Maven Build Step来运行分析。或者使用Java相关选项来设定更高的值。

> 原文链接：https://docs.sonarqube.org/7.9/analysis/overview/
