# 使用文件  
  
大多数构建工作都要使用到文件。Gradle 添加了一些概念和 API 来帮助您实现这一目标。

## 定位文件  

你可以使用 Project.file()方法来找到一个相对于项目目录的文件 。

**查找文件**

build.gradle  
  
```
// Using a relative path
File configFile = file('src/config.xml')
// Using an absolute path
configFile = file(configFile.absolutePath)
// Using a File object with a relative path
configFile = file(new File('src/config.xml'))  
``` 

您可以把任何对象传递给 file()方法，而它将尝试将其转换为一个绝对路径的 File 对象。通常情况下，你会传给它一个 String 或 File 的实例。而所提供的这个对象的 tostring() 方法的值会作为文件路径。如果这个路径是一个绝对路径，它会用于构构一个 File 实例。否则，会通过先计算所提供的路径相对于项目目录的相对路径来构造 File 实例。这个 file()方法也可以识别URL，例如是 `file:/some/path.xml`。

这是把一些用户提供的值转换为一个相对路径的 File 对象的有用方法。由于 file()方法总是去计算所提供的路径相对于项目目录的路径，最好是使用 new File(somePath)，因为它是一个固定的路径，而不会因为用户运行 Gradle 的具体工作目录而改变。

## 文件集合  

一个文件集合只是表示一组文件。它通过 FileCollection 接口来表示。Gradle API 中的许多对象都实现了此接口。比如，依赖配置 就实现了 FileCollection 这一接口。

使用 Project.files()方法是获取一个 FileCollection 实例的其中一个方法。你可以向这个方法传入任意个对象，而它们会被转换为一组 File 对象。这个 Files() 方法接受任何类型的对象作为其参数。根据16.1 章节 “定位文件”里对 file() 方法的描述，它的结果会被计算为相对于项目目录的相对路径。你也可以将集合，迭代变量，map 和数组传递给 files() 方法。它们会被展开，并且内容会转换为 File 实例。

**创建一个文件集合**

build.gradle  
  
```
FileCollection collection = files('src/file1.txt', new File('src/file2.txt'), ['src/file3.txt', 'src/file4.txt'])  
```  

一个文件集合是可迭代的，并且可以使用 as 操作符转换为其他类型的对象集合。您还可以使用+运算符把两个文件集合相加，或使用-运算符减去一个文件集合。这里是一些使用文件集合的例子。

**使用一个文件集合**

build.gradle  

```
// Iterate over the files in the collection
collection.each {File file ->
    println file.name
}
// Convert the collection to various types
Set set = collection.files
Set set2 = collection as Set
List list = collection as List
String path = collection.asPath
File file = collection.singleFile
File file2 = collection as File
// Add and subtract collections
def union = collection + files('src/file3.txt')
def different = collection - files('src/file3.txt')  
```  

你也可以向 files()方法传一个闭包或一个 Callable 实例。它会在查询集合内容，并且它的返回值被转换为一组文件实例时被调用。这个闭包或 Callable 实例的返回值可以是 files() 方法所支持的任何类型的对象。这是 “实现” FileCollection 接口的简单方法。

**实现一个文件集合**

build.gradle  
  
```
task list << {
    File srcDir
    // Create a file collection using a closure
    collection = files { srcDir.listFiles() }
    srcDir = file('src')
    println "Contents of $srcDir.name"
    collection.collect { relativePath(it) }.sort().each { println it }
    srcDir = file('src2')
    println "Contents of $srcDir.name"
    collection.collect { relativePath(it) }.sort().each { println it }
}  
```  

gradle -q list 的输出结果  

```
> gradle -q list
Contents of src
src/dir1
src/file1.txt
Contents of src2
src2/dir1
src2/dir2  
```

你可以向 files() 传入一些其他类型的对象：

**FileCollection**

它们会被展开，并且内容会被包含在文件集合内。

**Task**  

任务的输出文件会被包含在文件集合内。

**TaskOutputs**  
 
TaskOutputs 的输出文件会被包含在文件集合内。

要注意的一个地方是，一个文件集合的内容是缓计算的，它只在需要的时候才计算。这意味着您可以，比如创建一个 FileCollection 对象而里面的文件会在以后才创建，比方说在一些任务中才创建。

## 文件树  

文件树是按层次结构排序的文件集合。例如，文件树可能表示一个目录树或 ZIP 文件的内容。它通过 FileTree 接口表示。FileTree 接口继承自 FileCollection，所以你可以用对待文件集合一样的方式来对待文件树。Gradle 中的几个对象都实现了 FileTree 接口，例如 source sets。

使用 Project.fileTree()方法是获取一个 FileTree 实例的其中一种方法。它将定义一个基目录创建 FileTree 对象，并可以选择加上一些 Ant 风格的包含与排除模式。

**创建一个文件树**

build.gradle  
  
```
// Create a file tree with a base directory
FileTree tree = fileTree(dir: 'src/main')
// Add include and exclude patterns to the tree
tree.include '**/*.java'
tree.exclude '**/Abstract*'
// Create a tree using path
tree = fileTree('src').include('**/*.java')
// Create a tree using closure
tree = fileTree('src') {
    include '**/*.java'
}
// Create a tree using a map
tree = fileTree(dir: 'src', include: '**/*.java')
tree = fileTree(dir: 'src', includes: ['**/*.java', '**/*.xml'])
tree = fileTree(dir: 'src', include: '**/*.java', exclude: '**/*test*/**')  
```  

你可以像使用一个文件集合的方式一样来使用一个文件树。你也可以使用 Ant 风格的模式来访问文件树的内容或选择一个子树：

**使用文件树**

build.gradle 
 
```
// Iterate over the contents of a tree
tree.each {File file ->
    println file
}
// Filter a tree
FileTree filtered = tree.matching {
    include 'org/gradle/api/**'
}
// Add trees together
FileTree sum = tree + fileTree(dir: 'src/test')
// Visit the elements of the tree
tree.visit {element ->
    println "$element.relativePath => $element.file"
}  
```  

## 使用归档文件的内容作为文件树  

您可以使用档案的内容，如 ZIP 或者 TAR 文件，作为一个文件树。你可以通过使用 Project.zipTree() 或  Project.tarTree()方法来实现这一过程。这些方法返回一个 FileTree 实例，您可以像使用任何其他文件树或文件集合一样使用它。例如，您可以用它来通过复制内容扩大归档，或把一些档案合并到另一个归档文件中。

**使用归档文件作为文件树**


build.gradle  
  
```
// Create a ZIP file tree using path
FileTree zip = zipTree('someFile.zip')
// Create a TAR file tree using path
FileTree tar = tarTree('someFile.tar')
//tar tree attempts to guess the compression based on the file extension
//however if you must specify the compression explicitly you can:
FileTree someTar = tarTree(resources.gzip('someTar.ext'))  
```  

## 指定一组输入文件  

Gradle 中的许多对象都有一个接受一组输入文件的属性。例如，JavaCompile 任务有一个 source 属性，定义了要编译的源代码文件。你可以使用上面所示的 files()方法所支持的任意类型的对象设置此属性。这意味着您可以通过如 File、String、集合、FileCollection 对象，或甚至是一个闭包来设置此属性。这里有一些例子：

**指定一组文件**


build.gradle  
  
``` 
// Use a File object to specify the source directory
compile {
    source = file('src/main/java')
}
// Use a String path to specify the source directory
compile {
    source = 'src/main/java'
}
// Use a collection to specify multiple source directories
compile {
    source = ['src/main/java', '../shared/java']
}
// Use a FileCollection (or FileTree in this case) to specify the source files
compile {
    source = fileTree(dir: 'src/main/java').matching { include 'org/gradle/api/**' }
}
// Using a closure to specify the source files.
compile {
    source = {
        // Use the contents of each zip file in the src dir
        file('src').listFiles().findAll {it.name.endsWith('.zip')}.collect { zipTree(it) }
    }
}  
```  

通常情况下，有一个与属性相同名称的方法，可以追加这个文件集合。再者，这个方法接受 files()方法所支持的任何类型的参数。

**指定一组文件**


build.gradle  
  
```
compile {
    // Add some source directories use String paths
    source 'src/main/java', 'src/main/groovy'
    // Add a source directory using a File object
    source file('../shared/java')
    // Add some source directories using a closure
    source { file('src/test/').listFiles() }
}  
```  

## 复制文件  

你可以使用 Copy 任务来复制文件。复制任务非常灵活，并允许您进行，比如筛选要复制的文件的内容，或映射文件的名称。

若要使用 Copy 任务，您必须提供用于复制的源文件和目标目录。您还可以在复制文件的时候指定如何转换文件。你可以使用一个复制规范来做这些。一个复制规范通过 CopySpec 接口来表示。Copy 任务实现了此接口。你可以使用 CopySpec.from()方法指定源文件，使用 CopySpec.into()方法使用目标目录。

**使用 copy 任务复制文件**


build.gradle  
   
```
task copyTask(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
}  
```  

from() 方法接受和 files() 方法一样的任何参数。当参数解析为一个目录时，该目录下的所有文件（不包含目录本身） 都会递归复制到目标目录。当参数解析为一个文件时，该文件会复制到目标目录中。当参数解析为一个不存在的文件时，参数会被忽略。如果参数是一个任务，那么任务的输出文件 （即该任务创建的文件）会被复制，并且该任务会自动添加为 Copy 任务的依赖项。into() 方法接受和 files() 方法一样的任何参数。这里是另一个示例：

** 指定复制任务的源文件和目标目录**

build.gradle  
  
```
task anotherCopyTask(type: Copy) {
    // Copy everything under src/main/webapp
    from 'src/main/webapp'
    // Copy a single file
    from 'src/staging/index.html'
    // Copy the output of a task
    from copyTask
    // Copy the output of a task using Task outputs explicitly.
    from copyTaskWithPatterns.outputs
    // Copy the contents of a Zip file
    from zipTree('src/main/assets.zip')
    // Determine the destination directory later
    into { getDestDir() }
}  
```  

您可以使用 Ant 风格的包含或排除模式，或使用一个闭包，来选择要复制的文件：

** 选择要复制的文件**

build.gradle  
  
```
task copyTaskWithPatterns(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    include '**/*.html'
    include '**/*.jsp'
    exclude { details -> details.file.name.endsWith('.html') && details.file.text.contains('staging') }
}  
```  

此外，你也可以使用 Project.copy()方法来复制文件。它是与任务一样的工作方式，尽管它有一些主要的限制。首先， copy()不能进行增量操作（见15.9章节，"跳过处于最新状态的任务"）。

** 使用没有最新状态检查的 copy() 方法复制文件**

build.gradle  
  
```
task copyMethod << {
    copy {
        from 'src/main/webapp'
        into 'build/explodedWar'
        include '**/*.html'
        include '**/*.jsp'
    }
}  
```  

第二，当一个任务用作复制源（即作为 from() 的参数）的时候，copy()方法不能建立任务依赖性，因为它是一个方法，而不是一个任务。因此，如果您在任务的 action 里面使用 copy()方法，必须显式声明所有的输入和输出以得到正确的行为。

** 使用有最新状态检查的 copy() 方法复制文件**

build.gradle  
  
```
task copyMethodWithExplicitDependencies{
    inputs.file copyTask // up-to-date check for inputs, plus add copyTask as dependency
    outputs.dir 'some-dir' // up-to-date check for outputs
    doLast{
        copy {
            // Copy the output of copyTask
            from copyTask
            into 'some-dir'
        }
    }
}  
```  

在可能的情况下，最好是使用 Copy 任务，因为它支持增量构建和任务依赖关系推理，而不需要你额外付出。copy()方法可以作为一个任务执行的部分来复制文件。即，这个 copy()方法旨在用于自定义任务 （见第 57 章，编写自定义任务类）中，需要文件复制作为其一部分功能的时候。在这种情况下，自定义任务应充分声明与复制操作有关的输入/输出。

### 重命名文件

** 重命名复制的文件**

build.gradle  
  
```
task rename(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    // Use a closure to map the file name
    rename { String fileName ->
        fileName.replace('-staging-', '')
    }
    // Use a regular expression to map the file name
    rename '(.+)-staging-(.+)', '$1$2'
    rename(/(.+)-staging-(.+)/, '$1$2')
}  
```  

### 过滤文件

** 过滤要复制的文件**

build.gradle  
  
```
import org.apache.tools.ant.filters.FixCrLfFilter
import org.apache.tools.ant.filters.ReplaceTokens
task filter(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    // Substitute property references in files
    expand(copyright: '2009', version: '2.3.1')
    expand(project.properties)
    // Use some of the filters provided by Ant
    filter(FixCrLfFilter)
    filter(ReplaceTokens, tokens: [copyright: '2009', version: '2.3.1'])
    // Use a closure to filter each line
    filter { String line ->
        "[$line]"
    }
}  
```  

### 使用 CopySpec 类

复制规范用来组织一个层次结构。一个复制规范继承其目标路径，包含模式，排除模式，复制操作，名称映射和过滤器。

** 嵌套的复制规范**

build.gradle  
   
```
task nestedSpecs(type: Copy) {
    into 'build/explodedWar'
    exclude '**/*staging*'
    from('src/dist') {
        include '**/*.html'
    }
    into('libs') {
        from configurations.runtime
    }
}  
```  

## 使用 Sync 任务  

Sync 任务继承了 Copy 任务。当它执行时，它会将源文件复制到目标目录中，然后从目标目录移除所有不是它复制的文件。这可以用来做一些事情，比如安装你的应用程序、 创建你的归档文件的exploded 副本，或维护项目的依赖项的副本。

这里是一个例子，维护在 build/libs 目录中的项目运行时依赖的副本。

** 使用同步任务复制依赖项**

build.gradle  
  
```
task libs(type: Sync) {
    from configurations.runtime
    into "$buildDir/libs"
}  
```  

## 创建归档文件  

一个项目可以有你所想要的一样多的 JAR 文件。您也可以将 WAR、 ZIP 和 TAG 文件添加到您的项目。使用各种归档任务可以创建以下的归档文件： Zip, Tar, Jar, War, and Ear. 他们的工作方式都一样，所以让我们看看如何创建一个 ZIP 文件。

** 创建一个 ZIP 文件**

build.gradle  
  
```
apply plugin: 'java'
task zip(type: Zip) {
    from 'src/dist'
    into('libs') {
        from configurations.runtime
    }
}  
```  

为什么要用 Java 插件？

Java 插件对归档任务添加了一些默认值。如果你愿意，使用归档任务时可以不需要 Java 插件。您需要提供一些值给附加的属性。  

归档任务与 Copy 任务的工作方式一样，并且实现了相同的 CopySpec 接口。像使用 Copy 任务一样，你需要使用 from() 的方法指定输入的文件，并可以选择是否通过 into() 方法指定最终在存档中的位置。您可以通过一个复制规范来筛选文件的内容、 重命名文件和进行其他你可以做的事情。

### 归档命名

生成的归档的默认名称是 projectName-version.type。举个例子：

** 创建 ZIP 文件**

build.gradle  
  
```
apply plugin: 'java'
version = 1.0
task myZip(type: Zip) {
    from 'somedir'
}
println myZip.archiveName
println relativePath(myZip.destinationDir)
println relativePath(myZip.archivePath)  
```  

gradle -q myZip 的输出结果  

> gradle -q myZip
zipProject-1.0.zip
build/distributions
build/distributions/zipProject-1.0.zip  

它添加了一个名称为 myZip 的ZIP归档任务，产生 ZIP 文件 zipProject 1.0.zip。区分归档任务的名称和归档任务生成的归档文件的名称是很重要的。归档的默认名称可以通过项目属性 archivesBaseName 来更改。还可以在以后的任何时候更改归档文件的名称。

这里有很多你可以在归档任务中设置的属性。它们在以下的表 16.1，"存档任务-命名属性"中列出。你可以，比方说，更改归档文件的名称：

** 配置归档任务-自定义归档名称**

build.gradle  
  
```
apply plugin: 'java'
version = 1.0
task myZip(type: Zip) {
    from 'somedir'
    baseName = 'customName'
}
println myZip.archiveName  
```  

gradle -q myZip 的输出结果  

> gradle -q myZip
customName-1.0.zip  

您可以进一步自定义存档名称：

**示例 16.22. 配置归档任务 - appendix & classifier**

build.gradle  
  
```  

apply plugin: 'java'
archivesBaseName = 'gradle'
version = 1.0
task myZip(type: Zip) {
    appendix = 'wrapper'
    classifier = 'src'
    from 'somedir'
}
println myZip.archiveName  
```  

gradle -q myZip 的输出结果  

> gradle -q myZip
gradle-wrapper-1.0-src.zip  

表 16.1. 归档任务-命名属性
  
<table id="archiveTasksNamingProperties" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
属性名称</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
类型</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
默认&#20540;</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
描述</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">archiveName</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">baseName</code></span>-<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">appendix</code></span>-<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">version</code></span>-<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">classifier</code></span>.<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">extension</code></span></code>
<p style="margin-top:0px; margin-bottom:16px; padding-top:0px; padding-bottom:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
如果这些属性中的任何一个为空，那后面的<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">-</code>不会被添加到该名称中。</p>
</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
生成的归档文件的基本文件名</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">archivePath</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">File</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">destinationDir</code></span>/<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">archiveName</code></span></code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
生成的归档文件的绝对路径。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">destinationDir</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">File</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
依赖于归档类型。JAR包和 WAR包会生成到&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">project.buildDir</code></span>/libraries</code>中。ZIP文件和
 TAR文件会生成到<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">project.buildDir</code></span>/distributions</code>中。</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
存放生成的归档文件的目录</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">baseName</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">project.name</span></code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
归档文件的名称中的基本名称部分。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">appendix</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">null</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
归档文件的名称中的附录部分。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">version</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">project.version</span></code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
归档文件的名称中的版本部分。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">classifier</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">null</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
归档文件的名称中的分类部分。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">extension</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">String</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
依赖于归档的类型，用于TAR文件，可以是以下压缩类型：&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">zip</code>,&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">jar</code>,&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">war</code>,&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">tar</code>,&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">tgz</code>&nbsp;or&nbsp;<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">tbz2</code>.</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
归档文件的名称中的扩展名称部分。</td>
</tr>
</tbody>
</table>

### 共享多个归档之间的内容

你可以使用 Project.copySpec()方法在归档之间共享内容。

你经常会想要发布一个归档文件，这样就可从另一个项目中使用它。这一过程在第 51章，发布文件中会讲到。