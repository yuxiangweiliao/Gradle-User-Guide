# OSGi 插件  
  
OSGi 插件提供了工厂方法来创建一个 [OsgiManifest](http://blog.csdn.net/?aspxerrorpath=/maosidiaoxian/article/javadoc/org/gradle/api/plugins/osgi/OsgiManifest.html) 对象。OsgiManifest 继承自 [Manifest](http://blog.csdn.net/maosidiaoxian/article/javadoc/org/gradle/api/java/archives/Manifest.html)。要了解常见的清单处理的更多信息，请参阅[第 23.13.1节，“Manifest”](http://www.aqute.biz/Code/Bnd)。如果应用了 Java 插件，OSGi 插件将把默认 jar 的 manifest 对象替换为一个 OsgiManifest 对象。被替换的 manifest 会被合并到新的对象单中。

OSGi 插件使 Peter Kriens [BND tool](http://www.aqute.biz/Code/Bnd) 大量使用。

## 用法  

要使用 OSGi 插件，请在构建脚本中包含以下语句：

**示例 使用 OSGi 插件**

build.gradle  
  
```
apply plugin: 'osgi'   
```  

## 隐式应用插件  

适用于 Java 基础插件。

## 任务  

此插件不会添加任何任务。

## 依赖管理  

待决定

## 约定对象  

OSGi 插件添加了下列约定对象： [OsgiPluginConvention](http://blog.csdn.net/maosidiaoxian/article/dsl/org.gradle.api.plugins.osgi.OsgiPluginConvention.html)

### 约定属性

OSGi 插件没有向 project 添加任何的公约属性。

### 约定方法

OSGi 插件添加了以下方法。有关更多详细信息，请参见约定对象的 API 文档。

表 OSGi 方法
  
<table id="N13C87" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
方法</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
返回类型</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background-color:rgb(242,242,242)">
描述</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
osgiManifest()</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
<a target="_blank" class="ulink" href="../javadoc/org/gradle/api/plugins/osgi/OsgiManifest.html" target="_top" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">OsgiManifest</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important">
返回一个 OsgiManifest 对象。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background:rgb(247,247,247)">
osgiManifest(Closure cl)</td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background:rgb(247,247,247)">
<a target="_blank" class="ulink" href="../javadoc/org/gradle/api/plugins/osgi/OsgiManifest.html" target="_top" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">OsgiManifest</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:normal; line-height:inherit; vertical-align:text-top; text-align:left; float:none!important; background:rgb(247,247,247)">
返回一个通过闭包配置的 OsgiManifest 对象。</td>
</tr>
</tbody>
</table>

在 classes 目录下的类文件会被分析出关于它们的包的依赖，以及它们所公布的包名。并基于此计算 OSGi Manifest 中 Import-Package 和 Export-Package 的值。如果 classpath 中包含了 jar 包和 OSGi bundle，bundle 信息会被用来指定 Import-Package 的值的版本信息。在 OsgiManifest 对象的显式属性旁边，你可以添加 instructions。

**示例 OSGi MANIFEST.MF 文件配置**

build.gradle  
  
```
jar {
    manifest { // the manifest of the default jar is of type OsgiManifest
        name = 'overwrittenSpecialOsgiName'
        instruction 'Private-Package',
                'org.mycomp.package1',
                'org.mycomp.package2'
        instruction 'Bundle-Vendor', 'MyCompany'
        instruction 'Bundle-Description', 'Platform2: Metrics 2 Measures Framework'
        instruction 'Bundle-DocURL', 'http://www.mycompany.com'
    }
}
task fooJar(type: Jar) {
    manifest = osgiManifest {
        ~instruction 'Bundle-Vendor', 'MyCompany'
    }
}  
```  

instruction 调用的第一个参数是属性的键。其他参数构成了它的值。他们由 Gradle 使用,分隔符连接。要了解更多关于 instructions 的信息，可以看看 [BND tool](http://www.aqute.biz/Code/Bnd)。

