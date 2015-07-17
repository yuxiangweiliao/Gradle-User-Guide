# War 插件  
  
War 的插件继承自 Java 插件并添加了对组装 web 应用程序的 WAR 文件的支持。它禁用了 Java 插件生成默认的 JAR archive，并添加了一个默认的 WAR archive 任务。

## 用法  

要使用 War 的插件，请在构建脚本中包含以下语句：

**使用War 插件**

build.gradle  
  
```
apply plugin: 'war'  
```  

## 任务  

War 插件向 project 中添加了以下任务。

表 War 插件 - 任务

<table>
<tr >
<td 
任务名称</td>
<td>
依赖于</td>
<td>
类型</td>
<td>
描述</td>
</tr>
<tr>
<td >
<code>war</code></td>
<td >
<code >compile</code></td>
<td >
<a  href="file:///E:/translator/OmegaT/gradle/dsl/org.gradle.api.tasks.bundling.War.html"><code>War</code></a></td>
<td>
组装应用程序 WAR 文件。</td>
</tr>
</table> 

War 插件向 Java 插件所加入的 tasks 添加了以下的依赖。

表 War 插件 - 额外的task 依赖
  
<table >
<tr >
<td >
任务名称</td>
<td >
依赖于</td>
</tr>
<tr>
<td >
assemble</td>
<td >
war</td>
</tr>
</table>

图 26.1. War 插件 - tasks

![](images/05.png)  

## 项目布局
表 26.3. War 插件 - 项目布局
  
<table>
<tr>
<td>
目录</td>
<td>
意义</td>
</tr>
<tr>
<td>
<code>from
 &lt;s1&gt;'src/main/webapp'&lt;/s1&gt;</code></td>
<td >
Web 应用程序源代码</td>
</tr>
</table>

## 依赖管理  

War 插件添加了两个依赖配置： providedCompile 和 providedRuntime。虽然它们有各自的compile 和 runtime 配置，但这些配置有相同的作用域，只是它们不会添加到 WAR 文件中。要特别注意，这些 provided 配置的传递使用。假设你添加 commons-httpclient:commons-httpclient:3.0 依赖到任何一个 provided 配置。这个依赖又依赖于 commons-codec。这意味着 httpclient 和 commons-codec 都不会添加到你的 WAR 中，即使 commons-codec 是 compile 配置上的一个显示依赖。如果你不想要这种传递行为，只是把 provided 依赖声明成和commons-httpclient:commons-httpclient:3.0@jar 一样。

## 公约属性  

表 War插件 ​​- 目录属性
  
<table>
<tr>
<td >
属性名称</td>
<td >
类型</td>
<td >
默认&#20540;</td>
<td >
描述</td>
</tr>
<tr >
<td >
<code >webAppDirName</code></td>
<td >
<code >String</code></td>
<td >
<code>from
 &lt;s1&gt;'src/main/webapp'&lt;/s1&gt;</code></td>
<td >
web 应用程序源目录的名称，是一个相对于项目目录的目录名称。</td>
</tr>
<tr>
<td >
<code>webAppDir</code></td>
<td >
<code >File</code>&nbsp;(read-only)</td>
<td>
<code ><span ><code >projectDir</code></span>/<span ><code>webAppDirName</code></span></code></td>
<td >
Web 应用程序的源目录。</td>
</tr>
</table>

这些属性由一个 WarPluginConvention 公约对象提供。

## War  

War task 的默认行为是将 src/main/webapp 的内容复制到 archive 的根目录下。你的webapp 目录自然可能包含一个 WEB-INF 子目录，这个子目录可能还再包含一个 web.xml 文件。已编译的类被编译进 WEB-INF/classes。所有 runtime [13]配置的依赖被复制到 WEB-INF/lib。

另请参阅 War。

## 自定义  

下面是一个示例，展示了最重要的自定义选项：

**war 插件的自定义**

build.gradle  
  
```
configurations {
   moreLibs
}
repositories {
   flatDir { dirs "lib" }
   mavenCentral()
}
dependencies {
    compile module(":compile:1.0") {
        dependency ":compile-transitive-1.0@jar"
        dependency ":providedCompile-transitive:1.0@jar"
    }
    providedCompile "javax.servlet:servlet-api:2.5"
    providedCompile module(":providedCompile:1.0") {
        dependency ":providedCompile-transitive:1.0@jar"
    }
    runtime ":runtime:1.0"
    providedRuntime ":providedRuntime:1.0@jar"
    testCompile 'junit:junit:4.11'
}
    moreLibs ":otherLib:1.0"
}
war {
    from 'src/rootContent' // adds a file-set to the root of the archive
    webInf { from 'src/additionalWebInf' } // adds a file-set to the WEB-INF dir.
    classpath fileTree('additionalLibs') // adds a file-set to the WEB-INF/lib dir.
    classpath configurations.moreLibs // adds a configuration to the WEB-INF/lib dir.
    webXml = file('src/someWeb.xml') // copies a file to WEB-INF/web.xml
}  
```  

当然，你可以用一个定义了 excludes 和 includes 的闭包来配置不同的文件集。