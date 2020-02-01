# SonarScanner for Gradle

By [SonarSource](https://www.sonarsource.com/) – GNU LGPL 3 – [缺陷跟踪](https://jira.sonarsource.com/browse/SONARGRADL) – [源代码](https://github.com/SonarSource/sonar-scanner-gradle)
当前版本: **SonarQube Scanner for Gradle 2.7.1**

使用SonarScanner for Gradle插件可以很方便地在Gradle项目中运行SonarQube分析。无论何处，只要Gradle可用，就可以通过一个常规的Gradle任务来执行SonarQube分析，而不用手工下载安装和维护一套SonarQube执行器，Gradle构建已经包含了很多成功运行SonarQube分析所需的信息，通过预先配置相关信息，并基于此运行分析，能够显著降低手工配置的成本。

## 前提条件

- Gradle  2.14+
- 至少使用SonarQube服务器支持的最低Java版本

由javac编译出的字节码是必须的，包括Android项目。

## 配置插件

安装过程是自动的，但特定的全局属性依然需要配置。建议使用`~/.gradle/gradle.properties`文件来配置全局属性。请注意：Scanner使用系统属性，因此所有的属性都应当加上`systemProp`前缀。

```properties
# gradle.properties
systemProp.sonar.host.url=http://localhost:9000
 
#----- Token generated from an account with 'publish analysis' permission
systemProp.sonar.login=<token>
```

## 执行分析

首先，在构建中激活Scanner。对于Gradle 2.1及以上版本，在`build.gradle`中：
```java
plugins {
  id "org.sonarqube" version "2.7"
}

```

详见： https://plugins.gradle.org/plugin/org.sonarqube

假定本地环境已经部署了SonarQube，不再需要进行其他配置。

执行`gradle sonarqube`并等待它运行完成，然后打开console输出底部所示的web页面到SonarQube查看分析结果。

## 多层级项目分析

要分析多模块（多层级）项目，应在根项目中使用SonarQube插件。通常情况下应该是的Gradle构建中的顶层项目。与分析相关的信息应该被整体配置在项目的`sonarqube`块中。同时，所有在命令行中配置的属性也被应用到项目中。

```
// build.gradle
sonarqube {
    properties {
        property "sonar.sourceEncoding", "UTF-8"
    }
}
```

各个子项目间共享的配置可以在`subprojects`块中配置。

```
// build.gradle
subprojects {
    sonarqube {
        properties {
            property "sonar.sources", "src"
        }
    }
}
```

特定项目的信息在对应项目的`sonarqube`块中配置。

```
// build.gradle
project(":project1") {
    sonarqube {
        properties {
            property "sonar.branch", "Foo"
        }
    }}
```

To skip SonarQube analysis for a particular subproject, set sonarqube.skipProject to true.

如果某个项目要跳过SonarQube分析，在项目中配置sonarqube.skipProject为true

```
// build.gradle
project(":project2") {
    sonarqube {
        skipProject = true
    }
}
```

## 任务间的依赖关系

All tasks that produce output that should be included in the SonarQube analysis need to be executed before the `sonarqube` task runs. Typically, these are compile tasks, test tasks, and code coverage tasks. To meet these needs, the plugins adds a task dependency from `sonarqube` on `test` if the Java plugin is applied. Further task dependencies can be added as needed. For example:

如果任务的输出物需要纳入SonarQube分析，则该任务需要在`sonarqube`任务前执行完毕。通常，这类任务包含编译、单元测试及覆盖率统计等。为了满足这个要求，如果使用了Java插件，则SonarScanner for Gradle会为`sonarqube`任务添加一个`test`依赖。

```
// build.gradle
project.tasks["sonarqube"].dependsOn "anotherTask"
```

## 示例项目

我们提供了一个可运行的简单示例，你可以对照检查自己的配置是否正确:
https://github.com/SonarSource/sonar-scanning-examples/tree/master/sonarqube-scanner-gradle

## 分析属性的默认值

SonarScanner for Gradle智能地使用Gradle对象模型中的信息作为大部分标准分析参数的默认值。列表如下：

标准SonarQube属性与Gradle默认值对照:

| Property                   | Gradle 默认值                                                |
| :------------------------- | :----------------------------------------------------------- |
| `sonar.projectKey`         | `[${project.group}:]${project.name}` 用于根模块; `<root module key>:<module path>` 用于子模块； |
| `sonar.projectName`        | `${project.name}`                                            |
| `sonar.projectDescription` | `${project.description}`                                     |
| `sonar.projectVersion`     | `${project.version}`                                         |
| `sonar.projectBaseDir`     | `${project.projectDir}`                                      |
| `sonar.working.directory`  | `${project.buildDir}/sonar`                                  |

Notice that additional defaults are provided for projects that have the java-base or java plugin applied:

| Property                    | Gradle 默认值                                                |
| :-------------------------- | :----------------------------------------------------------- |
| `sonar.sourceEncoding`      | `${project.compileJava.options.encoding}`                    |
| `sonar.java.source`         | `${project.sourceCompatibility}`                             |
| `sonar.java.target`         | `${project.targetCompatibility}`                             |
| `sonar.sources`             | `${sourceSets.main.allSource.srcDirs}` (仅包括已存在的目录)  |
| `sonar.tests`               | `${sourceSets.test.allSource.srcDirs}` (仅包括已存在的目录)  |
| `sonar.java.binaries`       | `${sourceSets.main.output.classesDir}`                       |
| `sonar.java.libraries`      | `${sourceSets.main.compileClasspath}` (仅包括文件; 如有必要会添加rt.jar和jfxrt.jar) |
| `sonar.java.test.binaries`  | `${sourceSets.test.output.classeDir}`                        |
| `sonar.java.test.libraries` | `${sourceSets.test.compileClasspath}` (仅包括文件; 如有必要会添加rt.jar和jfxrt.jar) |
| `sonar.junit.reportPaths`   | `${test.testResultsDir}` (如果该目录存在)                    |

Groovy项目使用所有的Java默认值,此外还包括:

| Property                | Gradle 默认值                          |
| :---------------------- | :------------------------------------- |
| `sonar.groovy.binaries` | `${sourceSets.main.output.classesDir}` |

使用Jacoco插件时还包括：

| 属性                             | Gradle 默认值               |
| :------------------------------- | :-------------------------- |
| `sonar.jacoco.reportPaths`       | `${jacoco.destinationFile}` |
| `sonar.groovy.jacoco.reportPath` | `${jacoco.destinationFile}` |

对于Android项目的还包括如下附加的默认值 (`com.android.application`, `com.android.library`, 或`com.android.test`) 默认情况下，debug类型的第一个variant会被用来配置分析。你可以使用`androidVariant`属性来覆盖要使用的variant名称。

```
build.gradle
sonarqube {
    androidVariant 'fullDebug'
}
```

|                Property                 |                        Gradle default                        |
| :-------------------------------------: | :----------------------------------------------------------: |
| `sonar.sources` (for non test variants) | `${variant.sourcesets.map}` (ManifestFile/CDirectories/AidlDirectories/AssetsDirectories/CppDirectories/JavaDirectories/RenderscriptDirectories/ResDirectories/ResourcesDirectories) |
|    `sonar.tests` (for test variants)    | `${variant.sourcesets.map}` (ManifestFile/CDirectories/AidlDirectories/AssetsDirectories/CppDirectories/JavaDirectories/RenderscriptDirectories/ResDirectories/ResourcesDirectories) |
|      `sonar.java[.test].binaries`       |                 `${variant.destinationDir}`                  |
|      `sonar.java[.test].libraries`      |    `${variant.javaCompile.classpath} + ${bootclasspath}`     |
|           `sonar.java.source`           |         `${variant.javaCompile.sourceCompatibility}`         |
|           `sonar.java.target`           |         `${variant.javaCompile.targetCompatibility}`         |

## 手工传参 / 覆盖默认值

SonarScanner for Gradle会为项目及其子项目添加一个SonarQubeExtension扩展，该扩展支持配置或覆盖分析属性。

```groovy
// in build.gradle
sonarqube {
    properties {
        property "sonar.exclusions", "**/*Generated.java"
    }
}
```

SonarQube属性可以从命令行设定，或在系统属性中设定一个与SonarQube属性命名完全相同的属性。这种方法在处理密码等敏感信息时很有用、也可以用于环境信息或临时性配置。

```bash
gradle sonarqube -Dsonar.host.url=http://sonar.mycompany.com -Dsonar.verbose=true
```

While certainly useful at times, we recommend keeping the bulk of the configuration in a (versioned) build script, readily available to everyone. A SonarQube property value set via a system property overrides any value set in a build script (for the same property). When analyzing a project hierarchy, values set via system properties apply to the root project of the analyzed hierarchy. Each system property starting with `sonar.` will be taken into account.

尽管有时候这种方法很好用，我们还是建议将配置信息都存放在构建脚本中并纳入版本控制，对团队成员共享。在系统属性中设定的SonarQube属性会覆盖构建脚本中设定的同名属性。当分析多层级项目时，通过系统属性设定的属性值会作用于根项目，每一个以`sonar.`开头的系统属性都会被使用。

### 分析自定义的代码源

By default, the SonarScanner for Gradle passes on the project's main source set as production sources, and the project's test source set as test sources. This works regardless of the project's source directory layout. Additional source sets can be added as needed.

默认情况下，SonarScanner for Gradle会将项目的main代码设定为生产代码、test代码设定为测试代码，二不考虑项目的源码目录结构。附加的代码源可以通过如下方式添加：

```groovy
// build.gradle
sonarqube {
    properties {
        properties["sonar.sources"] += sourceSets.custom.allSource.srcDirs
        properties["sonar.tests"] += sourceSets.integTest.allSource.srcDirs
    }
}
```

## 高级话题

### 更多配置SonarQube属性的信息

我们来仔细看一下`sonarqube.properties` `{}`这个块。和上面例子中看到的一样，`property()`方法允许你设定新的属性，或者覆盖现有的属性。更进一步，至此所有已经配置的属性，包括所有Gradle中已经预先配置的属性，都是可用的。

上述对照表中的属性可以通过标准的Groovy语法来读写。为了协助控制，属性值仍然是有自己的“惯用”类型（File、List等）。sonarProperties块被评估后，这些值会被转换为字符串：集合值会被递归地转换为逗号分隔的字符串；所有其他值会通过调用`toString()`方法进行转换。

Because the `sonarProperties` block is evaluated lazily, properties of Gradle's object model can be safely referenced from within the block, without having to fear that they have not yet been set.
（最后一段没看懂，保留原文，求指教）