# 从 Gradle 中调用 Ant  
  
Gradle 提供了对 Ant 的优秀集成您可以在你的 Gradle 构建中，使用单独的 Ant 任务或整个 Ant 构建。事实上，你会发现在 Gradle 中使用 Ant 任务比使用 Ant 的 XML 格式更容易也更强大。你甚至可以只把 Gradle 当作一个强大的 Ant 任务脚本的工具。

Ant 可以分为两层。第一层是 Ant 的语言。它提供了用于 build.xml，处理的目标，特殊的构造方法比如宏，还有其他等等的语法。换句话说，除了 Ant 任务和类型之外全部都有。Gradle 理解这种语言，并允许您直接导入你的 Ant build.xml 到 Gradle 项目中。然后你可以使用你的 Ant 构建中的 target，就好像它们是 Gradle 任务一样。

Ant 的第二层是其丰富的 Ant 任务和类型，如 javac、copy 或 jar。这一层 Gradle 单靠 Groovy 和不可思议的 AntBuilder，对其提供了集成。

最后，由于构建脚本是 Groovy 脚本，所以您始终可以作为一个外部进程来执行 Ant 构建。你的构建脚本可能包含有类似这样的语句："ant clean compile".execute()。[8]

你可以把 Gradle 的 Ant 集成当成一个路径，将你的构建从 Ant 迁移至 Gradle 。例如，你可以通过导入您现有的 Ant 构建来开始。然后，可以将您的依赖声明从 Ant 脚本移到您的构建文件。最后，您可以将整个任务移动到您的构建文件，或者把它们替换为一些 Gradle 插件。这个过程可以随着时间一点点完成，并且在这整个过程当中你的 Gradle 构建都可以使用用。

## 在构建中使用 Ant 任务和类型  

在构建脚本中，Gradle 提供了一个名为 ant 的属性。它指向一个 AntBuilder 实例。AntBuilder 用于从你的构建脚本中访问 Ant 任务、 类型和属性。从 Ant 的 build.xml 格式到 Groovy 之间有一个非常简单的映射，下面解释。

通过调用 AntBuilder实例上的一个方法，可以执行一个 Ant 任务。你可以把任务名称当作方法名称使用。例如，你可以通过调用ant.echo()方法执行 Ant 的 echo 任务。Ant 任务的属性会作为 Map 参数传给该方法。下面是执行 echo 任务的例子。请注意我们还可以混合使用 Groovy 代码和 Ant 任务标记。这将会非常强大。


**示例 17.1. 使用 Ant 任务**

build.gradle   
  
```
task hello << {
    String greeting = 'hello from Ant'
    ant.echo(message: greeting)
}  
```  

gradle hello 的输出结果  

> gradle hello
:hello
[ant:echo] hello from Ant
BUILD SUCCESSFUL
Total time: 1 secs  

你可以把一个嵌套文本，通过作为任务方法调用的参数，把它传给一个 Ant 任务。在此示例中，我们将把作为嵌套文本的消息传给 echo 任务：

**示例 17.2. 向 Ant 任务传入嵌套文本**

build.gradle  
  
```
task hello << {
    ant.echo('hello from Ant')
}  
```  

gradle hello 的输出结果  

> gradle hello
:hello
[ant:echo] hello from Ant
BUILD SUCCESSFUL
Total time: 1 secs  

你可以在一个闭包里把嵌套的元素传给一个 Ant 任务。嵌套元素的定义方式与任务相同，通过调用与我们要定义的元素一样的名字的方法。

**示例 17.3. 向 Ant 任务传入嵌套元素**

build.gradle   
  
```
task zip << {
    ant.zip(destfile: 'archive.zip') {
        fileset(dir: 'src') {
            include(name: '**.xml')
            exclude(name: '**.java')
        }
    }
}    
```   

您可以用访问任务同样的方法，把类型名字作为方法名称，访问 Ant 类型。方法调用返回 Ant 数据类型，然后可以在构建脚本中直接使用。在以下示例中，我们创建一个 Ant 的 path 对象，然后循环访问它的内容。

**示例 17.4. 使用 Ant 类型**

build.gradle  
  
```  
task list << {
    def path = ant.path {
        fileset(dir: 'libs', includes: '*.jar')
    }
    path.list().each {
        println it
    }
}  
```  

有关 AntBuilder 的详细信息可以参阅 《Groovy in Action》的8.4章节， 或 Groovy 维基。

### 在您的构建中使用自定义 Ant 任务

要使自定义任务在您的构建中可用，你可以使用Ant 任务 taskdef（通常更容易） 或typedef，就像在 build.xml 文件中一样。然后，您可以像引用内置的 Ant 任务一样引用自定义 Ant 任务。

**示例 17.5. 使用自定义 Ant 任务**

build.gradle  
  
```
task check << {
    ant.taskdef(resource: 'checkstyletask.properties') {
        classpath {
            fileset(dir: 'libs', includes: '*.jar')
        }
    }
    ant.checkstyle(config: 'checkstyle.xml') {
        fileset(dir: 'src')
    }
}  
```  

你可以使用 Gradle 的依赖管理组合类路径，以用于自定义任务。要做到这一点，你需要定义一个自定义配置的类路径中，然后将一些依赖项添加到配置中。这在 50.4章节，“如何声明你的依赖关系”有更详细的描述。

**示例 17.6. 声明用于自定义 Ant 任务的类路径**

build.gradle  
  
```
configurations {
    pmd
}
dependencies {
    pmd group: 'pmd', name: 'pmd', version: '4.2.5'
}  
```  

若要使用类路径配置，请使用自定义配置里的 asPath 属性。

**示例 17.7. 同时使用自定义 Ant 任务和依赖管理**

build.gradle  
  
```
task check << {
    ant.taskdef(name: 'pmd', classname: 'net.sourceforge.pmd.ant.PMDTask', classpath: configurations.pmd.asPath)
    ant.pmd(shortFilenames: 'true', failonruleviolation: 'true', rulesetfiles: file('pmd-rules.xml').toURI().toString()) {
        formatter(type: 'text', toConsole: 'true')
        fileset(dir: 'src')
    }
}  
```  

## 导入 Ant 构建  

你可以使用 ant.importBuild()方法来向 Gradle 项目导入一个 Ant 构建。当您导入一个 Ant 构建时，每个 Ant 目标被视为一个 Gradle 任务。这意味着你可以用与 Gradle 任务完全相机的方式操纵和执行 Ant 目标。

**示例 17.8. 导入 Ant 构建**

build.gradle  
  
```
ant.importBuild 'build.xml'
build.xml
<project>
    <target name="hello">
        <echo>Hello, from Ant</echo>
    </target>
</project>  
```  

gradle hello 的输出结果  

> gradle hello
:hello
[ant:echo] Hello, from Ant
BUILD SUCCESSFUL
Total time: 1 secs  

您可以添加一个依赖于 Ant 目标的任务：

**示例 17.9. 依赖于 Ant 目标的任务**

build.gradle   
  
```
ant.importBuild 'build.xml'
task intro(dependsOn: hello) << {
    println 'Hello, from Gradle'
}  
```  

gradle intro的输出结果  

> gradle intro
:hello
[ant:echo] Hello, from Ant
:intro
Hello, from Gradle
BUILD SUCCESSFUL
Total time: 1 secs  

或者，您可以将行为添加到 Ant 目标中：  

**示例 17.10. 将行为添加到 Ant 目标**

build.gradle  
  
```
ant.importBuild 'build.xml'
hello << {
    println 'Hello, from Gradle'
}  
```  

gradle hello 的输出结果  

> gradle hello
:hello
[ant:echo] Hello, from Ant
Hello, from Gradle
BUILD SUCCESSFUL
Total time: 1 secs  

它也可以用于一个依赖于 Gradle 任务的 Ant 目标：

**示例 17.11. 依赖于 Gradle 任务的 Ant 目标**

build.gradle  
  
```
ant.importBuild 'build.xml'
task intro << {
    println 'Hello, from Gradle'
}
build.xml
<project>
    <target name="hello" depends="intro">
        <echo>Hello, from Ant</echo>
    </target>
</project>  
```  

gradle hello 的输出结果  

> gradle hello
:intro
Hello, from Gradle
:hello
[ant:echo] Hello, from Ant
BUILD SUCCESSFUL
Total time: 1 secs  

## Ant 属性和引用  

有几种方法来设置 Ant 属性，以便使该属性被 Ant 任务使用。你可以直接在 AntBuilder 实例上设置属性。Ant 属性也可以从一个你可以修改的 Map 中获得。您还可以使用 Ant property 任务。下面是一些如何做到这一点的例子。

**示例 17.12. Ant 属性设置**

build.gradle  
  
```
ant.buildDir = buildDir
ant.properties.buildDir = buildDir
ant.properties['buildDir'] = buildDir
ant.property(name: 'buildDir', location: buildDir)
build.xml
<echo>buildDir = ${buildDir}</echo>  
```  

许多 Ant 任务在执行时会设置一些属性。有几种方法来获取这些属性值。你可以直接从AntBuilder 实例获得属性。Ant 属性也可作为一个 Map。下面是一些例子。

**示例 17.13. 获取 Ant 属性**

build.xml  
  
```
<property name="antProp" value="a property defined in an Ant build"/>
build.gradle
println ant.antProp
println ant.properties.antProp
println ant.properties['antProp']  
```  

有几种方法可以设置 Ant 引用：

**示例 17.14. Ant 引用设置**

build.gradle  
  
```
ant.path(id: 'classpath', location: 'libs')
ant.references.classpath = ant.path(location: 'libs')
ant.references['classpath'] = ant.path(location: 'libs')
build.xml
<path refid="classpath"/>  
```  

有几种方法可以获取 Ant 引用：

**示例 17.15. 获取 Ant 引用**

build.xml  
  
```
<path id="antPath" location="libs"/>
build.gradle
println ant.references.antPath
println ant.references['antPath']  
```