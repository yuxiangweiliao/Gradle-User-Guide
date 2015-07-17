# Gradle 命令行的基本使用

本章介绍了命令行的基本使用。正如在前面的章节里你所见到的调用 gradle 命令来完成一些功能。

## 多任务调用

你可以以列表的形式在命令行中一次调用多个任务。例如 gradle compile test 命令会依次调用，并且每个任务仅会被调用一次。compile 和 test 任务以及它们的依赖任务。无论它们是否被包含在脚本中：即无论是以命令行的形式定义的任务还是依赖于其它任务都会被调用执行。来看下面的例子。

下面定义了四个任务。dist 和 test 都依赖于 compile，只用当 compile 被调用之后才会调用 gradle dist test 任务。

### 任务依赖

![](images/1-1.png)

### 多任务调用

build.gradle

```
task compile << {
    println 'compiling source'
}
task compileTest(dependsOn: compile) << {
    println 'compiling unit tests'
}
task test(dependsOn: [compile, compileTest]) << {
    println 'running unit tests'
}
task dist(dependsOn: [compile, test]) << {
    println 'building the distribution'
}
```

**gradle dist test** 的输出结果。

```
\> gradle dist test
:compile
compiling source
:compileTest
compiling unit tests
:test
running unit tests
:dist
building the distribution
BUILD SUCCESSFUL
Total time: 1 secs
```

由于每个任务仅会被调用一次，所以调用 **gradle test test** 与调用 **gradle test** 效果是相同的。

## 排除任务

你可以用命令行选项 -x 来排除某些任务，让我们用上面的例子来示范一下。

### 排除任务

**gradle dist -x test** 的输出结果。

```
\> gradle dist -x test
:compile
compiling source
:dist
building the distribution
BUILD SUCCESSFUL
Total time: 1 secs
```

可以看到，test 任务并没有被调用，即使他是 dist 任务的依赖。同时 test 任务的依赖任务 compileTest 也没有被调用，而像 compile 被 test 和其它任务同时依赖的任务仍然会被调用。

## 失败后继续执行

默认情况下只要有任务调用失败 Gradle 就是中断执行。这可能会使调用过程更快，但那些后面隐藏的错误不会被发现。所以你可以使用--continue 在一次调用中尽可能多的发现所有问题。

采用了--continue 选项，Gralde会调用*每一个*任务以及它们依赖的任务。而不是一旦出现错误就会中断执行。所有错误信息都会在最后被列出来。

一旦某个任务执行失败,那么所有依赖于该任务的子任务都不会被调用。例如由于 test 任务依赖于 complie 任务，所以如果 compile 调用出错，test 便不会被直接或间接调用。

## 简化任务名

当你试图调用某个任务的时候，无需输入任务的全名。只需提供足够的可以唯一区分出该任务的字符即可。例如，上面的例子你也可以这么写。用 **gradle di** 来直接调用 dist 任务。

### 简化任务名

**gradle di** 的输出结果

```
\> gradle di
:compile
compiling source
:compileTest
compiling unit tests
:test
running unit tests
:dist
building the distribution
BUILD SUCCESSFUL
Total time: 1 secs
```

你也可以用驼峰命名的任务中每个单词的首字母进行调用。例如，可以执行 **gradle compTest** or even **gradle cT** 来调用 compileTest 任务。

### 简化驼峰任务名

**gradle cT** 的输出结果。

```
\> gradle cT
:compile
compiling source
:compileTest
compiling unit tests
BUILD SUCCESSFUL
Total time: 1 secs
```

简化后你仍然可以使用 -x 参数。

## 选择构建位置

调用 gradle 时，默认情况下总是会构建当前目录下的文件，可以使用-b 参数选择构建的文件，并且当你使用此参数时settings。gradle 将不会生效，看下面的例子:

### 选择文件构建

subdir/myproject.gradle

```
task hello << {
    println "using build file '$buildFile.name' in '$buildFile.parentFile.name'."
}
```

**gradle -q -b subdir/myproject.gradle hello** 的输出结果

```
Output of gradle -q -b subdir/myproject.gradle hello
\> gradle -q -b subdir/myproject.gradle hello
using build file 'myproject.gradle' in 'subdir'.
```

另外，你可以使用 -p 参数来指定构建的目录，例如在多项目构建中你可以用 -p 来替代 -b 参数。

### 选择构建目录

**gradle -q -p subdir hello** 的输出结果

```
\> gradle -q -p subdir hello
using build file 'build.gradle' in 'subdir'.
```

## 获取构建信息

Gradle 提供了许多内置任务来收集构建信息。这些内置任务对于了解依赖结构以及解决问题都是很有帮助的。

了解更多，可以参阅项目报告插件以为你的项目添加构建报告。

### 项目列表

执行 **gradle projects** 会为你列出子项目名称列表。如下例。

### 收集项目信息

**gradle -q projects** 的输出结果

```
\> gradle -q projects
\------------------------------------------------------------
Root project
\------------------------------------------------------------
Root project 'projectReports'
+--- Project ':api' - The shared API for the application
\--- Project ':webapp' - The Web application implementation
To see a list of the tasks of a project, run gradle <project-path>:tasks
For example, try running gradle :api:tasks
```

这份报告展示了每个项目的描述信息。当然你可以在项目中用 description 属性来指定这些描述信息。

### 为项目添加描述信息.

build.gradle

```
description = 'The shared API for the application'
```

### 任务列表

执行 gradle tasks 会列出项目中所有任务。这会显示项目中所有的默认任务以及每个任务的描述。如下例

### 获取任务信息

**gradle -q tasks**的输出结果：

```
\> gradle -q tasks
\------------------------------------------------------------
All tasks runnable from root project
\------------------------------------------------------------
Default tasks: dists
Build tasks
\-----------
clean - Deletes the build directory (build)
dists - Builds the distribution
libs - Builds the JAR
Build Setup tasks
\-----------------
init - Initializes a new Gradle build. [incubating]
wrapper - Generates Gradle wrapper files. [incubating]
Help tasks
\----------
dependencies - Displays all dependencies declared in root project 'projectReports'.
dependencyInsight - Displays the insight into a specific dependency in root project 'projectReports'.
help - Displays a help message
projects - Displays the sub-projects of root project 'projectReports'.
properties - Displays the properties of root project 'projectReports'.
tasks - Displays the tasks runnable from root project 'projectReports' (some of the displayed tasks may belong to subprojects).
To see all tasks and more detail, run with --all.
```

默认情况下，这只会显示那些被分组的任务。你可以通过为任务设置 group 属性和 description 来把 这些信息展示到结果中。

### 更改任务报告内容

build.gradle

```
dists {
    description = 'Builds the distribution'
    group = 'build'
}
```

当然你也可以用--all 参数来收集更多任务信息。这会列出项目中所有任务以及任务之间的依赖关系。


### Obtaining more information about tasks

 **gradle -q tasks --all**的输出结果：

```
\> gradle -q tasks --all
\------------------------------------------------------------
All tasks runnable from root project
\------------------------------------------------------------
Default tasks: dists
Build tasks
\-----------
clean - Deletes the build directory (build)
api:clean - Deletes the build directory (build)
webapp:clean - Deletes the build directory (build)
dists - Builds the distribution [api:libs, webapp:libs]
    docs - Builds the documentation
api:libs - Builds the JAR
    api:compile - Compiles the source files
webapp:libs - Builds the JAR [api:libs]
    webapp:compile - Compiles the source files
Build Setup tasks
\-----------------
init - Initializes a new Gradle build. [incubating]
wrapper - Generates Gradle wrapper files. [incubating]
Help tasks
\----------
dependencies - Displays all dependencies declared in root project 'projectReports'.
dependencyInsight - Displays the insight into a specific dependency in root project 'projectReports'.
help - Displays a help message
projects - Displays the sub-projects of root project 'projectReports'.
properties - Displays the properties of root project 'projectReports'.
tasks - Displays the tasks runnable from root project 'projectReports' (some of the displayed tasks may belong to subprojects).
```

### 获取任务帮助信息

执行 **gradle help --task someTask** 可以显示指定任务的详细信息。或者多项目构建中相同任务名称的所有任务的信息。如下例。

### 获取任务帮助

**gradle -q help --task libs** 的输出结果

```
\> gradle -q help --task libs
Detailed task information for libs
Paths
     :api:libs
     :webapp:libs
Type
     Task (org.gradle.api.Task)
Description
     Builds the JAR
```

这些结果包含了任务的路径、类型以及描述信息等。

### 获取依赖列表

执行 **gradle dependencies** 会列出项目的依赖列表，所有依赖会根据任务区分，以树型结构展示出来。如下例。

### 获取依赖信息

**gradle -q dependencies api:dependencies webapp:dependencies** 的输出结果

```
\> gradle -q dependencies api:dependencies webapp:dependencies
\------------------------------------------------------------
Root project
\------------------------------------------------------------
No configurations
\------------------------------------------------------------
Project :api - The shared API for the application
\------------------------------------------------------------
compile
\--- org.codehaus.groovy:groovy-all:2.2.0
testCompile
\--- junit:junit:4.11
     \--- org.hamcrest:hamcrest-core:1.3
\------------------------------------------------------------
Project :webapp - The Web application implementation
\------------------------------------------------------------
compile
+--- project :api
|    \--- org.codehaus.groovy:groovy-all:2.2.0
\--- commons-io:commons-io:1.2
testCompile
No dependencies
```

虽然输出结果很多，但这对于了解构建任务十分有用，当然你可以通过**--configuration** 参数来查看 指定构建任务的依赖情况。

### 过滤依赖信息

**gradle -q api:dependencies --configuration testCompile** 的输出结果

```
\> gradle -q api:dependencies --configuration testCompile
\------------------------------------------------------------
Project :api - The shared API for the application
\------------------------------------------------------------
testCompile
\--- junit:junit:4.11
     \--- org.hamcrest:hamcrest-core:1.3
```

### 查看特定依赖

执行 Running gradle dependencyInsight 可以查看指定的依赖情况。如下例。

###  获取特定依赖

**gradle -q webapp:dependencyInsight --dependency groovy --configuration compile** 的输出结果

```
\> gradle -q webapp:dependencyInsight --dependency groovy --configuration compile
org.codehaus.groovy:groovy-all:2.2.0
\--- project :api
     \--- compile
```

这对于了解依赖关系、了解为何选择此版本作为依赖十分有用。了解更多请参阅[依赖检查报告](http://gradledoc.qiniudn.com/1.12/dsl/org.gradle.api.tasks.diagnostics.DependencyInsightReportTask.html)。

dependencyInsight 任务是'Help'任务组中的一个。这项任务需要进行配置才可以。如果用了 Java 相关的插件，那么 dependencyInsight 任务已经预先被配置到'Compile'下了。你只需要通过'--dependency'参数来制定所需查看的依赖即可。如果你不想用默认配置的参数项你可以通过 '--configuration' 参数来进行指定。了解更多请参阅[依赖检查报告](http://gradledoc.qiniudn.com/1.12/dsl/org.gradle.api.tasks.diagnostics.DependencyInsightReportTask.html)。

### 获取项目属性列表

执行 **gradle properties** 可以获取项目所有属性列表。如下例。

### 属性信息

**gradle -q api:properties** 的输出结果

```
\> gradle -q api:properties
\------------------------------------------------------------
Project :api - The shared API for the application
\------------------------------------------------------------
allprojects: [project ':api']
ant: org.gradle.api.internal.project.DefaultAntBuilder@12345
antBuilderFactory: org.gradle.api.internal.project.DefaultAntBuilderFactory@12345
artifacts: org.gradle.api.internal.artifacts.dsl.DefaultArtifactHandler@12345
asDynamicObject: org.gradle.api.internal.ExtensibleDynamicObject@12345
buildDir: /home/user/gradle/samples/userguide/tutorial/projectReports/api/build
buildFile: /home/user/gradle/samples/userguide/tutorial/projectReports/api/build.gradle
```

### 构建日志

**--profile** 参数可以收集一些构建期间的信息并保存到 build/reports/profile 目录下并且以构建时间命名这些文件。

下面这份日志记录了总体花费时间以及各过程花费的时间，并以时间大小倒序排列，并且记录了任务的执行情况。

如果采用了 buildSrc，那么在 buildSrc/build 下同时也会生成一份日志记录记录。

![](images/2-2.png)

## Dry Run

有时可能你只想知道某个任务在一个任务集中按顺序执行的结果，但并不想实际执行这些任务。那么你可以用-m 参数。例如 **gradle -m clean compile** 会调用 clean 和 compile，这与 tasks 可以形成互补，让你知道哪些任务可以用于执行。

## 本章小结

在本章中你学到很多用命令行可以做的事情，了解更多你可以参阅 **gradle** command in [附录 D， Gradle 命令行](http://gradledoc.qiniudn.com/1.12/userguide/gradle_command_line.html)。