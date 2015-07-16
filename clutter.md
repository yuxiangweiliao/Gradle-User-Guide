# 教程-杂七杂八  
  
## 创建目录  

有一个常见的情况是，多个任务都依赖于某个目录的存在。当然，你可以在这些任务的开始加入 mkdir 来解决这个问题。但这是种臃肿的解决方法。这里有一个更好的解决方案 (仅适用于这些需要这个目录的任务有着 dependsOn 的关系的情况)：

**使用 mkdir 创建目录**

build.gradle  
  
```
classesDir = new File('build/classes')
task resources << {
    classesDir.mkdirs()
    // do something
}
task compile(dependsOn: 'resources') << {
    if (classesDir.isDirectory()) {
        println 'The class directory exists. I can operate'
    }
    // do something
}  
```
gradle -q compile的输出结果  

```
> gradle -q compile
The class directory exists. I can operate  
```

## Gradle 属性和系统属性  

Gradle 提供了许多方式将属性添加到您的构建中。 从Gradle 启动的 JVM，你可以使用 -D 命令行选项向它传入一个系统属性。 **Gradle** 命令的-D选项和 **java** 命令的 -D 选项有着同样的效果。

此外，您也可以通过属性文件向您的 project 对象添加属性。您可以把一个 gradle.properties 文件放在 Gradle 的用户主目录（默认为 USER_HOME/.gradle），或您的项目目录中。对于多项目构建，您可以将 gradle.properties 文件放在任何子项目的目录中。通过 project 对象，可以访问到 gradle.properties 里的属性。用户的主目录中的属性文件比项目目录中的属性文件更先被访问到。

你也可以通过使用-P命令行选项来直接向您的项目对象添加属性。在更多的用法中，您甚至可以通过系统和环境属性把属性直接传给项目对象。例如，如果你在一个持续集成服务器上运行构建，但你没有这台机器的管理员权限，而你的构建脚本需要一些不能让其他人知道的属性值，那么，您就不能使用 -P 选项。在这种情况下，您可以在项目管理部分 （对普通用户不可见） 添加一个环境属性。如果环境属性遵循 ORG_GRADLE_PROJECT_propertyName= somevalue 的模式，这里的 propertyName 会被添加到您的项目对象中。对系统属性我们也支持相同的机制。唯一的区别是，它是 org.gradle.projectpropertyName 的模式。

通过 gradle.properties 文件，你还可以设置系统属性。如果此类文件中的属性有一个systemProp.的前缀，该属性和它的值会被添加到系统属性，且不带此前缀。在多项目构建中，除了在根项目之外的任何项目里的 systemProp. 属性集都将被忽略。也就是，只有根项目gradle.properties 文件里的 systemProp. 属性会被作为系统属性。

**使用 gradle.properties 文件设置属性**

gradle.properties  
  
```
gradlePropertiesProp=gradlePropertiesValue
systemProjectProp=shouldBeOverWrittenBySystemProp
envProjectProp=shouldBeOverWrittenByEnvProp
systemProp.system=systemValue  
```  

build.gradle  
  
```
task printProps << {
    println commandLineProjectProp
    println gradlePropertiesProp
    println systemProjectProp
    println envProjectProp
    println System.properties['system']
}  
```  

`gradle -q -PcommandLineProjectProp=commandLineProjectPropValue -Dorg.gradle.project.systemProjectProp=systemPropertyValue printProps`的输出结果  

```
> gradle -q -PcommandLineProjectProp=commandLineProjectPropValue -Dorg.gradle.project.systemProjectProp=systemPropertyValue printProps
commandLineProjectPropValue
gradlePropertiesValue
systemPropertyValue
envPropertyValue
systemValue  
```

### 检查项目的属性

当你要使用一个变量时，你可以仅通过其名称在构建脚本中访问一个项目的属性。如果此属性不存在，则会引发异常，并且构建失败。如果您的构建脚本依赖于一些可选属性，而这些属性用户可能在比如 gradle.properties 文件中设置，您就需要在访问它们之前先检查它们是否存在。你可以通过使用方法 hasProperty('propertyName') 来进行检查，它返回 true 或 false。

## 使用外部构建脚本配置项目  

您可以使用外部构建脚本来配置当前项目。Gradle 构建语言的所有内容在外部脚本中也可以使用。您甚至可以在外部脚本中应用其他脚本。

**使用外部构建脚本配置项目**

build.gradle  
  
```
apply from: 'other.gradle'  
```  

other.gradle  
  
```
println "configuring $project"
task hello << {
    println 'hello from other script'
}  
```  

gradle -q hello的输出结果  

```
> gradle -q hello
configuring root project 'configureProjectUsingScript'
hello from other script  
```

## 配置任意对象  

您可以用以下非常易理解的方式配置任意对象。

**配置任意对象**

build.gradle  
  
```
task configure << {
    pos = configure(new java.text.FieldPosition(10)) {
        beginIndex = 1
        endIndex = 5
    }
    println pos.beginIndex
    println pos.endIndex
}  
```  

gradle -q configure 的输出结果  

```
> gradle -q configure
1
5  
```

## 使用外部脚本配置任意对象  

你还可以使用外部脚本配置任意对象

**使用脚本配置任意对象**

build.gradle  
  
```
task configure << {
    pos = new java.text.FieldPosition(10)
    // Apply the script
    apply from: 'other.gradle', to: pos
    println pos.beginIndex
    println pos.endIndex
}  
```  

other.gradle  
  
```
beginIndex = 1;
endIndex = 5;  
```  

gradle -q configure 的输出结果  

```
> gradle -q configure
1
5  
```

## 缓存  

为了提高响应速度，默认情况下 Gradle 会缓存所有已编译的脚本。这包括所有构建脚本，初始化脚本和其他脚本。你第一次运行一个项目构建时， Gradle 会创建 .gradle 目录，用于存放已编译的脚本。下次你运行此构建时， 如果该脚本自它编译后没有被修改，Gradle 会使用这个已编译的脚本。否则该脚本会重新编译，并把最新版本存在缓存中。如果您通过 --recompile-scripts 选项运行 Gradle ，会丢弃缓存的脚本，然后重新编译此脚本并将其存在缓存中。通过这种方式，您可以强制 Gradle 重新生成缓存。
