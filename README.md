#Welcome 
本文旨在对 owner 提供综合性的指导，包括基本功能、高级用法等主题，并且文中附有详细 demo。  

owner github：[https://github.com/lviggiano/owner](https://github.com/lviggiano/owner)  
英文文档地址：[http://owner.aeonbits.org/](http://owner.aeonbits.org/)  
中文文档地址：[http://www.cnblogs.com/cyfonly/p/5523101.html](http://www.cnblogs.com/cyfonly/p/5523101.html)  
  

---  
####　　　　　　　　　　　作者：YunFeng Cheng  
　　　　　　　　　　owner 中文文档谨由本人维护，欢迎提出建议。  
  
    

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
	+ <a href="#import">引用属性</a>
	+ <a href="#param">参数化属性</a>
	+ <a href="#typeconvert">类型转换</a>
	+ <a href="#variextend">变量扩展</a>
	+ <a href="#reload">加载和热加载</a>
	+ <a href="#access">可访问性和可变性</a>
	+ <a href="#debug">程序调试</a>
	+ <a href="#forbit">禁用功能</a>
	+ <a href="#factory">配置工厂</a>
	+ <a href="#xml">XML支持</a>
	+ <a href="#event">事件支持</a>
	+ <a href="#singleton">单例</a>

#<a id="owner">owner 是什么</a>
owner 是一个 java 库，旨在减少通过 properties 文件来处理应用程序配置的代码。它已经打包成一个 jar 文件，你也可以从 Maven 中央仓库下载。开发此 API 是受 GWT i18n 启迪（[点此查看](http://www.gwtproject.org/doc/latest/DevGuideI18nConstants.html)）。使用 GWT i18n 来加载配置文件有一个问题，那就是它仅仅适用于客户端代码（JavaScript），不是标准的 java 类。此外，GWT 是一个大的库，它是为了很多用途而设计，并不仅仅针对配置文件。  

#<a id="install">安装</a>
owner 是一个 java 库，即 jar 包。owner 可以在 Maven 仓库中获取，当然你也可以从这个连接下载它的库 jar 包、javadoc.jar、sources.jar 或者包含源码及 javadocs 的文件。下载好之后，你就需要配置你的 IDE 了。一般来说你只需要在 CLASSPATH 环境变量中引入它的 jar 包。

###<a id="maven">Maven</a>
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

###<a id="java8">Java 8</a>
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
  
###<a id="mapping">映射机制</a>
由于 properties 文件和 java 接口类有相同的名字且都在同一个包下面，owner API 能够自动把两者关联起来。  
  
举个例子，假如你的映射接口叫做 com.foo.bar.ServerConfig，owner 会试着从类路径中把它关联到 com.foo.bar.ServerConfig.properties。同样，在 properties 文件中定义的属性名也会自动和 java 类中相同名字的方法相关联。比如属性文件中的属性 port 会和 java 类中的方法 int port()关联，属性 hostname 会和方法 String hostname() 关联。此外，关联类型转换将会自动进行，方法 port() 会返回一个 int 类型，方法 hostname() 会返回一个 String 类型。  
  
这种映射机制是完全可定制的，包括刚才介绍的自动类型转换也是足够灵活去覆盖大多数的 java 类型以及用户自定义类型。

###<a id="useconfig">使用 Config 对象</a>
现在，你可以创建一个ServerConfig 对象并且在代码中使用:
```
ServerConfig cfg = ConfigFactory.create(ServerConfig.class);
System.out.println("Server " + cfg.hostname() + ":" + cfg.port() + " will run " + cfg.maxThreads());
```
###<a id="useano">使用 @DefaultValue 和 @Key 注解</a>
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

###<a id="notdefi">未定义属性</a>
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
* 配置工厂
* XML支持
* 事件支持
* 单例

新特性的开发不希望将已有特性变得复杂，版本的向后兼容也在我们的目标当中。

###<a id="loadstra">加载策略</a>
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

###<a id="import">引用属性</a>
你可以使用另外的机制来加载属性到映射接口中，那就是在调用 ConfigFactory.create() 时人工指定一个属性对象：  
  
```
public interface ImportConfig extends Config {
　　@DefaultValue("apple")
　　String foo();
　　@DefaultValue("pear")
　　String bar();
　　@DefaultValue("orange")
　　String baz();
}
// 然后...
Properties props = new Properties();
props.setProperty("foo", "pineapple");
props.setProperty("bar", "lime");
ImportConfig cfg = ConfigFactory.create(ImportConfig.class, props); // 属性引用!
assertEquals("pineapple", cfg.foo());
assertEquals("lime", cfg.bar());
assertEquals("orange", cfg.baz());
```
  
当然你可以同时指定多个需要引用的属性：  
  
```
ImportConfig cfg = ConfigFactory.create(ImportConfig.class, props1, props2, ...);
```
  
假如 props1 和 props2 同时指定了同一个属性的值，那么首先指定的值将会被采用。  
  
```
Properties p1 = new Properties();
p1.setProperty("foo", "pineapple");
p1.setProperty("bar", "lime");
Properties p2 = new Properties();
p2.setProperty("bar", "grapefruit");
p2.setProperty("baz", "blackberry");
ImportConfig cfg = ConfigFactory.create(ImportConfig.class, p1, p2); // 属性引用!
assertEquals("pineapple", cfg.foo());
// p1先指定，所以这是 lime 而不是 grapefruit
assertEquals("lime", cfg.bar()); 
assertEquals("blackberry", cfg.baz());
```  
此外，你可以非常方便的引用系统属性或环境变量：  
  
```
interface SystemEnvProperties extends Config {
　　@Key("file.separator")
　　String fileSeparator();
　　@Key("java.home")
　　String javaHome();
　　@Key("HOME")
　　String home();
　　@Key("USER")
　　String user();
　　void list(PrintStream out);
}
SystemEnvProperties cfg = ConfigFactory.create(SystemEnvProperties.class, System.getProperties(), System.getenv());
assertEquals(File.separator, cfg.fileSeparator());
assertEquals(System.getProperty("java.home"), cfg.javaHome());
assertEquals(System.getenv().get("HOME"), cfg.home());
assertEquals(System.getenv().get("USER"), cfg.user());
```  

#####与加载策略的交互
上面讲到的“引用属性”是属性加载机制中的附加功能。通过代码引入的属性在优先级上要高于通过 @Sources 注释的方式。假设有这样一个场景，你已通过 @Sources 定义你的配置文件，但是你又想让用户在代码中自己指定配置文件，这个时候怎么办呢？  
  
```
@Sources(...)
interface MyConfig extends Config { 
　　...
}
public static void main(String[] args) {
　　MyConfig cfg;
　　if (args.lenght() > 0) {
　　　　Properties props = new Properties();
　　　　props.load(new FileInputStream(new File(args[0])));
　　　　cfg = ConfigFactory.create(MyConfig.class, userProps);
　　} else {
　　　　cfg = ConfigFactory.create(MyConfig.class);
　　}
}
```  
  
在上例中，用户指定的 properties 文件将会重写通过 @Sources 注解加载的 properties 文件中的同名属性。很多命令行工具会使用这种方式，它允许用户在命令行中重写默认配置。（这仅针对1.0.3.1及更高级的版本，在1.0.3.1版本之前引用属性优先级比从 properties 文件中加载的要低。这在1.0.3.1作出改变，并将在以后的版本中都采用这种方式）  
  
###<a id="param">参数化属性</a>
owner 另外一个杰出的特性，就是它允许在方法接口上提供参数。属性值应当遵循 java.util.Formatter 类指定的位置计数规则。  
  
```
public interface Sample extends Config {
　　@DefaultValue("Hello Mr. %s!")
　　String helloMr(String name);
}
Sample cfg = ConfigFactory.create(Sample.class);
print(cfg.helloMr("Luigi")); // will println 'Hello Mr. Luigi!'
```  
  
#####禁用参数扩展
owner 支持用户关闭参数扩展，这可以通过使用 @DisableFeature 注解来实现。  
  
```
public interface Sample extends Config {
　　@DisableFeature(PARAMETER_FORMATTING)
　　@DefaultValue("Hello %s.")    
　　public String hello(String name); 
　　// 将会忽略参数并返回"Hello %s."
}
```  
  
@DisableFeature 注解可以用在方法层面，也可以用在接口层面，当使用在接口层面时，将会适用于该接口下的所有方法。  
  
```
DisableFeature(PARAMETER_FORMATTING)
public interface Sample extends Config {
　　@DefaultValue("Hello %s.")    
　　public String hello(String name); 
　　// 将会忽略参数并返回"Hello %s."
}
```  
  
###<a id="typeconvert">类型转换</a>
owner API 支持原始类型和枚举类型的属性转换。当你定义映射接口时，你可以使用广泛的返回类型，并且他们会自动从 String 类型转换成原始类型或者枚举类型。  
  
```
// conversion happens from the value specified in the properties files (if available).
int maxThreads();
// conversion happens also from @DefaultValue
@DefaultValue("3.1415")
double pi();
// enum values are case sensitive! java.util.concurrent.TimeUnit is an enum
@DefaultValue("NANOSECONDS");
TimeUnit timeUnit();
```
  
你可以在配置接口中将业务对象定义为返回类型，甚至是你自定义的对象，最简单的方式就是使用一个带 String 参数的 public 构造参数来定义你的业务对象：  
  
```
public class CustomType {
　　private final String text;
　　public CustomType(String text) {
　　　　this.text = text;
　　}
　　public String getText() {
　　　　return text;
　　}
}
public interface SpecialTypes extends Config {
　　@DefaultValue("foobar.txt")
　　File sampleFile();
　　@DefaultValue("http://owner.aeonbits.org")
　　URL sampleURL();
　　@DefaultValue("example")
　　CustomType customType();
　　@DefaultValue("Hello %s!")
　　CustomType salutation(String name);
}
```  
  
owner API 会将"example"传递给 CustomType 的构造函数并返回。
  
#####数组和集合  
owner 具有对 java 数组和集合的第一级支持，因此你可以这样定义属性：  
  
```
public class MyConfig extends Config {
　　@DefaultValue("apple, pear, orange")
　　public String[] fruit();
　　@Separator(";")
　　@DefaultValue("0; 1; 1; 2; 3; 5; 8; 13; 21; 34; 55")
　　public int[] fibonacci();
　　@DefaultValue("1, 2, 3, 4")
　　List<Integer> ints();
　　@DefaultValue("http://aeonbits.org, http://github.com, http://google.com")
　　MyOwnCollection<URL> myBookmarks();
　　// Concrete class are allowed (in this case java.util.Stack)
　　// when type is not specified <String> is assumed as default
　　@DefaultValue("The Lord of the Rings,The Little Prince,The Da Vinci Code")
　　Stack books();
}
```  
  
你可以通过对接口明确指定 Collection、 List、 Set、 SortedSet 或者 Vector、 Stack、 LinkedList 等具体实现（甚至你自己实现的 java 集合框架接口，只要在实现类中定义一个默认无参的构造函数）来使用数组对象、原始类型或者 java 集合（但是要注意，并不支持 Map 接口及其子接口）。  
  
默认情况下 woner 使用逗号（","）来分割数组和集合中的元素，但是你可以通过 @Separator 注解来指定不同的字符。假如你的属性有更复杂的拆分逻辑，你可以通过 @TokenizerClass 注解加上 Tokenizer 接口来自定义断词器类。例如：  
  
```
public class MyConfig extends Config {
　　@Separator(";")
　　@DefaultValue("0; 1; 1; 2; 3; 5; 8; 13; 21; 34; 55")
　　public int[] fibonacci();
　　@TokenizerClass(CustomDashTokenizer.class)
　　@DefaultValue("foo-bar-baz")
　　public String[] withSeparatorClass();
}
public class CustomDashTokenizer implements Tokenizer {
　　// this logic can be as much complex as you need
　　@Override
　　public String[] tokens(String values) {
　　　　return values.split("-", -1);
　　}
}
```  
  
@Separator 和 @TokenizerClass 能够作用在方法层面和接口层面，当只在方法层面指定时只对该方法有效，当在接口层面上指定时则对整个接口类生效。方法层面上指定的注解会覆盖类层面上的注解：  
  
```
@Separator(";")
public interface ArrayExample extends Config {
　　// takes the class level @Separator
　　@DefaultValue("1; 2; 3; 4")
　　public int[] semicolonSeparated();
　　// overrides the class-level @Separator(";")
　　@Separator(",")
　　@DefaultValue("1, 2, 3, 4")
　　public int[] commaSeparated();
　　// overrides the class level @Separator(";")
　　@TokenizerClass(CustomDashTokenizer.class)
　　@DefaultValue("1-2-3-4")
　　public int[] dashSeparated();
}
```  
  
需要注意的是，不能在同一级别上同时使用 @Separator 和 @TokenizerClass，因为这两个注解实际上是在干同一件事！比如下面的例子将会抛出 UnsupportedOperationException 异常：  
  
```
// @Separator and @TokenizerClass cannot be used together
// on class level.
@TokenizerClass(CustomCommaTokenizer.class)
@Separator(",")
public interface Wrong extends Config {
　　// will throw UnsupportedOperationException!
　　@DefaultValue("1, 2, 3, 4")
　　public int[] commaSeparated();
}
public interface AlsoWrong extends Config {
　　// will throw UnsupportedOperationException!
　　// @Separator and @TokenizerClass cannot be
　　// used together on method level.
　　@Separator(";")
　　@TokenizerClass(CustomDashTokenizer.class)
　　@DefaultValue("0; 1; 1; 2; 3; 5; 8; 13; 21; 34; 55")
　　public int[] conflictingAnnotationsOnMethodLevel();
}
```  
  
然而，即使在类层面上有冲突，woner 却能够在方法层面上进行纠正：  
  
```
// @Separator and @TokenizerClass cannot be used together on class level.
@Separator(";")
@TokenizerClass(CustomDashTokenizer.class)
public interface WrongButItWorks extends Config {
　　// but this overrides the class level annotations
　　// hence it will work!
　　@Separator(";")
　　@DefaultValue("1, 2, 3, 4")
　　public int[] commaSeparated();
}
```  
  
当然，我们是不建议这么做的，毕竟这是一种错误的设置方法（可以理解为 owner 自身的 bug 吧）……  
  
#####@ConverterClass 注解  
owner 通过提供 @ConverterClass 注解，使用户可以通过实现 Converter 接口指定自定义的转换逻辑。  
  
```
interface MyConfig extends Config {
　　@DefaultValue("foobar.com:8080")
　　@ConverterClass(ServerConverter.class)
　　Server server();
　　@DefaultValue("google.com, yahoo.com:8080, owner.aeonbits.org:4000")
　　@ConverterClass(ServerConverter.class)
　　Server[] servers();
}
class Server {
　　private final String name;
　　private final Integer port;
　　public Server(String name, Integer port) {
　　　　this.name = name;
　　　　this.port = port;
　　}
}
public class ServerConverter implements Converter<Server> {
　　public Server convert(Method targetMethod, String text) {
　　　　String[] split = text.split(":", -1);
　　　　String name = split[0];
　　　　Integer port = 80;
　　　　if (split.length >= 2)
　　　　　　port = Integer.valueOf(split[1]);
　　　　return new Server(name, port);
　　}
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
Server s = cfg.server(); // will return a single server
Server[] ss = cfg.servers(); // it works also with collections
```
  
在上面的例子中，我们调用 servers() 并返回一个 Server 对象数组，ServerConverter 会被调用多次以转换每一个元素，并且在任何情况下 ServerConverter  都将针对单个元素工作。  
  
#####owner 支持的类型  
owner API支持的自动转换类型包括：  
  
1. 原始类型：boolean, byte, short, integer, long, float, double  
2. 枚举类型：注意转换是区分大小写的，FOO != foo  
3. java.lang.String  
4. java.net.URL, java.net.URI  
5. java.io.File：字符"~"将扩展到"user.home"系统属性  
6. java.lang.Class：这很有用，比如你想加载JDBC驱动……  
7. 任何声明了带一个 java.lang.String 参数的 public 构造函数的可实例化类  
8. 任何声明了带一个 java.lang.Object 参数的 public 构造函数的可实例化类  
9. 任何声明了 public static 方法 valueOf(java.lang.String) 且返回一个自身实例的类  
10. 任何可通过 @ConverterClass 注解实例化的对象  
11. 任何以上对象的集合：Set, List, SortedSet或者具体实现（比如 LinkedHashSet），但不支持 Map 和其子接口  
  
假如 owner API 无法映射你的业务对象，将会抛出 UnsupportedOperationException 异常，同时附带有用的描述来帮助你快速定位问题。  
   
你也可以通过使用这个 static 方法 PropertyEditorManager.registerEditor() 注册你自定义的 PropertyEditor 把文本属性转换成你的业务对象。请查看 PropertyEditorSupport ，它将对你实现一个 [PropertyEditor](http://docs.oracle.com/javase/7/docs/api/java/beans/PropertyEditorSupport.html) 很有帮助。  
  
###<a id="variextend">变量扩展</a>  
有时候从另外一个属性扩展到其他属性是很有用的，我们来看看下面的 properties 文件：  
  
```
story=The ${animal} jumped over the ${target}
animal=quick ${color} fox
target=${target.attribute} dog
target.attribute=lazy
color=brown
```  
  
其关联的映射接口为：  
  
```
public interface ConfigWithExpansion extends Config {
　　String story();
}
```  
  
然后属性 story 会被扩展为 The quick brown fox jumped over the lazy dog。  
  
使用注解时这依然有效，但是你需要在方法上指定每一个属性：  
  
```
public interface ConfigWithExpansion　extends Config {
　　@DefaultValue("The ${animal} jumped over the ${target}")
　　String story();
　　@DefaultValue("quick ${color} fox")
　　String animal();
　　@DefaultValue("${target.attribute} dog")
　　String target();
　　@Key("target.attribute")
　　@DefaultValue("lazy")
　　String targetAttribute();
　　@DefaultValue("brown")
　　String color();
}
ConfigWithExpansion conf = ConfigFactory.create(ConfigWithExpansion.class);
String story = conf.story();
```  
  
有时候你也许想要扩展系统属性或者环境变量，这可以通过引用来实现：  
  
```
public interface SystemPropertiesExample　extends Config {
　　@DefaultValue("Welcome: ${user.name}")
　　String welcomeString();
　　@DefaultValue("${TMPDIR}/tempFile.tmp")
　　File tempFile();
}
SystemPropertiesExample conf = ConfigFactory.create(SystemPropertiesExample.class,System.getProperties(),System.getenv());
String welcome = conf.welcomeString();
File temp = conf.tempFile();
```
  
######禁用属性扩展  
你可以使用 @DisableFeature 注解来禁用变量扩展特性：  
  
```
public interface Sample extends Config {
　　@DefaultValue("Earth")
　　String world();
　　@DisableFeature(VARIABLE_EXPANSION)
　　@DefaultValue("Hello ${world}.")
　　// will return the string "Hello ${world}."
　　String sayHello();
}
```  
  
同样，@DisableFeature 可以定义在接口和方法层面。当定义在接口层面上时，对接口下的所有方法都生效。  
  
#####@Key 变量扩展  
通常情况下，我们需要把完全一样的工程部署在测试环境，经过测试后才会部署到生产环境。在这个过程中，我们会使用部署工具来保证程序（包括配置信息）没有发生变化。这个工程包含所有的运行时所需配置，针对不同的运行环境（测试/生产），管理员或者应用自身需要选择不同的配置。配置会大大影响程序如何工作，所以它必须和其他所有源代码一样需要严格控制。  
  
在 v1.0.6 之后的版本，已经支持在 @Key 注释上使用变量扩展。这种方式能够很好的应对上述情况。我们在同一个配置文件中配置好所有环境的配置信息，让后让管理员或者应用自身来选择合适的配置。  
  
假如在你的 XML 配置文件中，你定义了三个不同环境：开发环境、测试环境和生产环境，如下：  
  
```
<servers>
　　　　<dev> <!-- development environment -->
　　　　<name>Development</name>
　　　　<hostname>devhost</hostname>
　　　　<port>6000</port>
　　　　<user>myuser1</user>
　　　　<password>mypass1</password>
　　</dev>
　　<uat> <!-- user acceptance test environment -->
　　　　<name>User Acceptance Test</name>
　　　　<hostname>uathost</hostname>
　　　　<port>60020</port>
　　　　<user>myuser2</user>
　　　　<password>mypass2</password>
　　</uat>
　　<prod> <!--  production environment -->
　　　　<name>Production</name>
　　　　<hostname>prod-host</hostname>
　　　　<port>600</port>
　　　　<user>prod-user</user>
　　　　<password>secret</password>
　　</prod>
</servers>
```
  
或者一个 properties 文件，如下：  
  
```
servers.dev.name=Development
servers.dev.hostname=devhost
servers.dev.port=6000
servers.dev.user=myuser1
servers.dev.password=mypass1

servers.uat.name=User Acceptance Test
servers.uat.hostname=uathost
servers.uat.port=60020
servers.uat.user=myuser2
servers.uat.password=mypass2

servers.prod.name=Production
servers.prod.hostname=prod-host
servers.prod.port=600
servers.prod.user=prod-user
servers.prod.password=secret
```
  
接下来你可以把你的映射接口定义为：  
  
```
@Sources("classpath:org/aeonbits/owner/variableexpansion/KeyExpansionExample.xml")
public interface ExpandsFromAnotherKey extends Config {
　　@DefaultValue("dev")
　　String env();
　　@Key("servers.${env}.name")
　　String name();
　　@Key("servers.${env}.hostname")
　　String hostname();
　　@Key("servers.${env}.port")
　　Integer port();
　　@Key("servers.${env}.user")
　　String user();
　　@Key("servers.${env}.password")
　　String password();
}
```
  
请注意，这个例子中，我把 env() 定义默认值为 "dev"，所以 ${env} 将会使用 "dev" 作为默认值。  
  
还有另外一种方法，你可以在创建 Config 对象时指定 ${env} 的值：  
  
```
Map myVars = new HashMap();
myVars.put("env", "uat"); // here!
ExpandsFromAnotherKey cfg = ConfigFactory.create(ExpandsFromAnotherKey.class, myVars); // here!
assertEquals("User Acceptance Test", cfg.name());
assertEquals("uathost", cfg.hostname());
assertEquals(new Integer(60020), cfg.port());
assertEquals("myuser2", cfg.user());
assertNull("mypass2", cfg.password());
```
  
通过这种方式，你就可以选择使用哪个环境的配置。  
  
###<a id="reload">重加载和热重载</a>  
owner 支持程序重加载，也支持自动热重载。实现热重载有两种方式：同步和异步。  
  
#####程序化重加载  
你可以手动要求配置对象执行加载，这可以通过 Reloadable 来实现：  
  
```
@Sources{...}
interface MyConfig extends Reloadable {
　　String someProperties();
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
cfg.reload();
```
  
cfg.reload() 会像对象初始化时一样重新加载所有属性，假如配置文件被修改过，重加载后的将会是最新的属性值。Reloadable 接口继承自 Config 。  
  
![](http://owner.aeonbits.org/img/config-hierarchy.png)  
  
#####自动热重载  
  
你可以指定在配置文件被修改的情况下执行自动重加载属性值。  
  
```
@HotReload
@Sources("file:foo/bar/baz.properties")
interface MyConfig extends Config {
　　@DefaultValue("localhost")
　　String serverName();
}
```  
  
你可以看到在上面的例子中我们在接口层面上指定了 @HotReload 注解。  
  
热重载只能作用在文件系统 URL 上，就是说你可以在这几种 URL 上让它工作：  
  
1. file:path/to/your.properties：  基于文件系统的URL  
2. jar:file:path/to/some.jar!/path/to/your.properties：  本地文件系统中包含properties文件的jar文件  
3. classpath:path/to/your.properties：  从 classpath 中加载的资源，通常情况下，应用程序会从基于文件系统的classpath下加载类和资源，因此这种方式几乎任何时候都有效
  
假如你不使用 @Sources 注解，owner 会尝试在同一个包下面寻找与类名相同的 properties 文件。  
  
#####@HotReload 注解  
@HotReload 可接受三个可选参数，其定义如下：  
  
```
@interface HotReload {
　　long value() default 5;
　　TimeUnit unit() default SECONDS;
　　HotReloadType type() default SYNC;
}
enum HotReloadType {
　　SYNC, ASYNC
}  
```
[更多细节请点此查看](http://owner.newinstance.it/latest/apidocs/org/aeonbits/owner/Config.HotReload.html)。
  
你可以指定热重载的时间间隔（通过value和unit），你也可以指定你需要的重载方式，下面是一些例子：  
  
```
// Using the default values:
// will check for MyConfig.properties file changes in classpath
// with interval of 5 seconds.
// It will use SYNC hot reload.
@HotReload
interface MyConfig extends Config { ... }
// Will check for file changes every 2 seconds.
// It will use SYNC hot reload.
@HotReload(2)
@Sources("file:foo/bar/baz.properties")
interface MyConfig extends Config { ... }
// Will check for file changes every 500 millis.
// It will use SYNC hot reload.
@HotReload(value=500, unit = TimeUnit.MILLISECONDS)
@Sources("file:foo/bar/baz.properties")
interface MyConfig extends Config { ... }
// Will use ASYNC reload type: will span a
// separate thread that will check for file
// changes every 5 seconds (default)
@HotReload(type=HotReloadType.ASYNC)
@Sources("file:foo/bar/baz.properties")
interface MyConfig extends Config { ... }
// Will use ASYNC reload type and will check every 2 seconds.
@HotReload(value=2, type=HotReloadType.ASYNC)
@Sources("file:foo/bar/baz.properties")
interface MyConfig extends Config { ... }
```  
  
文件的最后修改日期将被用来检测文件的更改。  
  
#####同步热重载  
同步热重载以这样的方式工作：每次你调用通过 ConfigFactory.create() 构造的 Config 对象的方法时，配置文件就会检查修改并重载文件。这就意味着，假如你在很长一段时间内都没有使用 Config 对象，在对应文件系统上就不会去检查，重载也不会发生。鉴于此，我们也可以称这种方法为“懒加载”，因为只有我们使用时才会发生。  
  
这种方式是使用 @HotReload 的默认工作方式，你也可以显示的声明其工作方式：  
  
```
@HotReload(type=HotReloadType.SYNC)
```  
  
#####异步热重载  
异步热载以这样的方式工作：在指定的时间间隔上执行一个单独的线程上的周期性任务来检查更新并重载。也就是说即使你不调用也会有重载发生。通过指定 type=ASYNC 来选择这种方式。  
  
```
@HotReload(type=HotReloadType.ASYNC)
```  
  
#####热加载示例  
在项目源码中有个热加载的例子：  
  
```
public class AutoReloadExample {
　　private static final String spec =　"file:target/test-resources/AutoReloadExample.properties";
　　private static File target;
　　@Sources(spec)
　　@HotReload(1)
　　interface AutoReloadConfig extends Config, Reloadable {
　　　　@DefaultValue("5")
　　　　Integer someValue();
　　}
　　static {
　　　　try {
　　　　　　target = new File(new URL(spec).getFile());
　　　　} catch (MalformedURLException e) {
　　　　　　e.printStackTrace();
　　　　}
　　}
　　public static void main(String[] args)
　　　　throws IOException, InterruptedException {save(target, new Properties() { {
　　　　　　setProperty("someValue", "10");
　　　　}});
　　　　AutoReloadConfig cfg =　ConfigFactory.create(AutoReloadConfig.class);
　　　　cfg.addReloadListener(new ReloadListener() {
　　　　　　　public void reloadPerformed(ReloadEvent event) {
　　　　　　　　　System.out.print( "\rReload intercepted at "+ new Date() + " \n");
　　　　　　　}
　　　　});
　　　　System.out.println("You can change the file "+ target.getAbsolutePath() +　" and see the changes reflected below");
　　　　int someValue = 0;
　　　　while (someValue >= 0) {
　　　　　　someValue = cfg.someValue();
　　　　　　System.out.print("\rsomeValue is: " + someValue + "\t\t\t\t");
　　　　　　Thread.sleep(500);
　　　}
　　}
}
```  
  
你要运行这个例子的话，需要执行以下步骤：  
  
```
# after downloading the sources in the directory 'owner'
$ cd owner
$ mvn clean compile test-compile
$ java -classpath \
       target/classes/:target/test-classes/ \
       org.aeonbits.owner.examples.AutoReloadExample
```  
  
然后可以更改程序所指示的文件，以查看所反映的更改和被截取的重载事件。  
  
###<a id="access">可访问性和可变性</a>  
  
默认情况下，owner 创建的对象是不可改变的且重视信息隐藏。这就意味着一点配置对象被创建它的属性就不能被改变了，并且不能被属性映射方法外的其他方式所访问。  
  
这些方式是设计 owner 时做的限制，但有时候用户会觉得这并非很好，因此这里提供了 Mutable 接口和 Accessible 接口。以下是Mutable 接口和 Accessible 接口的层次结构：  
  
![](http://owner.aeonbits.org/img/config-hierarchy.png)  
  
#####Mutable 接口  
Mutable 接口允许开发者在程序运行期间改变 Config 对象的属性。比如：  
  
```
interface MyConfig extends Mutable {
    @DefaultValue("18")
    public Integer minAge();
    public Integer maxAge();
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
// this comes from the @DefaultValue
assertEquals(Integer.valueOf(18), cfg.minAge());
// now we change the minAge to 21 using setProperty
String oldValue = cfg.setProperty("minAge", "21");
assertEquals("18", oldValue); // the old value was 18
assertEquals(Integer.valueOf(21), cfg.minAge()); // now is 21
// now we remove the minAge property
oldValue = cfg.removeProperty("minAge");
assertEquals("21", oldValue); // the old value is 21
assertNull(cfg.minAge()); // now is null
```  
  
上面例子中我们看到 setProperty 和 removeProperty 操作，Mutable 接口甚至提供了 clear() 、load(InputStream) 和 load(Reader) 方法，它实现了对 Config 对象内属性的完整写访问。  
  
#####Accessible 接口  
Mutable 接口实现了对 Config 对象内属性的完整写访问，Accessible  则允许读访问。  
  
```
interface MyConfig extends Accessible {
    @DefaultValue("Bohemian Rapsody - Queen")
    String favoriteSong();
    @Key("salutation.text")
    @DefaultValue("Good Morning")
    String salutation();
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
assertEquals("Good Morning", cfg.getProperty("salutation.text"));
// print all properties to a PrintWriter
cfg.list(System.out);
// saves properties to an OutputStream
File tmp = File.createTempFile("owner-", ".tmp");
cfg.store(new FileOutputStream(tmp), "no comments");
```
  
正如你看到的，Accessible  不局限于 getProperty() ，它还提供了 list()、store()等操作。  
  
###<a id="debug">调试</a>  
在 owner API 中我们保留了对 properties 文件的调试方式。  
  
####toString() 方法  
toString() 方法可以很方便的让我们看到 Config 对象的内容：  
  
```
interface MyConfig extends Config {
　　@Key("max.threads")
　　@DefaultValue("25")
　　int maxThreads();
　　@Key("max.folders")
　　@DefaultValue("99")
　　int maxFolders();
　　@Key("default.name")
　　@DefaultValue("untitled")
　　String defaultName();
}
public static void main(String[] args) {
　　MyConfig cfg = ConfigFactory.create(MyConfig.class);
　　System.out.println("cfg = " + cfg);
　　// output will be: 
　　// "cfg = {default.name=untitled, max.folders=99, max.threads=25}"
}
```  
  
####list() 方法  
在你的映射接口中选择性的定义以下方法能是你调试更简单。  
  
```
void list(PrintStream out);
void list(PrintWriter out);
```
  
你可以实现定义了以上两个方法的 Accessible  接口，或者手动增加。  
  
手动增加：  
```
public interface SampleConfig extends Config {
　　@Key("server.http.port")
　　@DefaultValue("80")
　　int httpPort();
　　void list(PrintStream out); // manually defined
}
ServerConfig cfg = ConfigFactory.create(ServerConfig.class);
cfg.list(System.out);
```
  
实现 Accessible  接口：  
```
public interface SampleConfig extends Accessible {
　　@Key("server.http.port")
　　@DefaultValue("80")
　　int httpPort();
}
ServerConfig cfg = ConfigFactory.create(ServerConfig.class);
cfg.list(System.out); // list() is defined in Accessible interface
```
  
###<a id="forbit">禁用功能</a>  
由于某些原因你可能想禁用 owner 的一些特性，比如你想自己实现变量扩展，你会想禁用 owner 自身提供的变量扩展特性。这可以通过 @DisabledFeature 注解来实现。  
  
@DisabledFeature 注解能同时指定多个特性，可以在接口层面和方法层面使用。  
  
```
// on class level...
@DisableFeature({VARIABLE_EXPANSION, PARAMETER_FORMATTING}) 
public interface SampleConfig extends Config {
    @DefaultValue("Earth")
    public String planet();
    // on method level...
    @DisableFeature({VARIABLE_EXPANSION, PARAMETER_FORMATTING}) 
    @DefaultValue("Hello %s, welcome on ${planet}!")
    public String hello(String name); 
}
```  
  
上面的例子中 String hello(String name) 将会返回 "Hello %s, welcome on ${planet}!"，忽略参数传递。  
  
###<a id="factory">配置工厂</a>  
由于大多数事情都是通过注解来完成，所以在 owner 中并没有太多配置。  
  
有人曾问过：我想在运行时确定资源位置，这可能吗？简单的回答是没有，即使有一些选择来解决这个限制。但是最后，我们同意增加一些配置属性来配置 owner 的 ConfigFactory。以下面的例子来说明：  
  
```
// notice ${mypath} here
@Sources("file:${mypath}/myconfig.properties");
interface MyConfig extends Config { ... }
MyConfig cfg = ConfigFactory.create(MyConfig.class);
```
  
变量 ${mypath} 应该是从系统属性或者环境变量中扩展而来（加入有定义的话）。然而这不够简便：环境变量是只读的，并且有些时候要改变系统属性并不容易，尤其是部署在共享 JVM 中的应用。  
  
因此， owner 提供一些与 ConfigFactory 相关的上下文属性。  
  
```
// notice ${mypath} here
@Sources("file:${mypath}/myconfig.properties");
interface MyConfig extends Config { ... }
// notice ${mypath} here
ConfigFactory.setProperty("mypath", "/foo/bar/baz");
MyConfig cfg = ConfigFactory.create(MyConfig.class);
```
  
当 create() 方法执行 "file:${mypath}/myconfig.properties" 时，它将会扩展为  "file:/foo/bar/baz/myconfig.properties"。  
  
需要注意的是，加入你修改了 ConfigFactory 中的值并想重新加载这个 Config 对象，这并不会生效，因为 Config 对象只是工作在这些属性的快照上。  
  
你完全可以在运行时定义配置文件的来源：  
  
````
// notice ${myurl} here
@Sources("${myurl}");
interface MyConfig extends Config { ... }
// notice ${myurl} here
ConfigFactory.setProperty("myurl", "http://somewhere.com/conf.properties");
MyConfig cfg = ConfigFactory.create(MyConfig.class);
```
  
###<a id="xml">XML支持</a>  
  
JDK 1.5之后 java 属性支持 XML 格式（通过 loadFromXML() 和 storeToXML()），而 owner 也没有理由不提供 XML 支持。事实上，owner 甚至做的更好。  
  
#####java XML 格式  
java.util.Properties 类支持 XML 格式如下：  
  
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
　　<comment>this is an example</comment>
　　<entry key="server.ssh.alive.interval">60</entry>
　　<entry key="server.ssh.address">127.0.0.1</entry>
　　<entry key="server.http.port">80</entry>
　　<entry key="server.http.hostname">localhost</entry>
　　<entry key="server.ssh.user">admin</entry>
　　<entry key="server.ssh.port">22</entry>
</properties>
```
  
有没有注意到上面的是非结构化的 XML 格式？  
  
owner 支持加载 XML 文件作为配置属性，但支持更加格式化的 XML 格式，这可以根据用户的个人喜好来定义。  
  
#####用户指定 XML格式  
假如你不喜欢 java 默认风格的 XML 格式，你可以自定义 XML 格式：  
  
```
<server>
　　<http port="80">
　　　　<hostname>localhost</hostname>
　　</http>
　　<ssh port="22">
　　　　<address>127.0.0.1</address>
　　　　<alive interval="60"/>
　　　　<user>admin</user>
　　</ssh>
</server>
```
  
对 owner而言，上面的例子相当于在前一节中列出的示例。XML 元素和 XML 属性会被转换成属性名，元素值和属性值会被转换成 properties 值。也就是说上面的配置相当于：  
  
```
server.http.port=80
server.http.hostname=localhost
server.ssh.port=22
server.ssh.address=127.0.0.1
server.ssh.alive.interval=60
server.ssh.user=admin
```
  
#####加载 XML  
  
owner 加载 XML 的方式和加载 properties 文件是一样的。加入你定义了一个映射接口 foo.bar.ServerConfig.java，只需在 classpath 下创建 foo.bar.ServerConfig.xml 文件；或者你指定 @Sources 注解指向 XML 文件的 RUL。owner 能够识别 .xml 后缀并从 XML 文件中加载配置。  
  
对应以上 XML 配置的映射接口如下：  
  
```
interface ServerConfig extends Config {

　　@Key("server.http.port")
　　int httpPort();

　　@Key("server.http.hostname")
　　String httpHostname();

　　@Key("server.ssh.port")
　　int sshPort();

　　@Key("server.ssh.address")
　　String sshAddress();

　　@Key("server.ssh.alive.interval")
　　int aliveInterval();

　　@Key("server.ssh.user")
　　String sshUser();
}
```
  
假如你想把一个 Config 对象存储到 XML 文件中，你需要了解 Accessible 接口中的 storeToXML 方法。  
  
###<a id="event">事件支持</a>  
  
热加载是个很酷的功能，然而如果能知道哪些属性值发生了变化将会更有趣，这样你就可以重新配置受影响的 services 即可。owner 实现了一个功能丰富的事件系统，使你了解什么时候执行了重加载，什么时候属性改变了，并且让你验证新的值是否适应你的应用要求，假如不符合，你就可以进行回滚这个属性（或整个属性集合）的变化。  
  
#####监听重载  
  
在重载和热加载那个章节就说过，owner 支持程序加载和自动加载。两种情况下都会发生加载事件。  
  
当加载事件发生时如果你想得到通知，就要继承 Reloadable，它暴露了一些注册 ReloadListener 的方法。你也可以实现 TransactionalReloadListener，它会在加载生效之前通知你，这样你就可以检查哪些属性发生了变化，并在这些变化不适用于你的程序时进行回滚。  
  
![](http://owner.aeonbits.org/img/reload-event.png)  
  
Reloadable 接口允许增加或移除 ReloadListener 对象。  
  
ReloadListener 接口定义了一个 reloadPerformed() 方法来接收 ReloadEvent，ReloadEvent 包括了所有 oldProperties、newProperties 和一个 PropertyChangeEvent （每个变化的属性对应一个）列表，PropertyChangeEvent 则包含 propertyName、oldValue 和 newValue。  
  
当我们调用 Reloadable.addReloadListener() 方法时，它会传递一个 TransactionalReloadListener 实例，TransactionalReloadListener 继承了 ReloadListener 并增加了一个 beforeReload() 方法。这个监听器在加载已经触发但改变生效之间收到通知信息。这种情况下监听器就可以检查发生的变化及是否接收变化，它能触发 RollbackBatchException 异常，此时所有变化都将无效，保持当前值。当 RollbackBatchException  被抛出后 ，由于监听器终止了加载所以 ReloadListener.reloadPerformed() 将不会被调用。  
  
举个例子：  
  
```
@Sources("Example.properties")
interface MyConfig extends Reloadable {
　　@DefaultValue("5")
　　Integer someInteger();
　　@DefaultValue("foobar")
　　String someString();
　　@DefaultValue("3.14")
　　Double someDouble();
　　String nullsByDefault();
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
final boolean[] reloadPerformed = new boolean[] {false};
cfg.addReloadListener(new TransactionalReloadListener() {
　　public void beforeReload(ReloadEvent event)　throws RollbackBatchException {
　　　　String notAllowedValue = "42";
　　　　String newSomeInteger = event.getNewProperties().getProperty("someInteger");
　　　　// 42 makes the reload to rollback completely!
　　　　if (notAllowedValue.equals(newSomeInteger))
　　　　　　throw new RollbackBatchException("42 is not allowed for property 'someInteger'");
　　}
    public void reloadPerformed(ReloadEvent event) {
　　　　reloadPerformed[0] = true;
　　}
});
// we update the properties file in the filesystem
File target = new File("Example.properties");
save(target, new Properties() { {
　　setProperty("someInteger", "41");
　　setProperty("someString", "bazbar");
　　setProperty("someDouble", "2.718");
　　setProperty("nullsByDefault", "NotNullNow");
}});
cfg.reload();
// reload happened
assertTrue(reloadPerformed[0]);
assertEquals(new Integer(41), cfg.someInteger());
assertEquals("bazbar", cfg.someString());
assertEquals(new Double("2.718"), cfg.someDouble());
assertNotNull(cfg.nullsByDefault());
reloadPerformed[0] = false; // reset the flag
save(target, new Properties() { {
　　setProperty("someInteger", "42"); // not allowed!
　　setProperty("someString", "blahblah");
　　setProperty("someDouble", "1.234");
}});
cfg.reload();
// reload was rolled back since 42 is not allowed
assertFalse(reloadPerformed[0]);
assertEquals(new Integer(41), cfg.someInteger());
assertEquals("bazbar", cfg.someString());
assertEquals(new Double("2.718"), cfg.someDouble());
assertNotNull(cfg.nullsByDefault());
```
  
#####监听属性改变  
  
当用户通过 Mutable 接口中声明的方法改变属性、或者重加载发生时PropertyChangeEvent 就会发生。要在属性改变事件发生时获得通知，你的接口必须继承暴露了注册 PropertyChangeListener 方法的 Mutable。 你也可以实现 TransactionalPropertyChangeListener，它会在属性改变生效之前通知你，这样你就可以检查哪些属性发生了变化，并在这些变化不适用于你的程序时进行单个回滚或者全部回滚。  
  
![](http://owner.aeonbits.org/img/propertychange-event.png)  
  
Mutable 接口允许增加或移除 PropertyChangeListener对象。你可以增加一个监听器指定属性名，这样这个监听器就会和某个属性绑定。  
  
```
public interface Mutable extends Config {
　　void addPropertyChangeListener(String propertyName, PropertyChangeListener listener);
　　void addPropertyChangeListener(PropertyChangeListener listener);
　　void removePropertyChangeListener(PropertyChangeListener listener);
　　// ...the rest of the interface is cut...
}
```  
  
Mutable 接口定义了改变属性值的方法，这些方法会触发一个  PropertyChangeEvent 事件并通知注册的监听器。  
  
```
public interface Mutable extends Config {
　　String setProperty(String key, String value);
　　String removeProperty(String key);
　　void clear();
　　void load(InputStream inStream) throws IOException;
　　void load(Reader reader) throws IOException;
　　// ...the rest of the interface is cut...
}
```
  
PropertyChangeListener 接口来源于 java.beans 包，它定义了一个 propertyChange() 方法来接收 PropertyChangeEvent，PropertyChangeEvent 包含了 propertyName、oldValue 和 newValue。  
  
当我们调用 Mutable.addPropertyChangeListener() 方法时，它会传递一个 TransactionalPropertyChangeListener实例，TransactionalPropertyChangeListener 继承了 PropertyChangeListener 并增加了一个 beforePropertyChange() 方法。这个监听器在加载已经触发但改变生效之间收到通知信息。这种情况下监听器就可以检查发生的变化及是否接收变化，它能触发 RollbackOperationException 和 RollbackBatchException 异常。  
  
当抛出 RollbackOperationException 异常时，监听器将执行单个属性改变的回滚，即便这个属性改变已经被一个会导致很多属性改变的事件所触发，它也仅仅希望单个属性改变被回滚。相反，RollbackBatchException 异常意味着所有改变都将回滚并保留原值。  
  
当以上两个异常中的任何一个被抛出，由于监听器终止了改变所以 PropertyChangeListener.propertyChange() 将不会被调用。  
  
显然，在热加载过程中或者通过 Reloadable 接口调用 的 reload() 操作会触发 PropertyChangeEvent. 事件。举个例子：  
  
```
@Sources("Example.properties")
interface MyConfig extends Reloadable {
　　@DefaultValue("5")
　　Integer someInteger();
　　@DefaultValue("foobar")
　　String someString();
　　@DefaultValue("3.14")
　　Double someDouble();
　　String nullsByDefault();
}
MyConfig cfg = ConfigFactory.create(MyConfig.class);
final boolean[] reloadPerformed = new boolean[] {false};
cfg.addPropertyChangeListener("someInteger",
　　　　new TransactionalPropertyChangeListener() {
　　public void beforePropertyChange(PropertyChangeEvent event)
　　　　　　throws RollbackOperationException, RollbackBatchException {
　　　　String notAllowedValue = "88";
　　　　String makesEverythingToRollback = "42";
　　　　String newSomeInteger = (String)event.getNewValue();
　　　　if (notAllowedValue.equals(newSomeInteger))
　　　　　　throw new RollbackOperationException("88 is not allowed for property 'someInteger', " +"the single property someInteger is rolled back");
　　　　if (makesEverythingToRollback.equals(newSomeInteger))
　　　　　　throw new RollbackBatchException("42 is not allowed for property 'someInteger', " +　"the whole event is rolled back");
　　}
　　public void propertyChange(PropertyChangeEvent evt) {
　　　　reloadPerformed[0] = true;
　　}
});
save(target, new Properties() { {
　　setProperty("someInteger", "41");
　　setProperty("someString", "bazbar");
　　setProperty("someDouble", "2.718");
　　setProperty("nullsByDefault", "NotNullNow");
}});
cfg.reload();
assertTrue(reloadPerformed[0]);
assertEquals(new Integer(41), cfg.someInteger());
assertEquals("bazbar", cfg.someString());
assertEquals(new Double("2.718"), cfg.someDouble());
assertNotNull(cfg.nullsByDefault());
reloadPerformed[0] = false;
cfg.setProperty("someInteger", "55");
assertTrue(reloadPerformed[0]);
assertEquals(new Integer(55), cfg.someInteger());
reloadPerformed[0] = false;
cfg.setProperty("someInteger", "88");
// 88 is rolled back.
assertFalse(reloadPerformed[0]);
assertEquals(new Integer(55), cfg.someInteger());
reloadPerformed[0] = false;
save(target, new Properties() { {
　　setProperty("someInteger", "42");
　　setProperty("someString", "blahblah");
　　setProperty("someDouble", "1.234");
}});
cfg.reload();
assertFalse(reloadPerformed[0]);
assertEquals(new Integer(55), cfg.someInteger());
assertEquals("bazbar", cfg.someString());
assertEquals(new Double("2.718"), cfg.someDouble());
assertNotNull(cfg.nullsByDefault());
reloadPerformed[0] = false;
save(target, new Properties() { {
　　setProperty("someInteger", "88");
　　setProperty("someString", "this is not rolled back");
　　setProperty("someDouble", "1.2345");
}});
cfg.reload();
assertFalse(reloadPerformed[0]);
// only someInteger=88 is rolled back
assertEquals(new Integer(55), cfg.someInteger());
assertEquals("this is not rolled back", cfg.someString());
assertEquals(new Double("1.2345"), cfg.someDouble());
assertNull(cfg.nullsByDefault());
```
  
以上所讲的事件通知机制允许用户进行一些基本校验，将来会提供一个更简单、更强大的校验机制，而事件机制将会其底层支撑。  
  
###<a id="singleton">单例</a>  
  
把配置设计成单例对于应用程序来说是很有意义的。  
  
绝大多数情况下，你会采用以下方式来实例化 Config 对象：  
  
```
MyConfig cfg = ConfigFactory.create(MyConfig.class);
```
  
人们倾向于这样使用：  
  
```
class MyApp {
　　private static MyConfig cfg =ConfigFactory.create(MyConfig.class);

　　public void doSomething() {
　　　　UserInterface ui = new UserInterface(cfg);
　　　　Model model = new Model(cfg);
　　　　ui.setModel(model);
　　　　// do something more with cfg...
　　}
}
```
  
这样做的问题在于，在复杂的应用程序内部传递 cfg 对象并不实效，并且当你在多个地方调用 ConfigFactory.create() 时将会创建多个 cfg 对象实例。这并不是你想要的。  
  
几个例子，假如你有一个 J2EE WEB 项目，在你的 servlets 里有个 Config 对象，你需要在 web.xml 中配置一个 ServletContextListener 并且把配置文件对象对象绑定到 ServletContext，然后在 servlets 中获取 cfg 对象。  
  
示例：  
```
public class MyServletContextListener
　　implements ServletContextListener {

　　public void contextInitialized(ServletContextEvent sce) {
　　　　MyConfig cfg = ConfigFactory.create(MyConfig.class);
　　　　sce.getServletContext().setAttribute("com.acme.foo.bar.MyConfig", cfg);
　　}

　　public void contextDestroyed(ServletContextEvent sce) {
　　　　sce.getServletContext().removeAttribute("com.acme.foo.bar.MyConfig");
　　}
}

// then in your servlets

public class MyServlet extends HttpServlet {
　　private MyConfig cfg = null;

　　public void init(ServletConfig config)　throws ServletException {
　　　　cfg = (MyConfig)config.getServletContext().getAttribute("com.acme.foo.bar.MyConfig");
    }

　　public void destroy() {
　　　　cfg = null;
　　}

　　protected void doGet(HttpServletRequest req,HttpServletResponse resp)
　　　　throws ServletException, IOException {

　　　　// do something with cfg;
    }
}
```
  
我并不喜欢上面的例子，因为这是一种依赖注入机制（你并不需要一个框架来实现 IoC）并且我常通过单例来实现。然而，这依然会有大量的代码，样板代码，而这正式 owner 所摒弃的。  
  
因此，owner 提供了一种单例模式，我叫它 ConfigCache，因为它并不仅仅是个单例。  
  
#####ConfigCache  
  
对于 ConfigFactory 我想大家现在都很熟悉了：  
  
```
MyConfig instance = ConfigFactory.create(MyConfig.class);
```
  
现在你可以用同样的方式使用 ConfigCache：  
  
```
MyConfig instance = ConfigCache.getOrCreate(MyConfig.class);
```
  
它们的区别在于，每次使用 ConfigFactory 时都会创建一个新的 MyConfig 实例，但使用 ConfigCache 时，实例是从内部缓存中返回的。  
  
```
MyConfig firstFromFactory = ConfigFactory.create(MyConfig.class);
MyConfig secondFromFactory = ConfigFactory.create(MyConfig.class);
// firstFromFactory not same as secondFromFactory

MyConfig firstFromCache = ConfigCache.getOrCreate(MyConfig.class);
MyConfig secondFromCache = ConfigCache.getOrCreate(MyConfig.class);
// firstFromCache same as secondFromCache
```
  
你可以给每个实例分配id：  
  
```
MyConfig firstFromCache = ConfigCache.getOrCreate("foo", MyConfig.class);
MyConfig secondFromCache = ConfigCache.getOrCreate("foo", MyConfig.class);
MyConfig thirdFromCache = ConfigCache.getOrCreate("bar", MyConfig.class);
// firstFromCache same as secondFromCache
// thirdFromCache not same as secodFromCache or firstFromCache
```
  
id 是 java.lang.Object 类型，但是你可以使用 String 来作为 id，比如上面的名字。  
  
至于 ConfigFactory，你可以传递一个引用列表给 ConfigCache。事实上 ConfigCache 和 ConfigFactory 非常相似：  
  
```
public final class org.aeonbits.owner.ConfigCache {
　　public static <T extends Config> T getOrCreate(Class<? extends T> clazz, Map<?, ?>... imports);
　　public static <T extends Config> T getOrCreate(Factory factory, Class<? extends T> clazz, Map<?, ?>... imports);
　　public static <T extends Config> T getOrCreate(Object id, Class<? extends T> clazz, Map<?, ?>... imports);
　　public static <T extends Config> T getOrCreate(Factory factory, Object id, Class<? extends T> clazz, Map<?, ?>... imports);
　　public static <T extends Config> T get(Object id);
　　public static <T extends Config> T add(Object id, T config);
　　public static void clear();
　　public static <T extends Config> T remove(Object id);
}
```
  
ConfigCache 是线程安全的，你无需担心并发访问问题。  
  
