# Web 工程构建

本章介绍了 Gradle 对 Web 工程的相关支持。Gradle 为 Web 开发提供了两个主要插件，War plugin 和 Jetty plugin。 其中 War plugin 继承自 Java plugin，可以用来打 war 包。jetty plugin 继承自 War plugin 作为工程部署的容器。

## 打 War 包

需要打包 War 文件，需要在脚本中使用 War plugin：

### War plugin

build.gradle

```
apply plugin: 'war'
```

备注：本示例代码可以在 Gradle 发行包中的 samples/webApplication/quickstart 路径下找到。

由于继承自 Java 插件，当你执行 **gradle build** 时，将会编译、测试、打包你的工程。Gradle 会在  `src/main/webapp` 下寻找 Web 工程文件。编译后的 classes 文件以及运行时依赖也都会被包含在 War 包中。

> Groovy web构建
> 
> 在一个工程中你可以采用多个插件。比如你可以在 web 工程中同时使用 War plugin 和 Groovy plugin。插件会将 Gradle 依赖添加到你的 War 包中。

## Web 工程启动

要启动 Web 工程，只需使用 Jetty plugin 即可：

### 采用 Jetty plugin 启动 web 工程

build.gradle

```
apply plugin: 'jetty'
```

由于 Jetty plugin 继承自 War plugin。调用 gradle jettyRun 将会把你的工程启动部署到 jetty 容器中。调用 gradle jettyRunWar 会打包并启动部署到 jetty 容器中。

待添加：使用哪个 URL，配置端口，使用源文件的地方，可编辑你的文件，以及重新加载的内容。


