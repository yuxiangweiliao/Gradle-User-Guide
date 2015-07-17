# 依赖管理基础

本章节介绍如何使用 Gradle 进行基本的依赖管理.

## 什么是依赖管理?

通俗来讲，依赖管理由如下两部分组成。首先，Gradle 需要知道项目构建或运行所需要的一些文件，以便于找到这些需要的文件。我们称这些输入的文件为项目的依赖。其次，你可能需要构建完成后自动上传到某个地方。我们称这些输出为发布。下面来仔细介绍一下这两部分：

大部分工程都不太可能完全自给自足，一般你都会用到其他工程的文件。比如我工程需要 Hibernate 就得把它的类库加进来，比如测试的时候可能需要某些额外 jar 包，例如 JDBC 驱动或 Ehcache 之类的 Jar 包。

这些文件就是工程的依赖。Gradle 需要你告诉它工程的依赖是什么，它们在哪，然后帮你加入构建中。依赖可能需要去远程库下载，比如 Maven 或者 Ivy 库。也可以是本地库，甚至可能是另一个工程。我们称这个过程叫*依赖解决*。

通常，依赖的自身也有依赖。例如，Hibernate 核心类库就依赖于一些其他的类库。所以，当 Gradle 构建你的工程时，会去找到这些依赖。我们称之为*依赖传递*。

大部分工程构建的主要目的是脱离工程使用。例如，生成 jar 包，包括源代码、文档等，然后发布出去。

这些输出的文件构成了项目的发布内容。Gralde 也会为你分担这些工作。你声明了发布到到哪，Gradle 就会发布到哪。“发布”的意思就是你想做什么。比如，复制到某个目录，上传到 Maven 或 Ivy 仓库。或者在其它项目里使用，这些都可以称之为*发行*。

## 依赖声明

来看一下这个脚本里声明依赖的部分：

### 声明依赖 

build.gradle

```
apply plugin: 'java'
repositories {
    mavenCentral()
}
dependencies {
    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

这是什么意思呢？这段脚本是这么个意思。首先，Hibernate-core.3.6.7.final.jar 这货是编译期必需的依赖。并且这货相关的依赖也会一并被加载进来，该段脚本同时还声明项目测试阶段需要 4.0 版本以上的 Junit。同时告诉 Gradle 可以去 Maven 中央仓库去找这些依赖。下面的章节会进行更详细的描述。

## 依赖配置

Gradle 中依赖以组的形式来划分不同的*配置*。每个配置都只是一组指定的依赖。我们称之为*依赖配置* 。你也可以借由此声明外部依赖。后面我们会了解到，这也可用用来声明项目的发布。

Java 插件定义了许多标准配置项。这些配置项形成了插件本身的 classpath。比如下面罗列的这一些，并且你可以从 [Table 23.5，“Java 插件 - 依赖配置”](http://gradledoc.qiniudn.com/1.12/userguide/java_plugin.html#tab:configurations)了解到更多详细内容.。

### compile

编译范围依赖在所有的 classpath 中可用，同时它们也会被打包

### runtime

runtime 依赖在运行和测试系统的时候需要，但在编译的时候不需要。比如，你可能在编译的时候只需要 JDBC API JAR，而只有在运行的时候才需要 JDBC 驱动实现

### testCompile

测试期编译需要的附加依赖

### testRuntime

测试运行期需要 

不同的插件提供了不同的标准配置，你甚至也可以定义属于自己的配置项。

## 外部依赖

依赖的类型有很多种，其中有一种类型称之为*外部依赖*。这种依赖由外部构建或者在不同的仓库中，例如 Maven 中央仓库或 Ivy 仓库中抑或是本地文件系统的某个目录中。

定义外部依赖需要像下面这样进行依赖配置

### 定义外部依赖

build.gradle

```
dependencies {
    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
}
```

外部依赖包含 group，name 和 version 几个属性。根据选取仓库的不同，group 和 version 也可能是可选的。

当然，也有一种更加简洁的方式来声明外部依赖。采用：将三个属性拼接在一起即可。"group:name:version" 

### 快速定义外部依赖

build.gradle

```
dependencies {
    compile 'org.hibernate:hibernate-core:3.6.7.Final'
}
```

## 仓库

Gradle 是在一个被称之为*仓库*的地方找寻所需的外部依赖。仓库即是一个按 group，name 和 version 规则进行存储的一些文件。Gradle 可以支持不同的仓库存储格式，如 Maven 和 Ivy，并且还提供多种与仓库进行通信的方式，如通过本地文件系统或 HTTP。

默认情况下，Gradle 没有定义任何仓库，你需要在使用外部依赖之前至少定义一个仓库，例如 Maven 中央仓库。

### 使用 Maven 中央仓库

build.gradle

```
repositories {
    mavenCentral()
}
```

或者其它远程 Maven 仓库：

### 使用 Maven 远程仓库

build.gradle

```
repositories {
    maven {
        url "http://repo.mycompany.com/maven2"
    }
}
```

或者采用 Ivy 远程仓库

### 采用 Ivy 远程仓库

build.gradle

```
repositories {
    ivy {
        url "http://repo.mycompany.com/repo"
    }
}
```

或者在指定本地文件系统构建的库。

### 采用本地 Ivy 目录

build.gradle

```
repositories {
    ivy {
        // URL can refer to a local directory
        url "../local-repo"
    }
}
```

一个项目可以采用多个库。Gradle 会按照顺序从各个库里寻找所需的依赖文件，并且一旦找到第一个便停止搜索。

了解更多库相关的信息请参阅章节“仓库”。

## 打包发布

依赖配置也被用于发布文件[[3]](#footname)我们称之为*打包发布*或*发布*。

插件对于打包提供了完美的支持，所以通常而言无需特别告诉 Gradle 需要做什么。但是你需要告诉 Gradle 发布到哪里。这就需要在 uploadArchives 任务中添加一个仓库。下面的例子是如何发布到远程 Ivy 仓库的：

### 发布到 Ivy 仓库

build.gradle

```
uploadArchives {
    repositories {
        ivy {
            credentials {
                username "username"
                password "pw"
            }
            url "http://repo.mycompany.com"
        }
    }
}
```

执行 **gradle uploadArchives**，Gradle 便会构建并上传你的 jar 包，同时会生成一个 ivy.xml 一起上传到目标仓库。

当然你也可以发布到 Maven 仓库中。语法只需稍微一换就可以了。[[4]](#footname)

p.s：发布到 Maven 仓库你需要 Maven 插件的支持，当然，Gradle 也会同时产生 pom.xml 一起上传到目标仓库。

### 发布到 Maven 仓库

build.gradle

```
apply plugin: 'maven'
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "file://localhost/tmp/myRepo/")
        }
    }
}
```

## 下一步目标

若对 DSL 感兴趣，请看 [Project.configurations{}](http://gradledoc.qiniudn.com/1.12/dsl/org.gradle.api.Project.html#org.gradle.api.Project:configurations(groovy.lang.Closure))，[Project.repositories{}](http://gradledoc.qiniudn.com/1.12/dsl/org.gradle.api.Project.html#org.gradle.api.Project:repositories(groovy.lang.Closure)) 和 [Project.dependencies{}](http://gradledoc.qiniudn.com/1.12/dsl/org.gradle.api.Project.html#org.gradle.api.Project:dependencies(groovy.lang.Closure))。

另外，继续顺着手册学习其它章节内容吧。~

<a name="footname">
[3]</a> 这让人感觉有点囧，我们正在尝试逐步分离两种概念。

<a name="footname">
[4]</a> 我们也在努力改进语法让发布到 Maven 仓库不那么费劲。