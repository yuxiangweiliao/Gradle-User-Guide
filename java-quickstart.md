# Java 构建入门

## Java 插件

如你所见，Gradle 是一个通用工具。它可以通过脚本构建任何你想要实现的东西，真正实现开箱即用。但前提是你需要在脚本中编写好代码才行。

大部分 Java 项目基本流程都是相似的：编译源文件，进行单元测试，创建 jar 包。使用 Gradle 做这些工作不必为每个工程都编写代码。Gradle 已经提供了完美的插件来解决这些问题。插件就是 Gradle 的扩展，简而言之就是为你添加一些非常有用的默认配置。Gradle 自带了很多插件，并且你也可以很容易的编写和分享自己的插件。Java plugin 作为其中之一，为你提供了诸如编译，测试，打包等一些功能。

Java 插件为工程定义了许多默认值，如Java源文件位置。如果你遵循这些默认规则，那么你无需在你的脚本文件中书写太多代码。当然，Gradle 也允许你自定义项目中的一些规则，实际上，由于对 Java 工程的构建是基于插件的，那么你也可以完全不用插件自己编写代码来进行构建。

后面的章节我们通过许多深入的例子介绍了如何使用 Java 插件来进行以来管理和多项目构建等。但在这个章节我们需要先了解 Java 插件的基本用法。

## 一个基本 Java 项目

来看一下下面这个小例子，想用 Java 插件，只需增加如下代码到你的脚本里。

### 采用 Java 插件

```
build.gradle
```

```
apply plugin: 'java'
```
					
备注:示例代码可以在 Gralde 发行包中的 samples/java/quickstart 下找到。

定义一个 Java 项目只需如此而已。这将会为你添加 Java 插件及其一些内置任务。

> 添加了哪些任务?
> 
> 你可以运行 gradle tasks 列出任务列表。这样便可以看到 Java 插件为你添加了哪些任务。

标准目录结构如下:

```
project  
	+build  
    +src/main/java  
    +src/main/resources  
    +src/test/java  
    +src/test/resources  
```

Gradle 默认会从 `src/main/java` 搜寻打包源码，在 `src/test/java` 下搜寻测试源码。并且 `src/main/resources` 下的所有文件按都会被打包，所有 `src/test/resources` 下的文件 都会被添加到类路径用以执行测试。所有文件都输出到 build 下，打包的文件输出到 build/libs 下。

### 构建项目

Java 插件为你添加了众多任务。但是它们只是在你需要构建项目的时候才能发挥作用。最常用的就是 build 任务,这会构建整个项目。当你执行 gradle build 时，Gralde 会编译并执行单元测试，并且将 `src/main/*` 下面 class 和资源文件打包。

### 构建 Java 项目

运行 gradle build 的输出结果

```
Output of gradle build
> gradle build
:compileJava
:processResources
:classes
:jar
:assemble
:compileTestJava
:processTestResources
:testClasses
:test
:check
:build
BUILD SUCCESSFUL
Total time: 1 secs
```

其余一些较常用的任务有如下几个:

#### clean

删除 build 目录以及所有构建完成的文件。

#### assemble

编译并打包 jar 文件，但不会执行单元测试。一些其他插件可能会增强这个任务的功能。例如，如果采用了 War 插件，这个任务便会为你的项目打出 War 包。

#### check

编译并测试代码。一些其他插件也可能会增强这个任务的功能。例如，如果采用了 Code-quality 插件，这个任务会额外执行 Checkstyle。

### 外部依赖

通常，一个 Java 项目拥有许多外部依赖。你需要告诉 Gradle 如何找到并引用这些外部文件。在 Gradle 中通常 Jar 包都存在于仓库中。仓库可以用来搜寻依赖或发布项目产物。下面是一个采用 Maven 仓库的例子。

### 添加 Maven 仓库

```
build.gradle
```

```
repositories {
    mavenCentral()
}
```

添加依赖。这里声明了编译期所需依赖 commons-collections 和测试期所需依赖 junit。

### 添加依赖

```
build.gradle
```

```
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

了解更多可参阅[依赖管理基础](dependency-management-basics.md)

### 自定义项目

Java 插件为你的项目添加了众多默认配置。这些默认值通常对于一个普通项目来说已经足够了。但如果你觉得不适用修改起来也很简单。看下面的例子，我们为 Java 项目指定了版本号以及所用的 JDK 版本，并且添加一些属性到 mainfest 中。

### 自定义 MANIFEST.MF

```
build.gradle
```

```
sourceCompatibility = 1.5
version = '1.0'
jar {
    manifest {
        attributes 'Implementation-Title': 'Gradle Quickstart', 'Implementation-Version': version
    }
}
```

> 都有哪些可用属性?
> 
> 可以执行 gradle propertie s来得到项目的属性列表。用这条命令可以看到插件添加的属性以及默认值。

Java 插件添加的都是一些普通任务，如同他们写在 Build 文件中一样。这意味着前面章节展示的机制都可以用来修改这些任务的行为。例如，可以设置任务的属性，添加任务行为，更改任务依赖，甚至是重写覆盖整个任务。在下面的例子中，我们将修改 test 任务，这是一个 Test 类型任务。让我们来在它执行时为它添加一些系统属性。

###  为 test 添加系统属性

```
build.gradle
```

```
test {
    systemProperties 'property': 'value'
}
```

### 发布 jar 包

如何发布 jar 包?你需要告诉 Gradle 发布到到哪。在 Gradle 中 jar 包通常被发布到某个仓库中。在下面的例子中，我们会将 jar 包发布到本地目录。当然你也可以发布到远程仓库或多个远程仓库中。

### 发布 jar 包

```
build.gradle
```

```
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
```

执行 gradle uploadArchives 以发布 jar 包。


### 创建 Eclipse 文件

若要把项目导入 Eclipse 中，你需要添加另外一个插件到你的脚本文件中。

### Eclipse plugin

```
build.gradle
```

```
apply plugin: 'eclipse'
```
						
执行 gradle eclipse 来生成 Eclipse 项目文件。

### 示例汇总

这是示例代码汇总得到的一个完整脚本：

### Java 示例 - 一个完整构建脚本

```
build.gradle
```
```
apply plugin: 'java'
apply plugin: 'eclipse'
sourceCompatibility = 1.5
version = '1.0'
jar {
    manifest {
        attributes 'Implementation-Title': 'Gradle Quickstart', 'Implementation-Version': version
    }
}
repositories {
    mavenCentral()
}
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
test {
    systemProperties 'property': 'value'
}
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
```

## 多项目构建

现在来看一个典型的多项目构建的例子。项目结构如下：

### 多项目构建-项目结构

```
Build layout
```

```
multiproject/
  api/
  services/webservice/
  shared/
```

`备注: 本示例代码可在 Gradle 发行包的 samples/java/multiproject 位置找到`

此处有三个工程。api 工程用来生成给客户端用的 jar 文件，这个 jar 文件可以为 XML webservice 提供 Java 客户端。webservice 是一个 web 应用，生成 XML。shared 工程包含的是前述两个工程共用的代码。

### 多项目构建定义

定义一个多项目构建工程需要在根目录(本例中与 multiproject 同级)创建一个*setting* 配置文件来指明构建包含哪些项目。并且这个文件必需叫 settings.gradle 本例的配置文件如下:

### 多项目构建中的 settings.gradle 

```
settings.gradle
```

```
include "shared", "api", "services:webservice", "services:shared"
```
			
### 公共配置

对多项目构建而言，总有一些共同的配置.在本例中，我们会在根项目上采用配置注入的方式定义一些公共配置。根项目就像一个容器，子项目会迭代访问它的配置并注入到自己的配置中。这样我们就可以简单的为所有工程定义主配置单了：

### 多项目构建-公共配置

```
build.gradle
```

```
subprojects {
    apply plugin: 'java'
    apply plugin: 'eclipse-wtp'
    repositories {
       mavenCentral()
    }
    dependencies {
        testCompile 'junit:junit:4.11'
    }
    version = '1.0'
    jar {
        manifest.attributes provider: 'gradle'
    }
}
```

值得注意的是我们为每个子项目都应用了 Java 插件。这意味着我们在前面章节学习的内容在子项目中也都是可用的。所以你可以在根项目目录进行编译，测试，打包等所有操作。

### 工程依赖

同一个构建中可以建立工程依赖，一个工程的 jar 包可以提供给另外一个工程使用。例如我们可以让 api 工程以依赖于 shared 工程的 jar 包。这样 Gradle 在构建 api 之前总是会先构建 shared 工程。

### 多项目构建-工程依赖

```
api/build.gradle
```

```
dependencies {
    compile project(':shared')
}
```

### 打包发布

如何发布，请看下文:

### 多项目构建-发布

```
api/build.gradle
```

```
task dist(type: Zip) {
    dependsOn spiJar
    from 'src/dist'
    into('libs') {
        from spiJar.archivePath
        from configurations.runtime
    }
}
artifacts {
   archives dist
}
```

## 下一步目标?

本章中，我们了解了如何构建一个基本 Java 工程。但这都是一小部分基础，用 Gradle 还可以做很多事。关于 了解更多可参阅 [Java 插件](java-package.md)，The Java Plugin，并且你可以在 Gradle 发行包中的 samples/java 目录找到更多例子。

另外，不要忘了继续阅读[依赖管理基础内容](dependency-management-basics.md)