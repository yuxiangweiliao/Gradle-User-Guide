# 构建基础

## Projects 和 tasks

projects 和 tasks是 Gradle 中最重要的两个概念。

任何一个 Gradle 构建都是由一个或多个 projects 组成。每个 project 包括许多可构建组成部分。 这完全取决于你要构建些什么。举个例子，每个 project 或许是一个 jar 包或者一个 web 应用，它也可以是一个由许多其他项目中产生的 jar 构成的 zip 压缩包。一个 project 不必描述它只能进行构建操作。它也可以部署你的应用或搭建你的环境。不要担心它像听上去的那样庞大。 Gradle 的 build-by-convention 可以让您来具体定义一个 project 到底该做什么。

每个 project 都由多个 tasks 组成。每个 task 都代表了构建执行过程中的一个原子性操作。如编译，打包，生成 javadoc，发布到某个仓库等操作。

到目前为止，可以发现我们可以在一个 project 中定义一些简单任务，后续章节将会阐述多项目构建和多项目多任务的内容。

## Hello world

你可以通过在命令行运行 gradle 命令来执行构建，gradle 命令会从当前目录下寻找 build.gradle  文件来执行构建。我们称 build.gradle 文件为构建脚本。严格来说这其实是一个构建配置脚本，后面你会了解到这个构建脚本定义了一个 project 和一些默认的 task。

你可以创建如下脚本到 build.gradle 中 To try this out，create the following build script named build.gradle。

### 第一个构建脚本

build.gradle

```
task hello {
    doLast {
        println 'Hello world!'
    }
}
```

然后在该文件所在目录执行 gradle -q hello

> -q 参数的作用是什么?
> 
> 该文档的示例中很多地方在调用 gradle 命令时都加了 -q 参数。该参数用来控制 gradle 的日志级别，可以保证只输出我们需要的内容。具体可参阅本文档第十八章日志来了解更多参数和信息。

### 执行脚本

```
Output of gradle -q hello
> gradle -q hello
Hello world!
```

上面的脚本定义了一个叫做 hello 的 task，并且给它添加了一个动作。当执行 gradle hello 的时候, Gralde 便会去调用 hello 这个任务来执行给定操作。这些操作其实就是一个用 groovy 书写的闭包。

如果你觉得它看上去跟 Ant 中的 targets 很像，那么是对的。Gradle 的 tasks 就相当于 Ant 中的 targets。不过你会发现他功能更加强大。我们只是换了一个比 target 更形象的另外一个术语。不幸的是这恰巧与 Ant 中的术语有些冲突。ant 命令中有诸如 javac、copy、tasks。所以当该文档中提及 tasks 时，除非特别指明 ant task。否则指的均是指 Gradle 中的 tasks。

## 快速定义任务

用一种更简洁的方式来定义上面的 hello 任务。

### 快速定义任务

build.gradle

```
task hello << {
    println 'Hello world!'
}
```

上面的脚本又一次采用闭包的方式来定义了一个叫做 hello 的任务，本文档后续章节中我们将会更多的采用这种风格来定义任务。

### 代码即脚本

Gradle 脚本采用 Groovy 书写，作为开胃菜,看下下面这个例子。 

### 在 gradle 任务中采用 groovy

build.gradle

```
task upper << {
    String someString = 'mY_nAmE'
    println "Original: " + someString
    println "Upper case: " + someString.toUpperCase()
}
Output of gradle -q upper
> gradle -q upper
Original: mY_nAmE
Upper case: MY_NAME
```

或者

### 在 gradle 任务中采用 groovy

build.gradle

```
task count << {
    4.times { print "$it " }
}
Output of gradle -q count
> gradle -q count
0 1 2 3
```

## 任务依赖

你可以按如下方式创建任务间的依赖关系

### 在两个任务之间指明依赖关系

build.gradle

```
task hello << {
    println 'Hello world!'
}
task intro(dependsOn: hello) << {
    println "I'm Gradle"
}
```

gradle -q intro 的输出结果

```
\> gradle -q intro
Hello world!
I'm Gradle
```

添加依赖 task 也可以不必首先声明被依赖的 task。

## 延迟依赖 

build.gradle

```
task taskX(dependsOn: 'taskY') << {
    println 'taskX'
}
task taskY << {
    println 'taskY'
}
```

Output of gradle -q taskX

```
 \> gradle -q taskX
taskY
taskX
```

可以看到，taskX 是 在 taskY 之前定义的，这在多项目构建中非常有用。

注意:当引用的任务尚未定义的时候不可使用短标记法来运行任务。

## 动态任务

借助 Groovy 的强大不仅可以定义简单任务还能做更多的事。例如，可以动态定义任务。

### 创建动态任务

build.gradle

```
4.times { counter ->
    task "task$counter" << {
        println "I'm task number $counter"
    }
}
```

gradle -q task1 的输出结果。

```
Output of gradle -q task1
\> gradle -q task1
I'm task number 1
```

## 任务操纵

一旦任务被创建后，任务之间可以通过 API 进行相互访问。这也是与 Ant 的不同之处。比如可以增加一些依赖。

### 通过 API 进行任务之间的通信 - 增加依赖

build.gradle

```
4.times { counter ->
    task "task$counter" << {
        println "I'm task number $counter"
    }
}
task0.dependsOn task2, task3
```

gradle -q task0的输出结果。

```
Output of gradle -q task0
\> gradle -q task0
I'm task number 2
I'm task number 3
I'm task number 0
```

为已存在的任务增加行为。

### 通过 API 进行任务之间的通信 - 增加任务行为

build.gradle

```
task hello << {
    println 'Hello Earth'
}
hello.doFirst {
    println 'Hello Venus'
}
hello.doLast {
    println 'Hello Mars'
}
hello << {
    println 'Hello Jupiter'
}
Output of gradle -q hello
> gradle -q hello
Hello Venus
Hello Earth
Hello Mars
Hello Jupiter
```

doFirst 和 doLast 可以进行多次调用。他们分别被添加在任务的开头和结尾。当任务开始执行时这些动作会按照既定顺序进行。其中 << 操作符 是 doLast 的简写方式。


## 短标记法

你早就注意到了吧，没错，每个任务都是一个脚本的属性，你可以访问它:

### 以属性的方式访问任务

build.gradle

```
task hello << {
    println 'Hello world!'
}
hello.doLast {
    println "Greetings from the $hello.name task."
}
```

gradle -q hello 的输出结果

```
Output of gradle -q hello
\> gradle -q hello
Hello world!
Greetings from the hello task.
```

对于插件提供的内置任务。这尤其方便(例如:complie)

## 增加自定义属性

你可以为一个任务添加额外的属性。例如,新增一个叫做 myProperty 的属性，用 ext.myProperty 的方式给他一个初始值。这样便增加了一个自定义属性。

### 为任务增加自定义属性

build.gradle

```
task myTask {
    ext.myProperty = "myValue"
}

task printTaskProperties << {
    println myTask.myProperty
}
```

gradle -q printTaskProperties 的输出结果

```
Output of gradle -q printTaskProperties
\> gradle -q printTaskProperties
myValue
```

自定义属性不仅仅局限于任务上，还可以做更多事情。

## 调用 Ant 任务

Ant 任务是 Gradle 中的一等公民。Gradle 借助 Groovy 对 Ant 任务进行了优秀的整合。Gradle 自带了一个 AntBuilder，在 Gradle 中调用 Ant 任务比在 build.xml 中调用更加的方便和强大。 通过下面的例子你可以学到如何调用一个 Ant 任务以及如何与 Ant 中的属性进行通信。

### 利用 AntBuilder 执行 ant.loadfile 

build.gradle

```
task loadfile << {
    def files = file('../antLoadfileResources').listFiles().sort()
    files.each { File file ->
        if (file.isFile()) {
            ant.loadfile(srcFile: file, property: file.name)
            println " *** $file.name ***"
            println "${ant.properties[file.name]}"
        }
    }
}
```

gradle -q loadfile 的输出结果 

```
Output of gradle -q loadfile
\> gradle -q loadfile
*** agile.manifesto.txt ***
Individuals and interactions over processes and tools
Working software over comprehensive documentation
Customer collaboration  over contract negotiation
Responding to change over following a plan
 *** gradle.manifesto.txt ***
Make the impossible possible, make the possible easy and make the easy elegant.
(inspired by Moshe Feldenkrais)
```

在你脚本里还可以利用 Ant 做更多的事情。想了解更多请参阅[在 Gradle 中调用 Ant](invoke-ant-seventeen.md)。

## 方法抽取

Gradle 的强大要看你如何编写脚本逻辑。针对上面的例子，首先要做的就是要抽取方法。

### 利用方法组织脚本逻辑

build.gradle

```
task checksum << {
    fileList('../antLoadfileResources').each {File file ->
        ant.checksum(file: file, property: "cs_$file.name")
        println "$file.name Checksum: ${ant.properties["cs_$file.name"]}"
    }
}
task loadfile << {
    fileList('../antLoadfileResources').each {File file ->
        ant.loadfile(srcFile: file, property: file.name)
        println "I'm fond of $file.name"
    }
}
File[] fileList(String dir) {
    file(dir).listFiles({file -> file.isFile() } as FileFilter).sort()
}
```

gradle -q loadfile 的输出结果

```
Output of gradle -q loadfile
\> gradle -q loadfile
I'm fond of agile.manifesto.txt
I'm fond of gradle.manifesto.txt
```

在后面的章节你会看到类似出去出来的方法可以在多项目构建中的子项目中调用。无论构建逻辑多复杂，Gradle 都可以提供给你一种简便的方式来组织它们。


## 定义默认任务

Gradle 允许在脚本中定义多个默认任务。

## 定义默认任务

build.gradle

```
defaultTasks 'clean', 'run'
task clean << {
    println 'Default Cleaning!'
}
task run << {
    println 'Default Running!'
}
task other << {
    println "I'm not a default task!"
}
```

gradle -q 的输出结果。

```
Output of gradle -q
\> gradle -q
Default Cleaning!
Default Running!
```

这与直接调用 gradle clean run 效果是一样的。在多项目构建中，每个子项目都可以指定单独的默认任务。如果子项目未进行指定将会调用父项目指定的的默认任务。 

## Configure by DAG

稍后会对 Gradle 的配置阶段和运行阶段进行详细说明 配置阶段后，Gradle 会了解所有要执行的任务 Gradle 提供了一个钩子来捕获这些信息。一个例子就是可以检查已经执行的任务中有没有被释放。借由此，你可以为一些变量赋予不同的值。 

在下面的例子中，为 distribution 和 release 任务赋予了不同的 version 值。

### 依赖任务的不同输出

build.gradle

```
task distribution << {
    println "We build the zip with version=$version"
}
task release(dependsOn: 'distribution') << {
    println 'We release now'
}
gradle.taskGraph.whenReady {taskGraph ->
    if (taskGraph.hasTask(release)) {
        version = '1.0'
    } else {
        version = '1.0-SNAPSHOT'
    }
}
```

gradle -q distribution 的输出结果

```
Output of gradle -q distribution
\> gradle -q distribution
We build the zip with version=1.0-SNAPSHOT
```

gradle -q release 的输出结果

```
Output of gradle -q release
\> gradle -q release
We build the zip with version=1.0
We release now
```

whenReady 会在已发布的任务之前影响到已发布任务的执行。即使已发布的任务不是主要任务(也就是说，即使这个任务不是通过命令行直接调用)

## 下一步目标

在本章中，我们了解了什么是 task，但这还不够详细。欲知更多请参阅章节[任务进阶](detailed-task-fifeen.md)。

另外，可以目录继续学习[Java 构建入门](java-quickstart.md)和[依赖管理基础](dependency-management-basics.md)。

