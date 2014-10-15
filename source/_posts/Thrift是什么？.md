title: Thrift是什么？
tags:
  - 拓展
date: 2011-08-17 16:50:35
---

<span style="color: #008000;">**来自于：http://blog.csdn.net/amuseme_lu/article/details/6261719**</span>

### Thrift  是什么？

Thrift源于大名鼎鼎的facebook之手，在2007年facebook提交Apache基金会将Thrift作为一个开源项目，对于当时的facebook来说创造thrift是为了解决facebook系统中各系统间大数据量的传 输通信以及系统之间语言环境不同需要跨平台的特性。所以thrift可以支持多种程序语言，例如:  C++, C#, Cocoa, Erlang, Haskell, Java, Ocami, Perl, PHP, Python, Ruby, Smalltalk. 在多种不同的语言之间通信thrift可以作为二进制的高性能的通讯中间件，支持数据(对象)序列化和多种类型的RPC服务。Thrift适用于程序对程 序静态的数据交换，需要先确定好他的数据结构，他是完全静态化的，当数据结构发生变化时，必须重新编辑IDL文件，代码生成，再编译载入的流程，跟其他IDL工具相比较可以视为是Thrift的弱项，Thrift适用于搭建大型数据交换及存储的通用工具，对于大型系统中的内部数据传输相对于JSON和xml无论在性能、传输大小上有明显的优势。

Thrift是IDL(interface definition language)描述性语言的一个具体实现，关于IDL的话题我们可以追溯到CORBA盛行1999-2001年(Common Object Request Broker Architecture/公用对象请求代理体系结构)，在 IDL 中我们似乎不会忘记到这几个关键字：module、interface、string、long 和 int，我还记得IDL利用module来创建名称空间，并且准确地映射为 Java 的 package，这些特性几乎和现在thrift的特性完全相同，所以thrift的设计思想和理念绝不是什么从火星来的new idea，看看在那个CORBA盛行的年代人们提出的概念，如图所示CORBA 请求的各个部分，回头我们再与thrift进行对比一下：

![Common Object Request Broker Architecture](http://zcitrq.bay.livefilestore.com/y1pJronriAjq9AasxlfkRFGO9E5Ioljuht5vGIT_Kjq5LANU7uTtKElt0UWmO6igfln4YBqrYCZPqG4hee1NQZ7mQoVSQz1TGCR/jt-2001-8-20-simpex-1.gif?psid=1)

**<span id="more-337"></span>Thrift 基础架构**

Thrift是一个服务端和客户端的架构体系，从我个人的感官上来看Thrift是一个类似XML-RPC+Java-to- IDL+Serialization Tools=Thrift 的东东，Thrift 具有自己内部定义的传输协议规范(TProtocol)和传输数据标准(TTransports)，通过IDL脚本对传输数据的数据结构(struct) 和传输数据的业务逻辑(service)根据不同的运行环境快速的构建相应的代码，并且通过自己内部的序列化机制对传输的数据进行简化和压缩提高高并发、 大型系统中数据交互的成本，下图描绘了Thrift的整体架构，分为6个部分：1.你的业务逻辑实现(You Code) 2.客户端和服务端对应的Service 3.执行读写操作的计算结果4.TProtocol 5.TTransports  6.底层I/O通信

![thrift](http://public.bay.livefilestore.com/y1pCfZp75Z-beYM_ZfeOVUmIjN3FskFdNTU9qpECWiCnk95JOaw6w5o4ZIiurVmK8w54SJ-pVkfQbYtJPaNb1F3vw/Apache-Thrift-Architecture.png?psid=1)

图 中前面3个部分是1.你通过Thrift脚本文件生成的代码，2.图中的褐色框部分是你根据生成代码构建的客户端和处理器的代码，3.图中红色的部分是2 端产生的计算结果。从TProtocol下面3个部分是Thrift的传输体系和传输协议以及底层I/O通信，Thrift并且提供 堵塞、非阻塞，单线程、多线程的模式运行在服务器上，还可以配合服务器/容器一起运行，可以和现有JEE服务器/Web容器无缝的结合。

**数据类型**

* Base Types：基本类型

* Struct：结构体类型

* Container：容器类型，即List、Set、Map

* Exception：异常类型

* Service： 定义对象的接口，和一系列方法

**协议**

Thrift可以让你选择客户端与服务端之间传输通信协议的类别，在传输协议上总体上划分为文本(text)和二进制(binary)传输协议, 为节约带宽，提供传输效率，一般情况下使用二进制类型的传输协议为多数，但有时会还是会使用基于文本类型的协议，这需要根据项目/产品中的实际需求：

* TBinaryProtocol – 二进制编码格式进行数据传输。

* TCompactProtocol – 这种协议非常有效的，使用Variable-Length Quantity (VLQ) 编码对数据进行压缩。

* TJSONProtocol – 使用JSON的数据编码协议进行数据传输。

* TSimpleJSONProtocol – 这种节约只提供JSON只写的协议，适用于通过脚本语言解析

* TDebugProtocol – 在开发的过程中帮助开发人员调试用的，以文本的形式展现方便阅读。

**传输层**

* TSocket- 使用堵塞式I/O进行传输，也是最常见的模式。

* TFramedTransport- 使用非阻塞方式，按块的大小，进行传输，类似于Java中的NIO。

* TFileTransport- 顾名思义按照文件的方式进程传输，虽然这种方式不提供Java的实现，但是实现起来非常简单。

* TMemoryTransport- 使用内存I/O，就好比Java中的ByteArrayOutputStream实现。

* TZlibTransport- 使用执行zlib压缩，不提供Java的实现。

**服务端类型**

* TSimpleServer -  单线程服务器端使用标准的堵塞式I/O。

* TThreadPoolServer -  多线程服务器端使用标准的堵塞式I/O。

* TNonblockingServer – 多线程服务器端使用非堵塞式I/O，并且实现了Java中的NIO通道。

**谁在用thrift ？**

![Quara ](http://public.bay.livefilestore.com/y1pEaI-1Jvv7yWALGBZpwJE4baM80WXFYHaHBeIrPS7DiItCE7dhTWN8bblTPiOMyeo597BqCjgtyOyR-r5tZHqOQ/quora.gif?psid=1)

Thrift用于Quara系统后端数据的通信，服务端是用C++来实现的，客户端则是python。

<span style="text-decoration: underline;">Quara背景:</span>Quara是在线问答服务公司，类似新浪微博和百度知道的合体，消息灵通人士透露，去年Quara获得了1400万美元投资，目前他们只有9名员工。

<span style="text-decoration: underline;">原文：</span>http://www.philwhln.com/quoras-technology-examined#thrift

![Evernote](http://public.bay.livefilestore.com/y1p0BU4Q_vPabzwkr0bUupVTpp2ly6B6E1L89I5s4azW4K96vETVgPMlPyVMVmrxmkAah7qY-CDdtIF5iC_9kO3sw/evernote-logo.gif?psid=1)

Thrift用于在多种Evernote API平台开发的客户端与Evernote服务器之间的通信与数据传输，Evernote API定义了自己的Evernote Data Access and Management (EDAM) 协议规范，让客户端使用更小的网络带宽上传、下载文件和在线即时搜索服务。

<span style="text-decoration: underline;">Evernote 背景:</span>EverNote是一款非常著名的免费软件，它最大的特点就是支持多平台，而且数据能通过网络互相同步。譬如说，你可以随时在手机上的Evernote新增笔记，回家后在电脑上也能看到它了！

<span style="text-decoration: underline;">原文：</span>http://www.evernote.com/about/developer/api/evernote-api.htm

HBase 中的Thrift

Thrift用于HBase中是为了提供跨平台的服务接口，在HBase 中可以使用[hbase-root]/bin/hbase thrift start 命令启动涵盖Thrift的HBase服务端，客户端通过thrift的命令生成不同版本的客户端代码，根据定义的数据格式，对远程HBase服务端进行 操作，是除了REST远程方法调用的另一种途径。

参见：http://wiki.apache.org/hadoop/Hbase/ThriftApi

更多资料请阅读：<span style="text-decoration: underline;">http://wiki.apache.org/thrift/PoweredBy</span>

**Thrift与其他传输方式的比较**

xml与JSON相比体积太大，但是xml传统，也不算复杂。

json 体积较小，新颖，但不够完善。

thrift 体积超小，使用起来比较麻烦，不如前两者轻便，但是对于1.高并发、2.数据传输量大、3.多语言环境， 满足其中2点使用 thrift还是值得的。

假定需要传输相同的内容，但使用不同的方式从 1、传输内容所产生的大小 2、传输过程中服务端和客户端所产生的开销，这2个方便进行比较。使用Thrift和其他方式的所产生的内容大小比较结果如下：

![thrift-size](http://public.bay.livefilestore.com/y1pCfZp75Z-bebqxcDBqfvqi1Cf6svZCypC7wkXK1OzwpqGYdgztQx3t9XEU9lZPm41HOIRc-_1fm03YiNgHrWApg/thrift-size.png?psid=1)

在上图中我们能明显看出，最臃肿的是RMI，其次是xml，使用Thrift的TCompactProtocol协议和Google 的 Protocol Buffers 相差的不算太多，相比而言还是Google 的 Protocol Buffers效果最佳。

使用Thrift 中的协议和其他方式的所产生的**运行开销**比较结果如下：

![thrift-load](http://public.bay.livefilestore.com/y1p7G5AiRmyV-zw46l5XQRY8t2pMv2kOwlHrgTPyhb28_3uP3a-CkBAVsk1Ohwsugh09MY9A37u5w_1B6AI-Iqp2Q/thrift-load.png?psid=1)

在上图中我们能明显看出，最占资源是REST2中协议，使用Thrift的TCompactProtocol协议和Google 的 Protocol Buffers 相差的不算太多，相比而言Thrift的TCompactProtocol协议效果最佳。