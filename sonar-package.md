# Sonar 插件  
  
你可能会想使用新的 Sonar Runner 插件来代替现在这个插件。尤其是因为只有 Sonar Runner 插件支持 Sonar 3.4 及更高的版本。  

Sonar 插件提供了对 Sonar，一个基于 web 的代码质量监测平台的集成。该插件添加了sonarAnalyze task ，用来分析一个 project 及子 project 都应用了哪个插件。分析结果存储于 Sonar 数据库中。该插件基于 Sonar Runner，并要求是 Sonar 2.11 或更高的版本。

SonarAnalyze task 是一项需要显式执行的独立任务，不依赖于任何其他 task。除了源代码之外，该 task 还分析了类文件和测试结果文件（如果有）。为获得最佳结果，建议在分析前运行一次完整的构建。在典型的设置中，会每天在构建服务器上运行一次分析。

## 用法  

最低要求是必须配置 Sonar 插件应用于该 project。

**示例 配置使用 Sonar 插件**

build.gradle  
  
```
apply plugin: "sonar"  
```  

除非 Sonar 是在本地上运行，并且有默认的配置，否则有必要配置 Sonar 服务器及数据库的连接设置。

**示例 配置 Sonar 连接设置**

build.gradle  
  
```
sonar
    server {
        url = "http://my.server.com"
    }
    database {
        url = "jdbc:mysql://my.server.com/sonar"
        driverClassName = "com.mysql.jdbc.Driver"
        username = "Fred Flintstone"
        password = "very clever"
    }
}  
```  

或者，可以从命令行设置某些或全部的连接设置 （见第 35.6 节，“从命令行配置 Sonar 设置”）。

Project 设置会决定这个项目将如何进行分析。默认配置非常适合于分析标准 Java 项目，并可以在许多方面进行自定义。

**示例 配置 Sonar project 设置**

build.gradle  
  
```
sonar
    project
        coberturaReportPath = file("$buildDir/cobertura.xml")
    }
}  
```  

在上面的例子中，sonar，server，database 和 project 块分别配置的是SonarRootModel， SonarServer， SonarDatabase 及 SonarProject 类型的对象。可以查阅它们的 API 文档以了解更多信息。

## 分析多项目构建  

Sonar 插件能够一次分析整个项目的层次结构。它能够在 Sonar 的 web 界面生成一个层次图，该层次图包含了综合的指标且能够深入到子项目中。同时，它比单独分析每个项目更快。

要分析项目的层次结构， 需要把 Sonar 插件应用于层次结构的最顶层项目。通常（但不是一定）会是根项目。在该 project 中的 sonar 块配置的是一个 SonarRootModel 类型的对象。它拥有所有全局配置，最重要的服务器和数据库的连接设置。

**示例 在多项目构建中的全局配置**

build.gradle  
  
```
apply plugin: "sonar"
sonar {
    server {
        url = "http://my.server.com"
    }
    database {
        url = "jdbc:mysql://my.server.com/sonar"
        driverClassName = "com.mysql.jdbc.Driver"
        username = "Fred Flintstone"
        password = "very clever"
    }
}   
```  

层次结构中的每个项目都有其自身的项目配置。共同的值可以在父构建脚本中进行设置。

**示例 多项目构建中的共同项目配置**

build.gradle  
   
```
subprojects {
    sonar
        project
            sourceEncoding = "UTF-8"
        }
    }
}  
```  

在子项目中的 sonar 块配置的是一个 SonarProjectModel 类型的对象。

这些 Projects 也可以单独配置。例如，设置 skip 属性为 true 以防止一个项目（和它的子项目）被分析。跳过的项目将不会显示在 Sonar 的 web 界面中。

**示例 多项目构建中的单独项目配置**

build.gradle  
  
```
project
    sonar
        project
            skip = true
        }
    }
}  
```  

另一种典型的各个项目配置是配置要分析的编程语言。注意，Sonar 只能分析每个项目的一种语言。

**示例 配置语言分析**

build.gradle  
  
```
project
    sonar
        project
            language = "groovy"
        }
    }
}  
```  

当一次只设置一个属性时，等效属性的语法更加简洁：

**示例 使用属性语法**

build.gradle  
  
```
project(":project2").sonar.project.language = "groovy"  
```  

## 分析自定义的 Source Sets  

默认情况下，Sonar 插件将分析main source set 里的生产源文件，以及 test source sets 里的测试源文件。它的分析独立于项目的源目录布局。根据需要，可以添加额外的 source sets。

**示例 分析自定义的Source Sets**

build.gradle  
  
```
sonar.project {
    sourceDirs += sourceSets.custom.allSource.srcDirs
    testDirs += sourceSets.integTest.allSource.srcDirs
}  
```  

## 分析非 Java 语言  

要分析非 Java 语言编写的代码，请安装相应的 Sonar 插件，并相应地设置sonar.project.language ：

**示例 分析非 Java 语言**

build.gradle  
  
```
sonar.project {
    language = "grvy" // set language to Groovy
}  
```  

截至 Sonar 3.4，每个项目只可以分析一种语言。不过，在多项目构建中你可以为不同的项目设置不同的语言。

## 设置自定义的 Sonar 属性  

最终，大多数配置都会以被称为 Sonar 属性的键-值对的形式传递给 Sonar 的代码分析器。在 API 文档中的 SonarProperty 注解显示了插件的对象模型的属性是如何映射到相应的 Sonar 属性中的。Sonar 插件提供了 hooks，用于 Sonar 属性传给代码分析器前的后置处理。相同的hook 可以用来添加额外的属性，并且不会被插件的对象模型所覆盖。

对于全局的 Sonar 属性，可以使用 SonarRootModel 上的 withGlobalProperties hook：

**示例 设置自定义的全局属性**

build.gradle  
  
```
sonar.withGlobalProperties { props ->
    props["some.global.property"] = "some value"
    // non-String values are automatically converted to Strings
    props["other.global.property"] = ["foo", "bar", "baz"]
}  
```  

对于每个项目的 Sonar 属性，使用 SonarProject 上的 withProjectProperties hook：

**示例 设置自定义的项目属性**

build.gradle  
  
```
sonar.project.withProjectProperties { props ->
    props["some.project.property"] = "some value"
    // non-String values are automatically converted to Strings
    props["other.global.property"] = ["foo", "bar", "baz"]
}  
```  

Sonar 的可用属性的列表可以在 Sonar 文档中找到。注意，对于大多数的这些属性，Sonar 插件的对象模型具有等效的属性，且没有必要使用 withGlobalProperties 或withProjectProperties 的 hook。对于第三方 Sonar 插件的配置，请参阅插件的文档。

## 从命令行配置 Sonar 的设置  

下面的属性或者可以从命令行中或者是作为 sonarAnalyze 任务的任务参数这两种方式之一来设置。任务参数将覆盖任何在构建脚本中设置的相应值。

- server.url
- database.url
- database.driverClassName
- database.username
- database.password
- showSql
- showSqlResults
- verbose
- forceAnalysis  

下面是一个完整的例子：
  
```
gradle sonarAnalyze --server.url=http://sonar.mycompany.com --database.password=myPassword --verbose  
```  

如果你需要从命令行设置其他属性，你可以使用系统属性来做：

**示例 实现自定义命令行属性**

build.gradle  
  
```
sonar.project {
    language = System.getProperty("sonar.language", "java")
}  
```  

然而，请记住，通常最好是配置在构建脚本中，并在代码控制下。

## 任务  

Sonar 插件向 project 中添加了以下任务。

表 声纳插件 - 任务  
  
<table id="N13A1A" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
任务名称</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
依赖于</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
类型</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
描述</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">sonarAnalyze</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
-</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
<a target="_blank" target="_blank" class="ulink" href="../dsl/org.gradle.api.plugins.sonar.SonarAnalyze.html" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">sonarAnalyze</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
分析项目层次结构，并将结果存储在 Sonar 数据库。</td>
</tr>
</tbody>
</table>