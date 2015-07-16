# 安装

## 先决条件

Gradle 需要 1.5 或更高版本的 JDK.Gradle 自带了 Groovy 库，所以不需要安装 Groovy。Gradle 会忽略已经安装的 Groovy。Gradle 会使用 ptah (这里的"path"应该是指 PATH 环境变量。[Rover12421]译注) 中的 JDK(可以使用 java -version 检查)。当然，你可以配置 JAVA_HOME 环境变量来指向 JDK 的安装目录。 

## 下载

从 Gralde 官方网站下载 Gradle 的最新发行包。

## 解压

Gradle 发行包是一个 ZIP 文件。完整的发行包包括以下内容(官方发行包有 full 完整版，也有不带源码和文档的版本，可根据需求下载。[Rover12421]译注):

- Gradle 可执行文件
- 用户手册 (有 PDF 和 HTML 两种版本)
- DSL 参考指南
- API 手册(Javadoc 和 Groovydoc)
- 样例，包括用户手册中的例子，一些完整的构建样例和更加复杂的构建脚本 
- 源代码。仅供参考使用,如果你想要自己来编译 Gradle 你需要从源代码仓库中检出发行版本源码，具体请查看 Gradle 官方主页。

## 配置环境变量

运行 gradle 必须将 GRADLE_HOME/bin 加入到你的 PATH 环境变量中。

## 测试安装

运行如下命令来检查是否安装成功.该命令会显示当前的 JVM 版本和 Gradle 版本。

```
gradle -v 
```

## JVM 参数配置

Gradle 运行时的 JVM 参数可以通过 GRADLE_OPTS 或 JAVA_OPTS 来设置.这些参数将会同时生效。 JAVA_OPTS 设置的参数将会同其它 JAVA 应用共享，一个典型的例子是可以在 JAVA_OPTS 中设置代理和 GRADLE_OPTS 设置内存参数。同时这些参数也可以在 gradle 或者 gradlew 脚本文件的开头进行设置。