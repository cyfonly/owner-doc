#Welcome 
本文旨在对 owner 提供综合性的指导，包括基本功能、高级用法等主题，并且文中附有详细 demo。  

owner github：[https://github.com/lviggiano/owner](https://github.com/lviggiano/owner)  
英文文档地址：[http://owner.aeonbits.org/](http://owner.aeonbits.org/)  

#概述
owner 是个超轻量 java 库（jar包）， 旨在摒弃 properties 文件的样板代码，助你轻松管理项目配置： 
* 简便：无需加载多余代码，转换并管理 properties 配置文件，通过最简单的方式使用你的 properties
* 强大：基于注解，自动类型转换，变量扩展，加载策略，热加载，事件通知，等等
* 灵活：选择你需要的功能，屏蔽你不需要的；容易上手，具有丰富的文档 

#目录
- <a href="#owner">owner 是什么</a>
- <a href="#install">安装</a>
	+ <a href="#maven">maven</a>
	+ <a href="#java8">java 8</a>
- <a href="#baseuse">基本用法</a>
	+ <a href="#mapping">映射机制</a>
	+ <a href="#useconfig">使用 Config 对象</a>
	+ <a href="#useano">使用 @DefaultValue 和 @Key 注解</a>
	+ <a href="#notdefi">未定义属性</a>
- <a href="#feature">功能特性</a>
	+ <a href="#loadstra">加载策略</a>
	+ 引用属性
	+ 参数化属性
	+ 类型转换
	+ 变量扩展
	+ 加载和热加载
	+ 可访问性和可变性
	+ 程序调试
	+ 禁用功能
	+ 元配置
	+ XML支持
	+ 事件支持
	+ 单例

#<a id="owner">owner 是什么</a>
owner 是一个 java 库，旨在减少通过 properties 文件来处理应用程序配置的代码。它已经打包成一个 jar 文件，你也可以从 Maven 中央仓库下载。开发此 API 是受 GWT i18n 启迪（[点此查看](http://www.gwtproject.org/doc/latest/DevGuideI18nConstants.html)）。使用 GWT i18n 来加载配置文件有一个问题，那就是它仅仅适用于客户端代码（JavaScript），不是标准的 java 类。此外，GWT 是一个大的库，它是为了很多用途而设计，并不仅仅针对配置文件。  

#<a id="install">安装</a>
owner 是一个 java 库，即 jar 包。owner 可以在 Maven 仓库中获取，当然你也可以从这个连接下载它的库 jar 包、javadoc.jar、sources.jar 或者包含源码及 javadocs 的文件。下载好之后，你就需要配置你的 IDE 了。一般来说你只需要在 CLASSPATH 环境变量中引入它的 jar 包。

####<a id="maven">Maven</a>
在 Maven 项目中引用非常简单，只需要在项目 pom.xml 文件中添加以下依赖：
```
<dependencies>
　　<dependency>
　　　　<groupId>org.aeonbits.owner</groupId>
　　　　<artifactId>owner</artifactId>
　　　　<version>1.0.8</version>
　　</dependency>
</dependencies>  
```
如果有更新的版本，只需要将新版本号替换 1.0.8 即可。写这篇文章时最新版本就是 1.0.8，但在使用时你最好检查下是否有新版本。  
当前很多 IDE 都能很好的集成Maven，所以在 pom.xml 文件中增加了上述依赖并刷新项目后，你就可以使用 owner 了。

####<a id="java8">Java 8</a>
java 8 引入了一些新的语言特性，比如接口中的 default methods。这种情况下 artifactId  需要使用 owber-java8 ：
```
<dependencies>
　　<dependency>
　　　　<groupId>org.aeonbits.owner</groupId>
　　　　<artifactId>owner-java8</artifactId>
　　　　<version>1.0.6</version>
　　</dependency>
</dependencies>
```
由于 owner-java8 依赖于 owner，所以你不需要在 Maven 依赖中同时指定它们两个。

#<a id="baseuse">基本用法</a>
要使用 owner API，你需要先定义一个与 properties 文件相关联的 java 接口类。  
假设你定义的 properties 文件名字为 ServerConfig.properties：
```
port=80
hostname=foobar.com
maxThreads=100
```
你可以在同一个 package 下定义一个简单的 java 接口类 ServerConfig.java 来访问 properties 文件：
```
import org.aeonbits.owner.Config;
public interface ServerConfig extends Config {
　　int port();
　　String hostname();
　　@DefaultValue("42")
　　int maxThreads();
}
```
注意上面的接口要继承 Config，这是一个让 owner 正常工作的标记接口。由于设计这个接口的目的是把属性映射到一小段代码中，我们不妨把这个接口叫做“属性映射接口”或者“映射接口”。  
  
####<a id="mapping">映射机制</a>
由于 properties 文件和 java 接口类有相同的名字且都在同一个包下面，owner API 能够自动把两者关联起来。  
  
举个例子，假如你的映射接口叫做 com.foo.bar.ServerConfig，owner 会试着从类路径中把它关联到 com.foo.bar.ServerConfig.properties。同样，在 properties 文件中定义的属性名也会自动和 java 类中相同名字的方法相关联。比如属性文件中的属性 port 会和 java 类中的方法 int port()关联，属性 hostname 会和方法 String hostname() 关联。此外，关联类型转换将会自动进行，方法 port() 会返回一个 int 类型，方法 hostname() 会返回一个 String 类型。  
  
这种映射机制是完全可定制的，包括刚才介绍的自动类型转换也是足够灵活去覆盖大多数的 java 类型以及用户自定义类型。

####<a id="useconfig">使用 Config 对象</a>
现在，你可以创建一个ServerConfig 对象并且在代码中使用:
```
ServerConfig cfg = ConfigFactory.create(ServerConfig.class);
System.out.println("Server " + cfg.hostname() + ":" + cfg.port() + " will run " + cfg.maxThreads());
```
####<a id="useano">使用 @DefaultValue 和 @Key 注解</a>
你注意到了在上面的例子中指定了 @DefaultValue("42")  注解吗？
```
public interface ServerConfig extends Config {
　　int port();
　　String hostname();
　　@DefaultValue("42")    // 这里
　　int maxThreads();
}
```
它适用于 maxThread 不存在于 properties 文件中的情况。由于 maxThreads() 方法返回一个 int，这个注解会自动转换成 int。
使用注解时你可以定制属性的键名：
```
# ServerConfig.properties示例
server.http.port=80
server.host.name=foobar.com
server.max.threads=100
```
此时，跟java应用中常见的命名方式一样，属性的名字使用“.”进行分割。由于我们使用的是“server.http.port”而不是“port”,我们就需要使用 @Key 注解来关联属性名和方法名。
```
/*
 * 映射上面 properties 文件的 java 接口示例
 */
public interface ServerConfig extends Config {
　　@Key("server.http.port")
　　int port();
　　@Key("server.host.name")
　　String hostname();
　　@Key("server.max.threads");
　　@DefaultValue("42")
　　int maxThreads();
}
```
@DefaultValue 和 @Key 是使用 owner 的基础。使用 @DefaultValue 注解可以帮助你在开发过程中完全抛开属性文件，你可以在最后把配置文件加上或者把这步工作丢给最后一个开发者。

####<a id="notdefi">未定义属性</a>
假如你在映射接口中定义了一个不能与 properties 文件中任何一个属性相匹配的方法，并且这个方法没有使用 @DefaultValue 注解，这个时候会发生什么呢？比如你定义了ServerConfig类，如下：
```
public static interface ServerConfig extends Config {
　　String hostname();
　　int port();
　　Boolean debugEnabled();
}
```
假如没有任何 ServerConfig.properties 与之相关联，当你调用 String hostname() 方法是将会返回一个 null，调用 Boolean debugEnabled() 是也一样，因为 String 和 Boolean 都是 java 对象。但是，当我们调用 int port() 时，将会抛出 NullPointerException 异常。  
  
那么你不想要 NullPointerException 异常的话，该怎么办？很简单，你可以定义一个默认值，比如你可以给一个 int 返回类型的设置 @DefaultValue("0") ，给boolean返回类型的设置@DefaultValue("false") 等等……

#<a id="loadstra">加载策略</a>
properties 文件和映射接口的关联是通过 owner API 匹配类名和文件名（.properties）来实现的，当然这个逻辑是可以根通过一些额外的注解来实现用户个性化的需求。
```
@Sources({ "file:~/.myapp.config", 
　　　　"file:/etc/myapp.config", 
　　　　"classpath:foo/bar/baz.properties" })
public interface ServerConfig extends Config {
　　@Key("server.http.port")
　　int port();
　　@Key("server.host.name")
　　String hostname();
　　@Key("server.max.threads");
　　@DefaultValue("42")
　　int maxThreads();
}
```
在上面的例子中， owner 会尝试从不同的 @Sources 中加载属性：  
  
1. 首先，它会尝试从用户home目录的 ~/.myapp.config 加载属性文件，假如找到，这个文件将会被使用；  
2. 假如上一个失败，它就会尝试从 /etc/myapp.config 加载属性文件，假如找到，这个文件将会被使用；  
3. 作为最后的手段，它将尝试从类路径下以 foo/bar/baz.properties 标志的文件中加载属性；  
4. 假如以上 URL 资源都不存在，java 类将不会与任何文件相关联，只有 @DefaultValue 会被使用。假如没有默认值，将会返回null（就像 java.util.Properties）  
  
在上面的例子中，属性值只会从第一个被找到的文件中加载。一旦有匹配的文件被加载，其他的都会被忽略。这是使用了 @Sources 注解的默认加载方式，你也可以显式的在接口定义时使用 @LoadPolicy(LoadType.FIRST) 注解来指定。  
  
那么假如你需要在一些属性间做一些重载该怎么办？没问题，这完全是可能的，你可以通过 @LoadPolicy(LoadType.MERGE) 注解来实现：
```
@LoadPolicy(LoadType.MERGE)
@Sources({ "file:~/.myapp.config", 
　　　"file:/etc/myapp.config", 
　　　"classpath:foo/bar/baz.properties" })
public interface ServerConfig extends Config {
　　...
}
```
这种情况下，所有 URL 中指定的文件都将被查询到，并且第一个定义某重载属性的文件中的值将会被采纳。具体过程如下：  
  
1. 首先，它会从 ~/.myapp.config 中加载指定的属性，如果找到就将关联的属性值返回；  
2. 然后，从 /etc/myapp.config 中加载指定的属性，如果找到就将关联的属性值返回；  
3. 最后它会从 classpath 路径下的 foo/bar/baz.properties 中加载指定的属性，如果找到就将关联的属性值返回；  
4. 假如指定的属性未在上述任何文件中找到的话，它就会返回用 @DefaultValue 标注的值，否则返回null。  
  
因此基本上我们在多个 properties 文件中执行合并，且第一个properties 文件会重写后面的文件的相同属性值。  
  
@Sources注解通过语法 file:${user.home}/.myapp.config （通过'user.home' 系统属性得到解决）或者 file:${HOME}/.myapp.config （通过$HOME 环境变量得到解决）考虑系统属性或环境变量。前面例子中使用的“~”是另一个变量扩展的例子，它等同于 ${user.home}。  


  
  
  
  
  



#<a id="feature">功能特性</a>
> 简单的事情就应该是简单的，复杂的事情就应该是可能的。  
> 　　　　　　　　　　　　—— 天才计算机大师 艾伦.凯 

这也是开发 owner 的宗旨。  
owner是个功能丰富的 API，但在增加更多的功能之前，我们尽可能的保持它现有基本用法的简单化。 
owner API 支持一系列功能，如下：
* 加载策略
* 引用属性
* 参数化属性
* 类型转换
* 变量扩展
* 加载和热加载
* 可访问性和可变性
* 程序调试
* 禁用功能
* 元配置
* XML支持
* 事件支持
* 单例

新特性的开发不希望将已有特性变得复杂，版本的向后兼容也在我们的目标当中。
