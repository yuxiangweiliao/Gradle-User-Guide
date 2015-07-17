# Checkstyle 插件  
  
Checkstyle 插件使用 Checkstyle 对你的项目的 Java 源文件执行质量检查，并从检查结果中生成报告。

## 用法  

要使用 Checkstyle 插件，请在构建脚本中包含以下语句：

**示例 29.1. 使用 Checkstyle 插件**

build.gradle  
  
```  
apply plugin: 'checkstyle'  
```  

该插件向你的项目添加了大量的执行质量检查的任务。你可以通过运行 gradle check 执行检查。

## Tasks  

Checkstyle 插件向 project 中添加了以下 tasks：

表 Checkstyle 插件 - tasks
  
<table id="N1335A" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
任务名称</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
依赖于</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
类型</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
描述</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyleMain</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">classes</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<a target="_blank" target="_blank" class="ulink" href="file:///E:/translator/OmegaT/gradle/dsl/org.gradle.api.plugins.quality.Checkstyle.html" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">checkstyle</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
针对生产Java 源文件运行 Checkstyle。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyleTest</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">testClasses</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
<a target="_blank" target="_blank" class="ulink" href="file:///E:/translator/OmegaT/gradle/dsl/org.gradle.api.plugins.quality.Checkstyle.html" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">checkstyle</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important; background:rgb(247,247,247)">
针对测试 Java 源文件运行 Checkstyle。</td>
</tr>
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyle<span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">SourceSet</code></span></code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap"><span class="replaceable" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline"><code style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">sourceSet</code></span>Classes</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<a target="_blank" target="_blank" class="ulink" href="file:///E:/translator/OmegaT/gradle/dsl/org.gradle.api.plugins.quality.Checkstyle.html" style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; color:rgb(0,112,66); text-decoration:none"><code class="classname" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">checkstyle</code></a></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
针对source set 的 Java 源文件运行 Checkstyle。</td>
</tr>
</tbody>
</table>

Checkstyle 插件向 Java 插件所加入的 tasks 添加了以下的依赖。

表 Checkstyle 插件-额外的 task 依赖

<table id="N133B1" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
任务名称</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
依赖于</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">check</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
所有 Checkstyle tasks，包括<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyleMain</code>和<code class="literal" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyleTest</code>。</td>
</tr>
</tbody>
</table>  

## 项目布局  

Checkstyle 插件预计是以下的项目布局：

表 Checkstyle 插件-项目布局
  
<table id="N133CD" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
File</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
意义</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">config/checkstyle/checkstyle.xml</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
Checkstyle 配置文件</td>
</tr>
</tbody>
</table>

## 依赖管理  

Checkstyle 插件添加了下列的依赖配置：

表 Checkstyle 插件-依赖配置
  
<table id="N133E9" style="margin:0px 0px 1.4em; padding:0px; border:1px solid rgb(208,208,208); font-family:inherit; font-size:14px; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:middle; border-collapse:collapse; border-spacing:0px; min-width:50%">
<thead style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
名称</td>
<td style="margin:0px; padding:0.3em 0.8em; border-width:0px 0px 1px; border-bottom-style:solid; border-bottom-color:rgb(208,208,208); font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:bold; line-height:inherit; vertical-align:text-top; float:none!important; background-color:rgb(242,242,242)">
意义</td>
</tr>
</thead>
<tbody style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<tr style="margin:0px; padding:0px; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline">
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
<code class="filename" style="margin:0px; padding:0px; border:0px; font-family:'Ubuntu Mono',courier,monospace; font-size:undefined; font-style:inherit; font-variant:inherit; font-weight:inherit; line-height:inherit; vertical-align:baseline; white-space:nowrap">checkstyle</code></td>
<td style="margin:0px; padding:0.3em 0.8em; border:0px; font-family:inherit; font-size:undefined; font-style:inherit; font-variant:inherit; line-height:inherit; vertical-align:text-top; float:none!important">
用到的 Checkstyle 库</td>
</tr>
</tbody>
</table>

## 配置  

请参阅 CheckstyleExtension。

