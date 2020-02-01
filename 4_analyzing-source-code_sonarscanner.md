# SonarScanner

译者注：SonarScanner是Sonar命令行工具（可视为客户端），不依赖于Ant、Maven等构建工具，可独立运行。

**下载 SonarScanner 4.0** - 兼容SonarQube 6.7+ (LTS) By [SonarSource](https://www.sonarsource.com/) – GNU LGPL 3 – [缺陷跟踪](https://jira.sonarsource.com/browse/SQSCANNER) – [源代码](https://github.com/Sonarsource/sonar-scanner-cli)

[Linux 64-bit](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.0.0.1744-linux.zip) | [Windowx 64-bit](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.0.0.1744-windows.zip) | [Mac OS X 64-bit](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.0.0.1744-macosx.zip) | [其他系统*](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.0.0.1744.zip) *需要装好JDK（版本要求和Server端一致）。

The SonarScanner is the scanner to use when there is no specific scanner for your build system.
如果你的构建工具没有对应的Scanner可用，请使用SonarScanner独立程序。

## 安装

- 将下载的文件解压到你指定的目录。以下我们用`$install_directory`来指代这个路径。
- 更新如下配置文件中的全局设定，指向你的SonarQube服务器。`$install_directory/conf/sonar-scanner.properties`:

```
#----- Default SonarQube server
#sonar.host.url=http://localhost:9000
```

- 将这个路径 `$install_directory/bin` 加入到你的PATH环境变量。

- 验证安装是否正确：打开一个终端窗口，执行命令`sonar-scanner -h` （Windows系统是`sonar-scanner.bat -h`）。你应该能够得到如下的输出：

  ```
  usage: sonar-scanner [options]
  
  Options:
    -D,--define <arg>     Define property
    -h,--help             Display help information
    -v,--version          Display version information
    -X,--debug            Produce execution debug output
  ```

如果需要查看更多的调试信息，可在命令中加上这3个参数其中一个：`-X`, `--verbose`, or `-Dsonar.verbose=true`.

## 使用

在开发工程的根目录创建一个**项目配置文件**: `sonar-project.properties`

```properties
sonar-project.properties
# must be unique in a given SonarQube instance
# 项目唯一标识（projectKey），在一个SoanrQube服务器范围内必须唯一。
sonar.projectKey=my:project

# --- optional properties 可选属性 ---

# defaults to project key
# 项目名称(projectName)，如不配置，默认使用projectKey
#sonar.projectName=My project
# defaults to 'not provided'
# 项目版本号，如不配置，默认为“not provided”(未指定)
#sonar.projectVersion=1.0
 
# Path is relative to the sonar-project.properties file. Defaults to .
# 扫描的源码路径(sources)，为此文件所在路径的相对路径。默认为“.”(当前目录)。
#sonar.sources=.
 
# Encoding of the source code. Default is default system encoding
# 源码的编码字符集，默认为系统使用的默认编码字符集。
#sonar.sourceEncoding=UTF-8
```

在开发工程根目录执行此命令来启动分析:`sonar-scanner`

## 示例项目

To help you get started, simple project samples are available for most languages on github. 为了帮助你快速上手，我们在GitHub上提供了简单的示例项目。包含了大多数编程语言。你可以到这里 [查看](https://github.com/SonarSource/sonar-scanning-examples) 或直接 [下载](https://github.com/SonarSource/sonar-scanning-examples/archive/master.zip). 你可以在 `sonarqube-scanner/src` 这个路径找到这些文件。

## sonar-project.properties文件的替代选项

如果不能在工程根目录下创建sonar-project.properties文件，可以用下面的方法来替代：

- 这些属性可以再命令行中直接通过-D参数指定。 例如:

```bash
sonar-scanner -Dsonar.projectKey=myproject -Dsonar.sources=src1
```

- The property project.settings can be used to specify the path to the project configuration file (this option is incompatible with the `sonar.projectBaseDir` property). 可使用`project.settings`这个属性来指定项目配置文件的路径（注意：这个属性和`sonar.projectBaseDir`不兼容）。例如:

```bash
sonar-scanner -Dproject.settings=../myproject.properties
```

- The root folder of the project to analyze can be set through the `sonar.projectBaseDir` property since SonarScanner 2.4. This folder must contain a `sonar-project.properties` file if `sonar.projectKey` is not specified on the command line. Additional analysis parameters can be defined in this project configuration file or through command-line parameters.
- 从SonarScanner 2.4版本起，待分析项目的根目录可通过`sonar.projectBaseDir`这个属性来指定。如果分析命令中没有通过`sonar.projectKey`属性来指定项目唯一标识，则这个指定的目录必须包含一个`sonar-project.properties`文件。其他附加的分析参数可以通过这个项目配置文件来配置，或直接在命令行中添加。

## Alternate Analysis Directory

If the files to be analyzed are not in the directory where the analysis starts from, use the `sonar.projectBaseDir` property to move analysis to a different directory. E.G. analysis begins from `jenkins/jobs/myjob/workspace` but the files to be analyzed are in `ftpdrop/cobol/project1`.

如果要分析的文件没有存放在分析执行所在的目录下，请使用`sonar.projectBaseDir` 属性来将分析移动到其他目录。例如：分析在`jenkins/jobs/myjob/workspace`执行，但要分析的文件存放在 `ftpdrop/cobol/project1`这个目录下。

```properties
sonar-project.properties
sonar.projectBaseDir=/home/ftpdrop/cobol/project1
sonar.sources=src
sonar.cobol.copy.directories=/copy
```
请参见“分析参数”(Analysis Parameters)来了解更多信息。


## 故障排除

**报错：Java heap space或 java.lang.OutOfMemoryError**
使用`SONAR_SCANNER_OPTS`环境变量来增加内存

```bash
export SONAR_SCANNER_OPTS="-Xmx512m"
```

在Windows环境，注意不要使用双引号：

```
set SONAR_SCANNER_OPTS=-Xmx512m
```

**报错：Unsupported major.minor version**

升级执行分析使用的Java版本，或者使用SonarScanner自带的JRE。

**报错：Property missing: `sonar.cs.analyzer.projectOutPaths'. No protobuf files will be loaded for this project.**

SonarScanner命令行工具无法分析.NET项目。请使用SonarScanner for MSBuild。如果就是用的SonarScanner for MSBuild，确保你没有触碰到一个已知的限制。

译者注：上面这句不懂，先直译了，请熟悉.NET的大神指点，原文是：

> If you are running Scanner for MSBuild, ensure that you are not hitting a known limitation.