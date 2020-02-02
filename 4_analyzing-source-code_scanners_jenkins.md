# SonarScanner for Jenkins

译者注：本文当介绍SonarQube的Jenkins插件，可以直接在Jenkins页面上配置扫描参数。

By [SonarSource](https://www.sonarsource.com/) – GNU LGPL 3 – [缺陷跟踪](https://jira.sonarsource.com/browse/SONARJNKNS) – [源代码](https://github.com/SonarSource/sonar-scanner-jenkins) 当前版本: **SonarScanner for Jenkins 2.9**

该插件可以再Jenkins全局配置中统一配置SonarQube服务器链接信息。这样可以通过标准的Jenkins构建步骤或Jenkins流水线描述语言（[Jenkins Pipeline DSL](https://jenkins.io/solutions/pipeline/)）来触发SonarQube分析。


- [SonarScanner](4_analyzing-source-code_scanner_cli.md)
- [SonarScanner for Maven](http://localhost:9000/documentation/analysis/scan/sonarscanner-for-maven/)
- [SonarScanner for Gradle](http://localhost:9000/documentation/analysis/scan/sonarscanner-for-gradle/)
- [SonarScanner for MSBuild](http://localhost:9000/documentation/analysis/scan/sonarscanner-for-msbuild/)

一旦作业运行完，插件将会检测到在构建过程中有SonarQube分析完成，并会在作业页面显示SonarQube图标，可点击它链接到SonarQube仪表板（Dashboard），并且会在作业页面显示质量阈（Quality Gate）的状态。

## 安装

1. [从Jenkins Update Center安装SonarScanner for Jenkins](https://plugins.jenkins.io/sonar).

   译者注：也可以直接下载hpi离线安装。

2. 配置SonarQube服务器。
   - 以管理员账号登录到Jenkins，然后进入 **Manage Jenkins > Configure System**
   - 找到SonarQube configuration部分, 点击Add SonarQube, 按提示填写相关信息.
   - 服务器的登录令牌（Authentication Token）应当被创建为密文'Secret Text'凭据。

## 分析 .NET 项目

**全局配置**

如果你想要使用SonarScanner for MSBuild来执行分析，如下的步骤是必须的。你可以按需定制一定数量的scanner实例。然后，对每个Jenkins作业来说，你可以指定使用哪个启动器来执行SonarQube分析。

1. 以管理员账号登录到Jenkins，然后进入 **Manage Jenkins > Global Tool Configuration**（全局工具配置）；
2. 点击**Add SonarScanner for MSBuild**
3. 安装最新版本的插件。勾选**Install automatically**来为Jenkins执行机自动配置插件。

如果在“Install from GitHub”没找到任何可用的版本，请先进行如下操作：**Manage Jenkins > Manage Plugins > Advanced > Check now**

**作业配置**

1. 打开作业配置的**Build**（构建）部分。
2. 添加SonarQube for MSBuild - Begin Analysis步骤。
3. 配置sonar.projectKey、sonar.projectName、sonar.projectVersion等分析参数。
4. 添加MSBuild构建步骤、或这行Windows批处理命令来执行构建。
5. 添加SonarQube for MSBuild - End Analysis步骤。

## 分析Java项目（使用Maven或Gradle构建）

**全局配置**

1. 以管理员账号登录到Jenkins，然后进入 **Manage Jenkins > Configure System** 
2. 找到SonarQube servers部分，然后勾选“Enable injection of SonarQube server configuration as build environment variables”（允许将SonarQube服务器配置信息作为环境变量注入到构建过程）

**作业配置**

1. 进入作业配置的 **Build Environment**（构建环境）页面. 
2. 启用**Prepare SonarScanner environment**来允许SonarQube服务器相关信息被注入到此作业。如果配置了多个SonarQube实例，你可以在下拉列表中选择一个。一旦环境变量可用，就可以在标准的Maven构建步骤中使用他们（Invoke top-level Maven targets，即：“调用顶层Maven目标”），设定需要包含的Maven Goal即可。或使用标准的Gradle构建步骤（Invoke Gradle script，即：”调用Gradle脚本“），设定需要执行的Gradle task即可。

Maven goal:

```
$SONAR_MAVEN_GOAL
```

Gradle task:

```
sonarqube
```

以上两种方式都可能涉及到SonarQube服务器登录验证。请确保全局配置中定义了正确的token.

## 分析其他类型的项目

**全局配置**

如果你想要使用SonarScanner来执行分析，如下的步骤是必须的。你可以按需定制一定数量的scanner实例。然后，对每个Jenkins作业来说，你可以指定使用哪个启动器来执行SonarQube分析。

1. 以管理员账号登录到Jenkins，然后进入 **Manage Jenkins > Global Tool Configuration**（全局工具配置）；
2. 找到**SonarScanner配置**，点击**Add SonarScanner**。可通过标准的Jenkins工具自动安装方式安装。你可以选择指定已安装的SonarScanner的路径（不要勾选"自动安装"）,或告诉Jenkins去一个远程位置获取安装包（勾选”自动安装“）

如果没有找到所有可用SonarScanner版本的下来列表，而只看到一个空白的输入框，这说明Jenkins还没有下载到所需的Update Center文件（默认频率为每天下载1次）。你可以强制它下载，点击**Manage Plugins > Advanced**中的”**Check Now**“按钮。

**作业配置**

1. 打开作业配置的**Build**（构建）部分。
2. 添加SonarScanner构建步骤。
3. 配置各种分析参数（属性）。你可以指定一个sonar-project.properties文件或在**Analysis properties**中直接填写。

## 使用Jenkins流水线（Pipeline）

我们提供一个`withSonarQubeEnv`块，使用它可以支持选择要使用的SonarQube服务器。在Jenkins全局配置中设定的服务器连接信息会被自动传送给Scanner

有需要的话，你可以覆盖`credentialId`如果你不想使用已经在全局配置中添加的凭据（例如：凭据是在Folder级别定义的）。

对于每一种Scanner，我们都给出了Pipeline脚本的示例。假设你在Unix slave上运行，并且配置了一台SonarQube服务器，名为 "My SonarQube Server" ，并配置好了所需的其他攻击。如果你在Windows slave上运行，请把`sh`换成`bat`。

SonarScanner:

```groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('SonarQube analysis') {
    def scannerHome = tool 'SonarScanner 4.0';
    withSonarQubeEnv('My SonarQube Server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
```

SonarScanner for Gradle:

```groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('SonarQube analysis') {
    withSonarQubeEnv() { // Will pick the global server connection you have configured
      sh './gradlew sonarqube'
    }
  }
}
```

SonarScanner for Maven:

```groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('SonarQube analysis') {
    withSonarQubeEnv(credentialsId: 'f225455e-ea59-40fa-8af7-08176e86507a', installationName: 'My SonarQube Server') { // You can override the credential to be used
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar'
    }
  }
}
```

SonarScanner for MSBuild:

```groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('Build + SonarQube analysis') {
    def sqScannerMsBuildHome = tool 'Scanner for MSBuild 4.6'
    withSonarQubeEnv('My SonarQube Server') {
      bat "${sqScannerMsBuildHome}\\SonarQube.Scanner.MSBuild.exe begin /k:myKey"
      bat 'MSBuild.exe /t:Rebuild'
      bat "${sqScannerMsBuildHome}\\SonarQube.Scanner.MSBuild.exe end"
    }
  }
}
```

## 在质量阈结果计算完成前暂停流水线

使用`waitForQualityGate`步骤，可以让流水线暂停运行，知道SonarQube分析完成并返回质量阈的状态。

### 前提条件:

- 在你的SonarQube服务器上配置一个webhook，指向`/sonarqube-webhook/`
- 在你的流水线脚本中使用`withSonarQubeEnv`步骤，以便SonarQube taskId 能够正确加载到流水线环境。

流水线脚本示例:

```groovy
node {
  stage('SCM') {
    git 'https://github.com/foo/bar.git'
  }
  stage('SonarQube analysis') {
    withSonarQubeEnv('My SonarQube Server') {
      sh 'mvn clean package sonar:sonar'
    } // 上送的SonarQube taskId会自动加载到流水线脚本
  }
}
  
// 无需占用一个节点
stage("Quality Gate"){
  timeout(time: 1, unit: 'HOURS') { // 为避免一些未知的问题导致流水线卡死，超时后流水线会被杀掉。
    def qg = waitForQualityGate() // 使用此前由withSonarQubeEnv采集的taskId
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
}
```

因为有webhook，本步骤可以用非常轻量化的方式来实施，没有必要占用一个节点来做轮询。并且它不会影响Jenkins重启（重启后步骤会被恢复）。请注意，为了避免race conditions，服务器端会被直接调用来检查任务是否已经完成。

（最后这句没看懂，求指教。主要是不明白什么叫race conditions）

> Note that to prevent race conditions, when the step starts (or is restarted) a direct call is made to the server to check if the task is already completed.

描述型流水线脚本示例:

```groovy
pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git url: 'https://github.com/foo/bar.git'
            }
        }
        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('My SonarQube Server') {
                    // Optionally use a Maven environment you've configured already
                    withMaven(maven:'Maven 3.5') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
```

如果你想要在同一条流水线中运行多个分析，并且使用`waitForQualityGate`，必须按顺序执行:

```groovy
pipeline {
    agent any
    stages {
        stage('SonarQube analysis 1') {
            steps {
                sh 'mvn clean package sonar:sonar'
            }
        }
        stage("Quality Gate 1") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('SonarQube analysis 2') {
            steps {
                sh 'gradle sonarqube'
            }
        }
        stage("Quality Gate 2") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
```