# 分析参数

项目分析的相关属性可在多处进行设置，如下是优先级排序：

- **全局属性**，在web界面设置，对所有项目生效。（顶栏中：**Administration > Configuration > General Settings**）。
- **项目属性**，在web界面设置，覆盖全局属性（在项目视图中：**Administration > General Settings**）。
- **项目分析参数**，在项目分析配置文件中定义，或分析器的配置文件中定义，覆盖web界面上的配置。
- **分析参数（命令行参数）**，在启动分析时运行的命令行中定义（使用 ```-D```），覆盖项目分析参数。

请注意，只有在web界面设置的参数才会被存储在数据库中。例如，如果你通过命令行中的参数覆盖了```sonar.exclusions```配置，这个设定将不会被存储在数据库中。后续的分析，包括联机模式下的SonarLint分析，将依然按照web界面中的```sonar.exclusions```设定进行并存储在数据库中。

在web界面（包括全局、项目级别）上展示出的绝大多数的属性key都可用作**分析参数**，但下面列出的这些参数只能在执行分析的时候设定。

对于特定语言的测试覆盖率和执行结果相关参数，请看**测试覆盖率与执行结果**。

对于特定语言的外部问题报告相关参数，请看**外部问题**。



## 必须配置的参数

### 服务器

| 参数Key          | 描述      | 默认值                                          |
| ---------------- | --------- | ----------------------------------------------- |
| `sonar.host.url` | 服务器URL | [http://localhost:9000](http://localhost:9000/) |

### 项目配置

| 参数Key            | 描述                                                         | 默认值                                     |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------ |
| `sonar.projectKey` | 项目的唯一标识。允许使用字母、数字、减号`-`、下划线`_`、小数点`.`、和冒号`:`。至少要包含一个字符，但不能是数字。 | 若是Maven项目。取` <groupId>:<artifactId>` |



## 可选的参数

### 项目标识

| 参数Key                | 描述                      | 默认值                                                       |
| ---------------------- | ------------------------- | ------------------------------------------------------------ |
| `sonar.projectName`    | web界面上要显示的项目名称 | 取Maven项目的`<name>`值；若无，取`projectKey`。若不配置，并且数据库中已经有一个名称，则不会被覆盖。 |
| `sonar.projectVersion` | 项目的版本号              | 取Maven项目的`<version>`值，否则为` not provided`            |

### 身份验证

如果Anyone这个伪用户组没有权限执行分析，你就需要通过如下两个参数配置一个有权限执行分析的用户身份凭据。

| 参数Key          | 描述                                                         | 默认值 |
| ---------------- | ------------------------------------------------------------ | ------ |
| `sonar.login`    | 用户名或登录令牌，该用户需要有相关项目执行分析的权限。       |        |
| `sonar.password` | sonar.login中用户的密码，如果是设定的登录令牌，则此项无需配置。 |        |

### Web服务

| 参数Key            | 描述                                                         | 默认值 |
| ------------------ | ------------------------------------------------------------ | ------ |
| `sonar.ws.timeout` | 等待Web服务调用响应的最大时长。当你在分析过程中等待服务器响应Web服务调用时遇到超时，可尝试修改此配置的默认值。 | 60     |

### 项目配置

 

| 参数Key                           | 描述                                                         | 默认值                                                       |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sonar.projectDescription`        | 项目描述                                                     | Maven项目取`<description>`                                   |
| `sonar.links.homepage`            | 项目主页                                                     | Maven项目取`<url>`                                           |
| `sonar.links.ci`                  | 持续集成系统链接                                             | Maven项目取`<ciManagement><url>`                             |
| `sonar.links.issue`               | 缺陷跟踪系统链接                                             | Maven项目取`<issueManagement><url>`                          |
| `sonar.links.scm`                 | 项目代码库链接                                               | Maven项目取`<scm><url>`                                      |
| `sonar.sources`                   | 源代码路径，多个路径用逗号分隔                               | 从Maven、Gradle或MSBuild项目的构建系统中读取。若`sonar.sources` 和`sonar.tests`均未配置，则默认值为项目根目录。 |
| `sonar.tests`                     | 测试案例路径，多个路径用逗号分隔                             | 从Maven、Gradle或MSBuild项目的构建系统中读取。若无，默认为空。 |
| `sonar.sourceEncoding`            | 源代码使用的编码字符集，如 `UTF-8`, `MacRoman`, `Shift_JIS`. 该参数可被Maven项目的`project.build.sourceEncoding`替代 。可用的编码字符集取决于你所使用的JVM。 | 默认为操作系统所使用的的编码字符集                           |
| `sonar.externalIssuesReportPaths` | 通用问题报告（Generic Issues Report）的路径，多个路径用逗号分隔。 |                                                              |
| `sonar.projectDate`               | 给当前的分析指定一个日期。该参数仅在你想要补充一个为分析过的历史扫描结果时有用。格式为`yyyy-MM-dd`，例如：2010-12-01。你不能创建一个比最后一次分析的日期还早的分析，重建项目的历史扫描记录，必须按照时间顺序进行。最早日期要作为第一次分析。![img](https://docs.sonarqube.org/7.9/images/exclamation.svg)请注意，如果你想补充很久之前的历史扫描，可能需要调整数据清理的相关设置。 | 操作系统当前日期                                             |
| `sonar.projectBaseDir`            | 使用该属性来指定项目根目录。例如：分析从`jenkins/jobs/myjob/workspace`启动，但要分析的文件在`ftpdrop/cobol/project1`。这个路径可以是绝对或相对路径。不是指定源路径，而是指定源路径的上级路径。这里设定的路径，成为其他目录设定的起始点。请注意分析进行需要对这个路径有写入权限，这里将创建`sonar.working.directory` |                                                              |
| `sonar.working.directory`         | 设定SonarScanner（或SonarScanner for Ant 2.0以上版本）执行分析的工作目录。该属性与SonarScanner for MSBuild不兼容。必须使用相对路径，并且对每个项目设定唯一值。![img](https://docs.sonarqube.org/7.9/images/exclamation.svg)注意：指定的文件夹在每次分析执行前会被删除。 | `.sonar`                                                     |
| `sonar.scm.provider`              | 如果无法自动检测版本控制系统，可使用该属性明确指定SonarQube使用的版本控制系统插件，以便抓取版本控制系统数据。该属性的值总是使用小写字母，并由插件决定。例如：用“tfvc” 来指定TFVC插件，如有需要请查看其他插件的文档。 |                                                              |
| `sonar.scm.forceReloadAll`        | 默认情况下，只会抓取变更文件的blame信息。设定该属性为`true`则会抓取所有文件的blame信息。当你感到一些版本控制系统的数据不是最新的，但SonarQube又没有能够获取最新数据的情况下可以使用这个设定。 |                                                              |
| `sonar.scm.exclusions.disabled`   | 对于支持的版本控制系统，自动忽略被版本控制系统忽略的文件。例如，在`.gitignore`文件中被忽略的文件会在分析中被自动忽略。设定该属性为`true`会关闭这一功能。 |                                                              |
| `sonar.scm.revision`              | Overrides the revision, for instance the Git sha1, displayed in analysis results. By default value is provided by the CI environment or guessed by the checked-out sources. 覆盖分析结果中显示的版本控制系统修订，例如Git的sha1值。默认情况下，值由持续集成环境提供或由检出的代码推测。（不懂这个属性什么意思，估计也不会有人用到。原文保留在这里，谁知道的话欢迎贡献翻译。谢谢！） |                                                              |
| `sonar.buildString`               | 由该属性传入的字符串将和分析结果一起存储，并在`api/project_analyses/search`结果中可见。因此，你可以后续识别出一次特定的分析并获取它的ID用于`api/project_analyses/set_baseline` |                                                              |
| `sonar.analysis.[yourKey]`        | 该属性允许你在分析上下文中插入定制的key/value对，并会被传送给[webhooks](https://docs.sonarqube.org/7.9/project-administration/webhooks/) |                                                              |

### 重复代码

| Key                                   | Description                                                  | Default |
| ------------------------------------- | ------------------------------------------------------------ | ------- |
| `sonar.cpd.${language}.minimumtokens` | 一段代码被判定重复的条件：至少有100个连续的重复特征（token）分布在至少10行代码中。上述默认设定中的100和10两个参数分别使用`sonar.cpd.${language}.minimumtokens`和`sonar.cpd.${language}.minimumLines来覆盖`。在Java项目中，一段代码被判定重复的条件：至少10个连续的语句，不考虑重复特征数和行数。这个阈值不能被覆盖率。 | 100     |
| `sonar.cpd.${language}.minimumLines`  | (同上)                                                       | 10      |

### 分析日志

| Key                              | Description                                                  | Default                       |
| -------------------------------- | ------------------------------------------------------------ | ----------------------------- |
| `sonar.log.level`                | 设定分析过程中产生日志的数量和级别。`DEBUG`: 显示 `INFO` 日志 + 更多`DEBUG` 级别的日志. 类似于 `sonar.verbose=true`. `TRACE`: 显示 `DEBUG` 日志 + 所有ElasticSearch查询和SonarScanner调用WebAPI的计时。 | `INFO`                        |
| `sonar.verbose`                  | 记录更多的客户端和服务端的分析日志，激活Scanner的`DEBUG`模式并添加客户端侧的环境变量和系统属性到服务器端的分析报告处理日志。![img](https://docs.sonarqube.org/7.9/images/exclamation.svg)注意：用该属性可能泄露密码等被存储为服务器端环境变量的敏感信息。 | `false`                       |
| `sonar.showProfiling`            | 记录时间相关日志，用来查看分析器在哪里花了更多的时间。该参数生成一个文件，包含这些计时信息：`<workingDir>/profiling/<moduleKey>-profiler.xml`。其中`<workingDir>`在使用SonarScanner进行分析时是`.sonar/profiling/`；当使SonarScanner for Maven的时候是用`target/sonar/profiling/`。 | `false`                       |
| `sonar.scanner.dumpToFile`       | 将传给scanner API的所有属性清单输出到一个文件，用来进行排错分析。 |                               |
| `sonar.scanner.metadataFilePath` | 设定scanner写入`report-task.txt`文件的路径，包含`ceTaskId`及其他信息。 | `sonar.working.directory`的值 |

 

> 原文链接：<https://docs.sonarqube.org/latest/analysis/analysis-parameters/>

