# 编写构建脚本  
  
这一章着眼于一些编写构建脚本的详细信息。

## Gradle 构建语言  

Gradle 提供一种领域特定语言或者说是 DSL，来描述构建。这种构建语言基于 Groovy 中，并进行了一些补充，使其易于描述构建。

## Project API  

在[Java 构建入门](java-quickstart.md)的教程中，我们使用了 apply ()方法。这方法从何而来？我们之前说在 Gradle 中构建脚本定义了一个项目（project）。在构建的每一个项目中，Gradle 创建了一个 Project 类型的实例，并在构建脚本中关联此 Project 对象。当构建脚本执行时，它会配置此 Project 对象：

- 在构建脚本中，你所调用的任何一个方法，如果在构建脚本中未定义，它将被委托给 Project 对象。
- 在构建脚本中，你所访问的任何一个属性，如果在构建脚本里未定义，它也会被委托给 Project 对象。    

下面我们来试试这个，试试访问 Project 对象的 name 属性。

**访问 Project 对象的属性**

build.gradle  
  
```
println name
println project.name  
```  

gradle -q check 的输出结果  

```
> gradle -q check
projectApi
projectApi  
```

这两个 println 语句打印出相同的属性。在生成脚本中未定义的属性，第一次使用时自动委托到 Project 对象。其他语句使用了在任何构建脚本中可以访问的 project 属性，则返回关联的 Project 对象。只有当您定义的属性或方法 Project 对象的一个成员相同名字时，你才需要使用 project 属性。

## 标准 project 属性

Project对象提供了一些在构建脚本中可用的标准的属性。下表列出了常用的几个属性。

表 Project 属性
  
<table>
<tr >
<td>
名称</td>
<td >
类型</td>
<td >
默认&#20540;</td>
</tr>
<tr>
<td>
<code>project</code></td>
<td>
<a  href="file:///E:/%E7%BF%BB%E8%AF%91/gradle/dsl/org.gradle.api.Project.html"><code>Project</code></a></td>
<td>
<code >Project</code>实例</td>
</tr>
<tr >
<td >
<code>name</code></td>
<td >
<code >String</code></td>
<td >
项目目录的名称。</td>
</tr>
<tr >
<td >
<code>path</code></td>
<td>
<code>String</code></td>
<td>
项目的绝对路径。</td>
</tr>
<tr>
<td>
<code>description</code></td>
<td>
<code>String</code></td>
<td>
项目的描述。</td>
</tr>
<tr>
<td>
<code>projectDir</code></td>
<td>
<code >File</code></td>
<td>
包含生成脚本的目录。</td>
</tr>
<tr>
<td>
<code >buildDir</code></td>
<td>
<code>File</code></td>
<td >
<code ><span><code >projectDir</code></span>/build</code></td>
</tr>
<tr >
<td >
<code >group</code></td>
<td >
<code>Object</code></td>
<td >
<code >未指定</code></td>
</tr>
<tr>
<td >
<code>version</code></td>
<td >
<code >Object</code></td>
<td >
<code>未指定</code></td>
</tr>
<tr>
<td>
<code>ant</code></td>
<td>
<a  href="file:///E:/%E7%BF%BB%E8%AF%91/gradle/javadoc/org/gradle/api/AntBuilder.html" ><code>AntBuilder</code></a></td>
<td >
<code>AntBuilder</code>实例</td>
</tr>
</table>
  
## Script API  

当 Gradle 执行一个脚本时，它将脚本编译为一个实现了 Script 接口的类。这意味着所有由该Script 接口声明的属性和方法在您的脚本中是可用的。

## 声明变量  

有两类可以在生成脚本中声明的变量： 局部变量和额外属性。

### 局部变量局部

局部变量是用 def 关键字声明的。它们只在定义它们的范围内可以被访问。局部变量是 Groovy 语言底层的一个特征。

**示例 13.2. 使用局部变量**

build.gradle  
  
```
def dest = "dest"
task copy(type: Copy) {
    from "source"
    into dest
}  
```  

### 额外属性

Gradle 的域模型中，所有增强的对象都可以容纳用户定义的额外的属性。这包括但并不限于项目（project）、任务（task）和源码集（source set）。额外的属性可以通过所属对象的 ext 属性进行添加，读取和设置。或者，可以使用 ext 块同时添加多个属性。

**例子 使用额外属性**

build.gradle  
  
```
apply plugin: "java"
ext {
    springVersion = "3.1.0.RELEASE"
    emailNotification = "build@master.org"
}
sourceSets.all { ext.purpose = null }
sourceSets {
    main {
        purpose = "production"
    }
    test {
        purpose = "test"
    }
    plugin {
        purpose = "production"
    }
}
task printProperties << {
    println springVersion
    println emailNotification
    sourceSets.matching { it.purpose == "production" }.each { println it.name }
}   
```  

gradle -q printProperties的输出结果  

```
> gradle -q printProperties
3.1.0.RELEASE
build@master.org
main
plugin  
```

在此示例中， 一个 ext 代码块将两个额外属性添加到 project 对象中。此外，通过将ext.purpose 设置为 null（null是一个允许的值），一个名为 purpose 的属性被添加到每个源码集（source set）。一旦属性被添加，他们就可以像预定的属性一样被读取和设置。

通过添加属性所要求特殊的语法，Gradle 可以在你试图设置 （预定义的或额外的） 的属性，但该属性拼写错误或不存在时 fail fast。[[5]](#footname)额外属性在任何能够访问它们所属的对象的地方都可以被访问，这使它们有着比局部变量更广泛的作用域。父项目上的额外属性，在子项目中也可以访问。

有关额外属性和它们的 API 的详细信息，请参阅 ExtraPropertiesExtension。

## 一些 Groovy 的基础知识  

Groovy 提供了用于创建 DSL 的大量特点，并且 Gradle 构建语言利用了这些特点。了解构建语言是如何工作的，将有助于你编写构建脚本，特别是当你开始写自定义插件和任务的时候。

### Groovy JDK

Groovy 对 JVM 的类增加了很多有用的方法。例如， iterable 新增的 each 方法，会对iterable 的元素进行遍历：

** Groovy JDK 的方法**

build.gradle  
  
```
// Iterable gets an each() method
configurations.runtime.each { File f -> println f }  
```  

可以看看[http://groovy.codehaus.org/groovy-jdk/](http://groovy.codehaus.org/groovy-jdk/)，了解更多详细信息。

### 属性访问器

Groovy 会自动地把一个属性的引用转换为对适当的 getter 或 setter 方法的调用。

**属性访问器**

build.gradle  
  
```
// Using a getter method
println project.buildDir
println getProject().getBuildDir()
// Using a setter method
project.buildDir = 'target'
getProject().setBuildDir('target')   
```  

### 括号可选的方法调用

调用方法时括号是可选的。

**不带括号的方法调用**

build.gradle  

```
test.systemProperty 'some.prop', 'value'
test.systemProperty('some.prop', 'value')  
```  

### List 和 Map

Groovy 提供了一些定义 List 和 Map 实例的快捷写法。

**List and map**

build.gradle  
  
```
// List literal
test.includes = ['org/gradle/api/**', 'org/gradle/internal/**']
List<String> list = new ArrayList<String>()
list.add('org/gradle/api/**')
list.add('org/gradle/internal/**')
test.includes = list
// Map literal
apply plugin: 'java'
Map<String, String> map = new HashMap<String, String>()
map.put('plugin', 'java')
apply(map)  
```  

### 作为方法最后一个参数的闭包

Gradle DSL 在很多地方使用闭包。你可以在这里查看更多有关闭包的资料。当方法的最后一个参数是一个闭包时，你可以把闭包放在方法调用之后：

**作为方法参数的闭包**

build.gradle  
  
```
repositories {
    println "in a closure"
}
repositories() { println "in a closure" }
repositories({ println "in a closure" })   
```   

### 闭包委托（delegate）

每个闭包都有一个委托对象，Groovy 使用它来查找变量和方法的引用，而不是作为闭包的局部变量或参数。Gradle 在配置闭包中使用到它，把委托对象设置为被配置的对象。

**闭包委托**

build.gradle  
  
```
dependencies {
    assert delegate == project.dependencies
    compile('junit:junit:4.11')
    delegate.compile('junit:junit:4.11')
}  
```


----------
<a name="footname">
[5] </a>截至 Gradle 1.0-milestone-9 版本，我们鼓励但不强制要求使用 ext 来添加额外属性因此，当未知的属性被设置时，Gradle不会构建失败。然而，它将打印一个警告。