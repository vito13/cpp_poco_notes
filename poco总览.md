# 介绍

POCO C++库是一组开源C++类库的集合，它们简化及加速了用C++来开发以网络功能为核心的可移植程序的过程。这些库，完美地与C++标准库结合到一起，并且填补了它所留下的那些空缺。它们具有模块化、高效的设计与实现，使得POCO C++库特别适合于进行嵌入式开发。而这是C++编程语言正在变得越来越流行的领域，因为，它既能进行底层(设备I/O、中断处理，等等)的开发，也能进行高级的面向对象的开发。当然，POCO也已经准备好面对企业级开发的挑战了。 


POCO由4个核心库及若干个附加库组成。核心库是：Foundation、XML、Util和Net。其中的两个附加库是：NetSSL，为Net 库中的网络类提供SSL 支持；Data，用来以统一的形式访问不同的SQL 数据库。POCO致力于进行以网络功能为核心的跨平台C++软件的开发，可以类比于Ruby on Rails对于Web开发的作用——一个功能强大而又简单易用的平台，用来构建妳自己的应用程序。POCO是严格围绕标准ANSI/ISO C++来开发的，并且支持标准库。贡献者们努力地达到以下要素之间的平衡：使用高级的C++特性；保持那些类的可理解性；保持代码的干净、一致及易维护性。

# Foundation库

Foundation 库是 POCO 的心脏。 它包含着：底层平台 的抽象层；常用 的辅助类和函数。 Foundation 库包含：定义 了固定尺寸整数的类型； 将整数在不同的字节序之间转换的函数； 一个 Poco::Any 类(基于 boost::Any )；用于错误处理 及调试的工具，包括各种各样的异常 类及断言支持。 还有： 一些用于内存管理的类，包括基于引用计数 的智能指针，以及一些用于缓冲区管理的类，还有内存池。对于字符串处理 ， POCO包含 了 一些函数，可做以下事情 ：修剪字符串 ；进行大小 写不敏感的比较；进行大小写转换。 还利用一些类提供了基本的 Unicode文字支持 ， 这些类能够将文字 在不同的字符编码之间转换，包括 UTF-8 和 UTF-16 。 还提供了 对于数字 的格式化和解析功能的支持，包括 一个类型安全的sprintf 变种。 还提供了以著名的PCRE 库( http://www.pcre.org )为基础的正则表达式支持。

POCO提供 了一些类，用于处理多种变种中的日期及时间。对于文件系统访问功能 ， POCO提供 了 Poco::File 和 Poco::Path 类，以及 Poco::DirectoryIterator 类。 在狠多程序中，某个部分需要向其它部分告知某些事件发生了 。 POCO提供 了 Poco::NotificationCenter 、 Poco::NotificationQueue 和事件 (类似 于 C#事件) 来简化这个过程。 以下示例展示了 POCO事件 的用法。 在这个示例中， 类 Source 拥有 一个公有事件，名为 theEvent ， 它有一个参数，类型为 int 。订阅 者可以通过调用 operator += 来订阅，调用 operator -= 来取消订阅，并且 要传入两个参数：指向某个对象 的一个指针；以及，指向某个成员函数 的指针。事件 可通用调用 operator () 来发射， 在 Source::fireEvent() 中就是这么做的。 

POCO 中的那些流式操作类，已经介绍过了。 为了对它们进行增强，还提供了 Poco::BinaryReader 和 Poco::BinaryWriter ，用于从流中读取及写入二进制数据，并且会自动、透明地处理字节序问题。

在复杂的多线程程序中，找出问题及缺陷的唯一手段就是输出详尽的日志信息。POCO提供了一个功能强大且可扩展的日志框架，它支持：过滤；路由到不同的频道；以及，日志消息的格式化。日志信息可被写入到以下目标：终端；文件；syslog守护进程；或者，发送到网络。如果POCO 提供的这些频道还不能满足妳，那么，还可以轻易地使用新的类来扩展日志框架。

对于 在运行时 载入( 及卸载 )共享 库的任务， POCO提供 了一个低层的 Poco::SharedLibrary 类。 在它之上，是 Poco::ClassLoader 类模板，以及对应的支持框架，使 得妳可以在运行时动态地载入及卸载 C++ 类，这就类似于 Java 和 .NET 中的功能。 类载入器框架，也使得， 妳可以轻易地以平台无关的方式来 为程序加入插件支持功能。

最后 ， POCO Foundation 中包含了对于多线程编程的不同层次的抽象。 有 Poco::Thread 类，以及常用的同步原语 ( Poco::Mutex 、 Poco::ScopedLock 、 Poco::Event 、 Poco::Semaphore 、 Poco::RWLock ) ， 一个 Poco::ThreadPool 类， 以及对于线程本地存储的支持， 也有高级的抽象，例如活跃对象（active objects）。简单 来说，活跃对象，指的就是， 它有某些方法，是在独立的线程中执行的。 这样，就可以进行异步 的成员函数调用——调用 一个成员函数， 在它正在执行的过程中， 去干一些其它的事，最后 ，获取 该函数的返回值。下面 的示例中展示了在POCO 中如何做到这一点。 在 ActiveAdder 类定义了一个活跃方法（active method） add() ， 该方法是由 addImpl() 这个成员函数来实现的。 在 main() 中调用该活跃方法，就会产生出一个 Poco::ActiveResult  ( 也被称作未来对象（ future ） )，最终会通过它来获取到该函数的返回值。 

# Foundation库组成
Foundation库是POCO库集中的一个，提供了编程时的一些常用抽象。在程序中被分成了18个部分，分别是：

1）Core

这部分除了建立跨平台库的基础头文件外，最有意义的部分是分装了原子计数的基本类（AtomicCounter），以及垃圾收集的一些类，如AutoPtr，SharedPtr。

2）Cache

顾名思义，内存Cache

3）Crypt

数字摘要

4）DateTime

时间

5）Events

分装了事件

6）Filesystem

文件系统，主要是对文件本身的操作，如移动，拷贝文件等

7）Hashing

Hash表

8）Logging

日志系统

9）Notifications

通知

10）Processes

进程通讯

11）RegularExpression

正则表达式，依赖于PCRE库.(http://www.pcre.org)

12）SharedLibrary

文件和类的动态实时加载

13）Streams

流

14）Tasks

任务

15）Text

文本装换

16）Threading

多线程

17）URI

URI操作

18）UUID

UUID生成和操作


在这18个模块中，Core、Events、Notifications、Processes、Tasks、Threading这几个模块应用时，对于创建整体程序架构的影响非常大，基本上可以决定了一个应用程序的复杂度，合理的应用这些模块可以使应用程序松耦合。其余的一些模块对应用整体结构影响不大，带来的都是一些局部的影响。

在看POCO库的时候经常觉得它的类写得好，内聚性非常强，耦合性很低。这个和它整体结构的合理性确实也是有一定关系的。 

# XML库

POCO XML 库提供了对于XML 的读取、处理和输出功能。出于POCO 的某个指导原则方面的考虑——不要尝试重新发明那些已经有用的东西—— POCO 的 XML 库支持工业标准的 SAX (版本2) 和 DOM接口 ， 这会令狠多用过XML 的 开发者 倍感亲切。 SAX ，即为XML的简单API（ Simple API for XML ( http://www.saxproject.org ) ），定义 了一个基于事件的接口，用于读取 XML 。 基于 SAX 的 XML解析 器，会遍历整个 XML文档 ，并且在遇到元素 、字符数据或其它XML 事物时通知应用程序。 SAX解析 器不需要将整个 XML文档 读入到内存中，因此 ， 可用来高效地解析巨型XML 文件。 反过来， DOM (文档对象模型 （ Document Object Model,  http://www.w3.org/DOM/ ）) ，使得应用程序能够 以一个树型风格的对象层次来完整地访问XML 文档。 为了完成工作，POCO 提供的 DOM解析 器必须将整个文档载入到内存中。 为了减少DOM 文档的内存占用， POCO 中的 DOM实现代码 用上了字符串池，使得 经常出现的字符串 （例如元素和属性名字） 只会被存储一次。 这个 XML 库基于 Expat开源XML解析 库 ( http://www.libexpat.org )开发 。 以 Expat 为基础的是 SAX接口 ，而以 SAX接口 为基础的就是 DOM实现 。对于字符串 ， XML 库使用的是 std::string ，并且 以 UTF-8 作为字符编码。 这就使得 XML 库可轻易地与程序中的其它部分配套使用。 在未来的版本中将支持XPath 和XSLT。
Util库

Util库的名字可能会让妳产生误解，实际上，它主要是一个用来创建命令行程序和服务器程序的框架。包含的功能：处理命令行参数(验证、绑定到配置属性，等等)；以及，管理配置信息。支持不同的配置文件格式——Java风格的属性文件、XML文件。

对于服务器程序，这个框架提供了对于Unix 守护进程的透明支持。当然，所有的服务器程序也都可以直接从命令行启动，这样便于测试及调试。
Net库

POCO的Net库使得妳能够轻易地写出基于网络通信的应用程序。无论妳只是想使用普通的TCP 套接字来发送数据，还是想要一个内置的完整功能的HTTP 服务器，Net 库都能满足妳。

在最底层， Net 库提供了一些套接字类 ，支持： TCP 流；服务器套接字； UDP 套接字；多播套接字； ICMP ；和原始套接字。如果 妳的程序中需要用到安全的套接字，那么，请使用 NetSSL 库，它是利用OpenSSL ( http://www.openssl.org )实现的。 以这些套接字类为基础，提供了两个用来构建TCP 服务器的框架—— 一个用来构建多线程服务器 (对于每个连接 都使用一个线程，该线程取自一个线程池 ) ，一个用来构建接收 者-响应者（ Acceptor-Reactor ） 模式的服务器。 多线程的 Poco::Net::TCPServer 类和它的支持框架，同时也是POCO 的HTTP 服务器实现( Poco::Net::HTTPServer )的基础。 在客户端开 发方面， Net 库提供了起到以下作用的类： 与 HTTP服务器通信 ；使用FTP 协议 发送及接收文件；使用SMTP 协议 发送邮件 (包含附件) ； 从POP3 服务器接收邮件。
将这些东西组合到一起

下面 的示例，展示了利用POCO 库实现的一个简单的HTTP 服务器。 这个服务器会返回一个 HTML文档 ，里面显示的是当前日期和时间。 在这里，用上了应用程序框架， 以开发出一个可以Unix 守护进程的形式运行的服务器程序。当前 ， 这个程序也可以在终端中直接启动。 在使用HTTP 服务器框架的过程中，定义 了 TimeRequestHandler 这个类， 它返回一个包含当前日期及时间的HTML 文档，以对来自客户端的请求进行响应。 同时，对于所接收到的每个请求， 都会利用日志框架输出一条日志信息。 为了与 TimeRequestHandler 类配套使用，还需要一个工厂类 ， TimeRequestHandlerFactory ； 该工厂类的一个实例会被传递给 HTTP服务器对象 。 HTTPTimeServer 这个应用程序类，定义了一个名为help 的命令行参数，具 体做法就是覆盖 了 Poco::Util::ServerApplication 的 defineOptions() 成员函数。另外 ， 在启动HTTP 服务器之前， 还会在 main() 中(通过 initialize() )读取默认的程序配置文件，并且取得某些配置属性的值。 