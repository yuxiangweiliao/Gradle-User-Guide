# 构建环境  
  
## 通过 gradle.properties 配置构建环境  

Gradle 提供了几个选项，可以很容易地配置将用于执行您的构建的 Java 进程。当可以通过 GRADLE\_OPTS 或 JAVA\_OPTS 在你的本地环境中配置这些选项时，如果某些设置如 JVM 内存设置，Java home，守护进程的开/关，它们可以和你的项目在你的版本控制系统中被版本化的话，将会更有用，这样整个团队就可以使用一致的环境了。在你的构建当中，建立一致的环境，就和把这些配置放进 gradle.properties 文件一样简单。这些配置将会按以下顺序被应用（以防在多个地方都有配置时只有最后一个 生效）：

- 位于项目构建目录的 gradle.properties。
- 位于 gradle 用户主目录的 gradle.properties。
- 系统属性，例如当在命令行中使用 -Dsome.property 时。  

下面的属性可以用于配置 Gradle 构建环境：

org.gradle.daemon
当设置为 true 时，Gradle 守护进程会运行构建。对于本地开发者的构建而言，这是我们最喜欢的属性。开发人员的环境在速度和反馈上会优化，所以我们几乎总是使用守护进程运行 Gradle 作业。由于 CI 环境在一致性和可靠性上的优化，我们不通过守护进程运行 CI 构建（即长时间运行进程）。

org.gradle.java.home  
为 Gradle 构建进程指定 java home 目录。这个值可以设置为 jdk 或 jre 的位置，不过，根据你的构建所做的，选择 jdk 会更安全。如果该设置未指定，将使用合理的默认值。

org.gradle.jvmargs  
指定用于该守护进程的 jvmargs。该设置对调整内存设置特别有用。目前的内存上的默认设置很大方。

org.gradle.configureondemand  
启用新的孵化模式，可以在配置项目时使得 Gradle 具有选择性。只适用于相关的项目被配置为在大型多项目中更快地构建。

org.gradle.parallel  
如果配置了这一个，Gradle 将在孵化的并行模式下运行。

### Forked java 进程

许多设置（如 java 版本和最大堆大小）可以在启动一个新的 JVM 构建进程时指定。这意味着 Gradle 在分析了各种 gradle.properties 文件之后，必须启动一个单独的 JVM 进程，以执行构建操作。当通过守护进程运行时，带有正确参数的 JVM 会启动一次，并在每次的守护进程构建执行时复用。当不通过守护进程执行 Gradle 时，在每次构建执行中都必须启动一个新的 JVM ，除非 JVM 是由 Gradle 启动脚本启动的，并且恰好具有相同的参数。

在执行每个构建时运行一个额外的 JVM 的代价是非常昂贵的，这就是为什么我们强烈推荐您使用 Gradle 守护进程，如果你指定了 org.gradle.java.home 或 org.gradle.jvmargs。更多详细信息，请参阅第十九章， Gradle 守护进程。

## 通过代理访问网站  

配置 HTTP 代理服务器 （例如用于下载依赖） 是通过标准的 JVM 系统属性来做的。这些属性可以直接在构建脚本中设置；例如设置代理主机为 System.setProperty （'http.proxyHost', 'www.somehost.org'）。或者，可以在构建的根目录或 Gradle 主目录中的 gradle.properties 文件中指定这些属性。

**配置 HTTP 代理服务器**
  
```
gradle.properties  
  
systemProp.http.proxyHost=www.somehost.org
systemProp.http.proxyPort=8080
systemProp.http.proxyUser=userid
systemProp.http.proxyPassword=password
systemProp.http.nonProxyHosts=*.nonproxyrepos.com|localhost  
```  

对于 HTTPS 有单独的设置。

**配置 HTTPS 代理服务器**  

```  
gradle.properties   

systemProp.https.proxyHost=www.somehost.org
systemProp.https.proxyPort=8080
systemProp.https.proxyUser=userid
systemProp.https.proxyPassword=password
systemProp.https.nonProxyHosts=*.nonproxyrepos.com|localhost   
```   

我们无法很好地概述所有可能的代理服务器设置。其中可以去看的一个地方是 Ant 项目的一个文件中的常量。这里是 SVN 的视图的链接。另一个地方是 JDK 文档的网络属性页。如果有人知道更好的概述，请发邮件让我们知道。

### NTLM 身份验证

如果您的代理服务器需要 NTLM 身份验证，您可能需要提供验证域，以及用户名和密码。有两种方法可以向 NTLM 代理提供验证域：

- 将 http.proxyUser 系统属性设置为一个这样的值：域/用户名。
- 通过 http.auth.ntlm.domain 系统属性提供验证域。