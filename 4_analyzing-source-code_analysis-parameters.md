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
| `sonar.working.directory`         | Set the working directory for an analysis triggered with the SonarScanner or the SonarScanner for Ant (versions greater than 2.0). This property is not compatible with the SonarScanner for MSBuild. Path must be relative, and unique for each project. ![img](https://docs.sonarqube.org/7.9/images/exclamation.svg) Beware: the specified folder is deleted before each analysis. | `.sonar`                                                     |
| `sonar.scm.provider`              | This property can be used to explicitly tell SonarQube which SCM plugin should be used to grab SCM data on the project (in case auto-detection does not work). The value of this property is always lowercase and depends on the plugin (ex. "tfvc" for the TFVC plugin). Check the documentation page of each plugin for more. |                                                              |
| `sonar.scm.forceReloadAll`        | By default, blame information is only retrieved for changed files. Set this property to `true` to load blame information for all files. This can be useful is you feel that some SCM data is outdated but SonarQube does not get the latest information from the SCM engine. |                                                              |
| `sonar.scm.exclusions.disabled`   | For supported engines, files ignored by the SCM, i.e. files listed in `.gitignore`, will automatically be ignored by analysis too. Set this property to `true` to disable that feature. |                                                              |
| `sonar.scm.revision`              | Overrides the revision, for instance the Git sha1, displayed in analysis results. By default value is provided by the CI environment or guessed by the checked-out sources. |                                                              |
| `sonar.buildString`               | The string passed with this property will be stored with the analysis and available in the results of `api/project_analyses/search`, thus allowing you to later identify a specific analysis and obtain its ID for use with `api/project_analyses/set_baseline`. |                                                              |
| `sonar.analysis.[yourKey]`        | This property stub allows you to insert custom key/value pairs into the analysis context, which will also be passed forward to [webhooks](https://docs.sonarqube.org/7.9/project-administration/webhooks/). |                                                              |

### 重复代码

| Key                                   | Description                                                  | Default |
| ------------------------------------- | ------------------------------------------------------------ | ------- |
| `sonar.cpd.${language}.minimumtokens` | A piece of code is considered duplicated as soon as there are at least 100 duplicated tokens in a row (override with `sonar.cpd.${language}.minimumTokens`) spread across at least 10 lines of code (override with `sonar.cpd.${language}.minimumLines`). For Java projects, a piece of code is considered duplicated when there is a series of at least 10 statements in a row, regardless of the number of tokens and lines. This threshold cannot be overridden. | 100     |
| `sonar.cpd.${language}.minimumLines`  | (see above)                                                  | 10      |

### 分析日志

| Key                              | Description                                                  | Default                            |
| -------------------------------- | ------------------------------------------------------------ | ---------------------------------- |
| `sonar.log.level`                | Control the quantity / level of logs produced during an analysis. `DEBUG`: Display `INFO` logs + more details at `DEBUG` level. Similar to `sonar.verbose=true`. `TRACE`: Display `DEBUG` logs + the timings of all ElasticSearch queries and Web API calls executed by the SonarScanner. | `INFO`                             |
| `sonar.verbose`                  | Add more detail to both client and server-side analysis logs. Activates `DEBUG` mode for the scanner, and adds client-side environment variables and system properties to server-side log of analysis report processing. ![img](https://docs.sonarqube.org/7.9/images/exclamation.svg)NOTE: There is the potential for this setting to expose sensitive information such as passwords if they are stored as server-side environment variables. | false                              |
| `sonar.showProfiling`            | Display logs to see where the analyzer spends time. This parameter generates a file containing these timing infos in `<workingDir>/profiling/<moduleKey>-profiler.xml` where `<workingDir>` is: `.sonar/profiling/` when analysis is run with SonarScanner, and `target/sonar/profiling/` when SonarScanner for Maven is used. | `false`                            |
| `sonar.scanner.dumpToFile`       | Outputs to the specified file the full list of properties passed to the scanner API as a means to debug analysis. |                                    |
| `sonar.scanner.metadataFilePath` | Set the location where the scanner writes the `report-task.txt` file containing among other things the `ceTaskId`. | value of `sonar.working.directory` |

 