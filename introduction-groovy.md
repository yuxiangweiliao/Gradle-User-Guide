# Groovy 快速入门  
  
要构建一个 Groovy 项目，你需要使用 Groovy 插件。该插件扩展了 Java 插件，对你的项目增加了 Groovy 的编译功能. 你的项目可以包含 Groovy 源码，Java 源码，或者两者都包含。在其他各方面，Groovy 项目与我们在第七章 Java 快速入门中所看到的Java 项目几乎相同。  

## 一个基本的 Groovy 项目  

让我们来看一个例子。要使用 Groovy 插件，你需要在构建脚本文件当中添加以下内容：  

例子 Groovy plugin

build.gradle  

```
apply plugin: 'groovy'   
```  

注意： 此例子的代码可以在 Gradle 的二进制文件或源码中的 `samples/groovy/quickstart` 里看到。  

这段代码同时会将 Java 插件应用到 project 中，如果 Java 插件还没被应用的话。Groovy 插件继承了 compile 任务 ，在 `src/main/groovy` 目录中查找源文件；且继承了 compileTest 任务，在 `src/test/groovy` 目录中查找测试的源文件。这些编译任务对这些目录使用了联合编译，这意味着它们可以同时包含 java 和 groovy 源文件。  

要使用 groovy 编译任务，还必须声明要使用的 Groovy 版本以及从哪里获取 Groovy 库。你可以通过在 groovy 配置中添加依赖来完成。compile 配置继承了这个依赖,从而在编译 Groovy和 Java 源代码时，groovy 库也会被包含在类路径中。下面例子中，我们会使用 Maven 中央仓库中的 Groovy 2.2.0 版本。  

例子 Dependency on Groovy 2.2.0

build.gradle  
  
```
repositories {
    mavenCentral()
}
dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.2.0'
}  
```  

这里是我们写好的构建文件：

例子 Groovy example - complete build file

build.gradle  
  
```
apply plugin: 'eclipse'
apply plugin: 'groovy'
repositories {
    mavenCentral()
}
dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.2.0'
    testCompile 'junit:junit:4.11'
}  
```  

运行 gradle build 将会对你的项目进行编译，测试和打成 jar 包。

## 总结  

这一章描述了一个很简单的 Groovy 项目。通常情况下，一个真实的项目所需要的不止于此。因为一个 Groovy 项目也 是一个 Java 项目， 由于 Groovy 工程也是一个 Java 工程，因此你能用 Java 做的事情 Groovy 也能做。  

你可以参阅 [Groovy 插件](package.md) 去了解更多关于 Groovy 插件的内容，或在 Gradle 发行包的 samples/groovy 目录中找到更多的 Groovy 项目示例。