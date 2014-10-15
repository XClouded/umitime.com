title: Apache Thrift的简单使用
tags:
  - 拓展
date: 2011-08-17 17:00:01
---

来自于：    http://blog.csdn.net/amuseme_lu/article/details/6262572

### Apache Thrift的简单使用

## 1\. 简单介绍

Thrift是Facebook的一个开源项目，主要是一个跨语言的服务开发框架。它有一个代码生成器来对它所定义的IDL定义文件自动生成服务代码框架。用户只要在其之前进行二次开发就行，对于底层的RPC通讯等都是透明的。目前它支持的语言有C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, Smalltalk, and OCaml.

<span id="more-339"></span>

## 2\. 下载与安装

可以在http://incubator.apache.org/thrift/download/去下载它的最新版本，目前最新版本是0.5.0。另外你也可以check出它的svn,方法如下：

<pre class="brush:shell">svn co http://svn.apache.org/repos/asf/thrift/trunk thrift

cd thrift</pre>

在它的jira中看到，它的0.6版本也很快就会出来了。

我的本本是debian 6.0，如果用ubuntu的兄弟安装方法也是一样的

<pre class="brush:shell">tar -zxvf thrift-0.5.0.tar.gz
cd thrift-0.5.0
./configure
make
sudo make install</pre>

这时thrift的代码生成器和一些库文件就生成好了。

你可以用如下命令看一下thrift的版本信息

<pre class="brush:shell">thrift -version</pre>

## 3\. 一个简单的例子

在thrift源代码目录有一个叫tutorial的目录，进行其中后运行thrift命令生成相应的服务代码：

<pre class="brush:shell">$ thrift -r --gen cpp tutorial.thrift // -r对其中include的文件也生成服务代码 -gen是生成服务代码的语言</pre>

运行完之后会在当前目录看到一个gen-cpp目录，其中就是thrfit命令生成的代码

这时你cd到tutorial/cpp目录,运行make，生成相应的CppServer与CppClient程式。

这时你可以用./CppServer运行服务端，让其监听一个特定的端口

这时你可以用./CppClient运行客户端程式，让其去连接服务端，调用其所对应的服务。默认调用后会输出如下信息：

<pre class="brush:shell">lemo@debian:~/Workspace/Facebook/Thrift/thrift-0.5.0/tutorial/cpp$ ./CppServer
Starting the server...
ping()
add(1,1)
calculate(1,{4,1,0})
calculate(1,{2,15,10})
getStruct(1)</pre>

如果你的终端中也出现了如上的信息，恭喜你，运行成功了。如果在运行CppServer的时候找不到动态库，看看你是不是运行了make install，如果运行了，再运行一下sudo ldconfig试试。再用ldd CppServer看一下它有没有找到相应的动态库了。

## 4\. 例子分析

### 4.1 Thrift IDL的分析

这边有两个IDL文件，内容如下：

<pre class="brush:py">shared.thrift
---------------
**
 * This Thrift file can be included by other Thrift files that want to share
 * these definitions.
 */
namespace cpp shared
namespace java shared
namespace perl shared
// 这里定义了一个结构体，没有定义方法，对应于生成的代码在gen-cpp中的shared_types.h中，其中有一个class叫SharedStruct,
// 有没有看到其中有两个方法叫read和write，这就是用来对其进行序列化与把序列化的方法.
// 对了，其中的i32是Thrift IDL中定义的变量类型，对应于c++语言中的int32_t
struct SharedStruct {
  1: i32 key
  2: string value
}
// 这里定义的一个服务，它语义上类似于面向对象中的定义一个接口，thrift的编译器会对其产生一套实现其接口的客户端与服务端方法
// 服务的一般定义格式如下
// service &lt;name&gt;
// &lt;returntype&gt; &lt;name&gt;(&lt;arguments&gt;)
// [ throws (&lt;exceptions&gt;)]
//   ...
// }
service SharedService {
  SharedStruct getStruct(1: i32 key)
}
tutorial.thrift
----------------
/**
 * Thrift files can reference other Thrift files to include common struct
 * and service definitions. These are found using the current path, or by
 * searching relative to any paths specified with the -I compiler flag.
 *
 * Included objects are accessed using the name of the .thrift file as a
 * prefix. i.e. shared.SharedObject
 */
 // 这个IDL包含了另一个IDL,也就是说另一个IDL中的对象与服务对其时可见的
include "shared.thrift"
/**
 * Thrift files can namespace, package, or prefix their output in various
 * target languages.
 */
 // 这里定义了一些语言的namespace空间
namespace cpp tutorial
namespace java tutorial
namespace php tutorial
namespace perl tutorial
/**
 * Thrift lets you do typedefs to get pretty names for your types. Standard
 * C style here.
 */
 // 自定义类型
typedef i32 MyInteger
/**
 * Thrift also lets you define constants for use across languages. Complex
 * types and structs are specified using JSON notation.
 */
 // 定义一些变量
const i32 INT32CONSTANT = 9853
const map&lt;string,string&gt; MAPCONSTANT = {'hello':'world', 'goodnight':'moon'}
/**
 * You can define enums, which are just 32 bit integers. Values are optional
 * and start at 1 if not supplied, C style again.
 */
 // 定义枚举类型
enum Operation {
  ADD = 1,
  SUBTRACT = 2,
  MULTIPLY = 3,
  DIVIDE = 4
}
/**
 * Structs are the basic complex data structures. They are comprised of fields
 * which each have an integer identifier, a type, a symbolic name, and an
 * optional default value.
 *
 * Fields can be declared "optional", which ensures they will not be included
 * in the serialized output if they aren't set.  Note that this requires some
 * manual management in some languages.
 */
struct Work {
  1: i32 num1 = 0,
  2: i32 num2,
  3: Operation op,
  4: optional string comment, //这里的optional字段类型表示如果这个字段的值没有被赋值，它就不会被序列化输出
}
/**
 * Structs can also be exceptions, if they are nasty.
 */
 // 这里定义了一些异常
exception InvalidOperation {
  1: i32 what,
  2: string why
}
/**
 * Ahh, now onto the cool part, defining a service. Services just need a name
 * and can optionally inherit from another service using the extends keyword.
 */
 // 这里是定义服务，它继承了shared的服务
service Calculator extends shared.SharedService {
  /**
   * A method definition looks like C code. It has a return type, arguments,
   * and optionally a list of exceptions that it may throw. Note that argument
   * lists and exception lists are specified using the exact same syntax as
   * field lists in struct or exception definitions.
   */
   void ping(),
   i32 add(1:i32 num1, 2:i32 num2),
   i32 calculate(1:i32 logid, 2:Work w) throws (1:InvalidOperation ouch),
   /**
    * This method has a oneway modifier. That means the client only makes
    * a request and does not listen for any response at all. Oneway methods
    * must be void.
    */
   oneway void zip()
}</pre>

&nbsp;

### 4.2 服务端与客户端代码的分析

#### 4.2.1 c++服务端

在tutorial/cpp目录中的CppServer.cpp是它的服务代码，主要分成两部分，

一部分是main方法用于做一些初始化与服务的启动，第二部分对于IDL中定义的接口的实现

<pre class="brush:cpp">int main(int argc, char **argv) {
// 定义了RPC的协议工厂，这里使用了二进制协议，你不可以使用别的协议，如JSON,Compact等
shared_ptr&lt;TProtocolFactory&gt; protocolFactory(new TBinaryProtocolFactory());
// 这里生成用户实现的CalculatorHandler服务，再把其帮定到一个Processor上去，它主要用于处理协议的输入与输出流
shared_ptr&lt;CalculatorHandler&gt; handler(new CalculatorHandler());
shared_ptr&lt;TProcessor&gt; processor(new CalculatorProcessor(handler));
// 生成一个传输通道，这里使用了Socket方式
shared_ptr&lt;TServerTransport&gt; serverTransport(new TServerSocket(9090));
// 生成一个传输工厂，主要用于把上面的transport转换成一个新的应用层传输通道
shared_ptr&lt;TTransportFactory&gt; transportFactory(new TBufferedTransportFactory());
// 生成一个简单的服务端，这是一个单线程的服务端
TSimpleServer server(processor,
                      serverTransport,
                      transportFactory,
                      protocolFactory);
// 你也可以生成一个多线程的服务端，就是对其加入线程池。但它现在还不支持进程池，但可能会在0.7版本中进行支持。
 /**
  * Or you could do one of these
 shared_ptr&lt;ThreadManager&gt; threadManager =
   ThreadManager::newSimpleThreadManager(workerCount);
 shared_ptr&lt;PosixThreadFactory&gt; threadFactory =
   shared_ptr&lt;PosixThreadFactory&gt;(new PosixThreadFactory());
 threadManager-&gt;threadFactory(threadFactory);
 threadManager-&gt;start();
 TThreadPoolServer server(processor,
                          serverTransport,
                          transportFactory,
                          protocolFactory,
                          threadManager);
 TThreadedServer server(processor,
                        serverTransport,
                        transportFactory,
                        protocolFactory);
 */
 printf("Starting the server.../n");
 server.serve();   // 启动服务
 printf("done./n");
 return 0;
}</pre>

另一部分如下：

<pre class="brush:cpp">// 这一部分主要是实现接口类，用于提供给相应的服务使用。
class CalculatorHandler : public CalculatorIf {
public:
 CalculatorHandler() {}
 void ping() {
   printf("ping()/n");
 }
 int32_t add(const int32_t n1, const int32_t n2) {
   ...
 }
 int32_t calculate(const int32_t logid, const Work &amp;work) {
 ...
 }
 void getStruct(SharedStruct &amp;ret, const int32_t logid) {
 ...
 }
 void zip() {
   printf("zip()/n");
 }
protected:
 map&lt;int32_t, SharedStruct&gt; log;
};</pre>

#### 4.2.2 c++客户端

<pre class="brush:cpp">int main(int argc, char** argv) {
 // 生成一个Socket连接到服务端
 shared_ptr&lt;TTransport&gt; socket(new TSocket("localhost", 9090));
 // 对Socket通道加入缓冲功能
 shared_ptr&lt;TTransport&gt; transport(new TBufferedTransport(socket));
 // 生成相应的二进制协议，这个要和服务端一致，不然会出现协议版本不对的错误
 shared_ptr&lt;TProtocol&gt; protocol(new TBinaryProtocol(transport));
 // 生成客户端的服务对象
 CalculatorClient client(protocol);
 try {
   transport-&gt;open(); // 加开服务
   // 调用事先定义好的服务接口
   client.ping();
   printf("ping()/n");
   int32_t sum = client.add(1,1);
   printf("1+1=%d/n", sum);
   Work work;
   work.op = Operation::DIVIDE;
   work.num1 = 1;
   work.num2 = 0;
   try {
     int32_t quotient = client.calculate(1, work);
     printf("Whoa? We can divide by zero!/n");
   } catch (InvalidOperation &amp;io) {
     printf("InvalidOperation: %s/n", io.why.c_str());
   }
   work.op = Operation::SUBTRACT;
   work.num1 = 15;
   work.num2 = 10;
   int32_t diff = client.calculate(1, work);
   printf("15-10=%d/n", diff);
   // Note that C++ uses return by reference for complex types to avoid
   // costly copy construction
   SharedStruct ss;
   client.getStruct(ss, 1);
   printf("Check log: %s/n", ss.value.c_str());
   // 关闭服务
   transport-&gt;close();
 } catch (TException &amp;tx) {
   printf("ERROR: %s/n", tx.what());
 }
}</pre>

#### 4.2.3 其它代码的实现

在tutorial目录中有其它代码的例子，如erl,java,python,perl,ruby等。

## 5 参考

1\. http://incubator.apache.org/thrift/

2\. http://incubator.apache.org/thrift/static/thrift-20070401.pdf