# 架构与集成
## 概述
SonarQube平台由如下4部分组成：
![IMG](https://docs.sonarqube.org/7.9/images/architecture-scanning.png)
1. SonarQube服务器，启动3个主要进程：
- Web服务，供开发人员、管理人员浏览代码质量快照、配置SonarQube实例。
- 搜索服务，实现用户界面上的搜索功能，基于Elasticsearch技术。
- 计算引擎，负责处理代码分析报告，并将报告写入数据库。
2. SonarQube数据库，用来存储：
- 该SonarQube实例的配置信息（安全设置、插件设置等）
- 项目的质量快照、视图等等（代码扫描分析结果）
3. 安装到服务器上的各种SonarQube插件，可包括各种开发语言、版本控制、集成、身份验证和管理插件。
4. 一个或多个SonarQube Scanner（代码扫描器，可视为客户端）。它们运行在你的构建/持续集成服务器上，用来分析代码。

## 集成
下面这个模型展示了SonarQube如何与其他ALM工具进行集成，并展示出SonarQube的各个组件将在哪里使用。
![IMG](https://docs.sonarqube.org/7.9/images/architecture-integrate.png)
1. 开发人员IDE中的代码，使用SonarLint进行本地分析。
2. 开发人员推送代码到版本控制系统，如：git，SVN，或TFVC...
3. 持续集成系统出发自动构建，并执行SonarScanner来分析代码。
4. 分析报告被发送到SonarQube服务器进行处理。
5. SonarQube服务器处理并存储分析报告结果到数据库，并在用户界面进行展示。
6. 开发人员通过用户界面来管理他们的代码问题，查看代码问题、添加评论、修复问题不断降低技术债。
7. 管理人员可查看分析报告。运维人员可使用API来自动化配置、提取数据，并可以通过使用JMX来监控SonarQube服务器。

## 关于机器和位置
- 一个SonarQube平台中，服务器和数据库是唯一的。
- 为了优化性能，平台的每个部分（服务器、数据库、扫描器）应当安装到不同的机器上，服务器所用的机器不宜再用作其他用途。
- SonarScanners可通过增加机器来扩容。
- 所有机器时间必须同步。
- SonarQube服务器和数据库必须在同一网络内。
- SonarScanners不必和服务器在统一网络内。
- SonarScanners和数据库之间没有直接通讯。

> 原文链接 https://docs.sonarqube.org/7.9/architecture/architecture-integration/
