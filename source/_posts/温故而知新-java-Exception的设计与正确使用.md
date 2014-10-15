title: 温故而知新-java Exception的设计与正确使用
tags:
  - java
  - checked exception
  - java Exception
  - unchecked exception
  - 最佳实践
date: 2012-02-18 23:51:44
---

当工作了一段时间，慢慢积累出经验，再回顾一些基础的时候，往往可以理解的更加深入，并由此想到自己以前的一些场景案例是可以更优雅的。

做java没多久，转了前端，不过并没有全部丢弃java，反而写多了js以后，惠及java，有了不同的感受，莫非是殊途同归么。

或许过了N久，再回来看这时候的记录，又有不同感受。

<span id="more-589"></span>

转帖的转帖，来源于：

http://www.iteye.com/topic/2038#9555

### Exception 处理之最佳实践

原文：http://www.onjava.com/pub/a/onjava/2003/11/19/exceptions.html

作者：Gunjan Doshi 2003-11-19

译者注：本文算是一篇学习笔记，仅供学习参考使用，有不妥之处，还请指出。2003-12-04

“本文是Exception处理的一篇不错的文章，从Java Exception的概念介绍起，依次讲解了Exception的类型（Checked/Unchecked），Exception处理的最佳实现：

1．  选择Checked还是Unchecked的几个经典依据

2．  Exception的封装问题

3．  如无必要不要创建自己得Exception

4．  不要用Exception来作流程控制

5．  不要轻易的忽略捕获的Exception

6．  不要简单地捕获顶层的Exception”

——选自JAVADigest.Net对原文的介绍

“JAVADigest.Net这个站点不知道大家是否经常上，就像它的名字一样，它让我们更加有效的消化Java，或者它就像个中转站一样，至少对我是这样的，有些好的可以说是非常经典的技术文章，我都是通过它第一次获得，更多的时候我是为了偷懒才上JAVADigest.Net，因为如果是近期比较经典的文章，它上边都有介绍文字和原文连接。”

——小插曲并非常荣幸地推荐JAVADigest.Net给你

关于异常处理的一个问题就是要对何时（when）和如何（how）使用它们做到了然于心。在本文中我将介绍一些关于异常处理的最佳实践，同时我也会涉及到最近争论十分激烈的checked Exception的使用问题。

作为开发员，我们都希望能写出解决问题并且是高质量的代码。不幸的是，一些副作用（side effects）伴随着异常在我们的代码中慢慢滋生。无庸置疑，没有人喜欢副作用（side effects）,所以我们很快就用我们自己的方式来避免它，我曾经看到一些聪明的程序员用下面的方式来处理异常：

public void consumeAndForgetAllExceptions(){

try {

&#8230;some code that throws exceptions

} catch (Exception ex){

ex.printStacktrace();

}

}

上边的代码有什么问题么？

在回答以前让我们想想怎样才是正确的？是的，一旦程序碰到异常，它就该挂起程序而“做”点什么。那么上边的代码是这样子的么？看吧，它隐瞒了什么？它把所有的“苦水”往肚里咽（在控制台打印出异常信息），然后一切继续，从表面上看就像什么都没有发生过一样……，很显然，上边代码达到的效果并不是我们所期望的。

后来又怎样？

public void someMethod() throws Exception{

}

上边的代码又有什么问题？

很明显，上边的方法体是空的，它不实现任何的功能（没有一句代码），试问一个空方法体能抛出什么异常？当然Java并不阻止你这么干。最近，我也遇到类似的情景，方法声明会抛出异常，但是代码中并没有任何“机会”来“展示”异常。当我问开发员为什么要这样做的时候，他回答我说“我知道，它确实有点那个，但我以前就是这么干的并且它确实能为我工作。”

在C++社区曾经花了数年实践来实践如何使用异常，关于此类的争论在java社区才刚刚开始。我曾经看到许多Java程序员针对使用异常的问题进行争论。如果对于异常处理不当的话，异常可以大大减慢应用程序的执行速度，因为它将消耗内存和CPU来创建、抛出并捕获异常。如果过分的依赖异常处理，代码对易读和易使用这两方面产生影响，以至于会让我们写出上边两处“糟糕”代码。

异常原理

大体上说，有三种不同的“情景”会导致异常的抛出：

l         编程错误导致异常（Exception due Programming errors）: 这种情景下，异常往往处于编程错误（如：NullPointerException 或者 IllegalArgumentException）,这时异常一旦抛出，客户端将变得无能为力。

l         客户端代码错误导致异常（Exception due client code errors）: 说白点就是客户端试图调用API不允许的操作。

l         资源失败导致异常（Exception due to resource failures）: 如内存不足或网络连接失败导致出现异常等。这些异常的出现客户端可以采取相应的措施来恢复应用程序的继续运行。

Java中异常的类型

Java 中定义了两类异常：

l         Checked exception: 这类异常都是Exception的子类

l         Unchecked exception: 这类异常都是RuntimeException的子类，虽然RuntimeException同样也是Exception的子类，但是它们是特殊的，它们不能通过client code来试图解决，所以称为Unchecked exception

举个例子，下图为NullPointerException的继承关系：

Figure 1\. Sample exception hierarchy

图中，NullPointerException继承自RuntimeException，所以它是Unchecked exception.

以往我都是应用checked exception多于Unchecked exception，最近，在java社区激起了一场关于checked exception和使用它们的价值的争论。这场争论起源于JAVA是第一个拥有Checked exception的主流OO语言这样一个事实，而C++和C#都是根本没有Checked exception，它们所有的异常都是unchecked。

一个checked exception强迫它的客户端可以抛出并捕获它，一旦客户端不能有效地处理这些被抛出的异常就会给程序的执行带来不期望的负担。

Checked exception还可能带来封装泄漏，看下面的代码：

public List getAllAccounts() throws

FileNotFoundException, SQLException{

&#8230;

}

上边的方法抛出两个异常。客户端必须显示的对这两种异常进行捕获和处理即使是在完全不知道这种异常到底是因为文件还是数据库操作引起的情况下。因此，此时的异常处理将导致一种方法和调用之间不合适的耦合。

接下来我会给出几种设计异常的最佳实践 （Best Practises for Designing the API）

1．  当要决定是采用checked exception还是Unchecked exception的时候，你要问自己一个问题，“如果这种异常一旦抛出，客户端会做怎样的补救？”

[原文：When deciding on checked exceptions vs. unchecked exceptions, ask yourself, "What action can the client code take when the exception occurs?"]

如果客户端可以通过其他的方法恢复异常，那么这种异常就是checked exception；如果客户端对出现的这种异常无能为力，那么这种异常就是Unchecked exception；从使用上讲，当异常出现的时候要做一些试图恢复它的动作而不要仅仅的打印它的信息，总来的来说，看下表：

Client&#8217;s reaction when exception happens

Exception type

Client code cannot do anything

Make it an unchecked exception

Client code will take some useful recovery action based on information in exception

Make it a checked exception

此外，尽量使用unchecked exception来处理编程错误：因为unchecked exception不用使客户端代码显示的处理它们，它们自己会在出现的地方挂起程序并打印出异常信息。Java API中提供了丰富的unchecked excetpion，譬如：NullPointerException , IllegalArgumentException 和 IllegalStateException等，因此我一般使用这些标准的异常类而不愿亲自创建新的异常类，这样使我的代码易于理解并避免的过多的消耗内存。

2． 保护封装性（Preserve encapsulation）

不要让你要抛出的checked exception升级到较高的层次。例如，不要让SQLException延伸到业务层。业务层并不需要（不关心？）SQLException。你有两种方法来解决这种问题：

l         转变SQLException为另外一个checked exception，如果客户端并不需要恢复这种异常的话；

l         转变SQLException为一个unchecked exception，如果客户端对这种异常无能为力的话；

多数情况下，客户端代码都是对SQLException无能为力的，因此你要毫不犹豫的把它转变为一个unchecked exception，看看下边的代码：

public void dataAccessCode(){

try{

..some code that throws SQLException

}catch(SQLException ex){

ex.printStacktrace();

}

}

上边的catch块紧紧打印异常信息而没有任何的直接操作，这是情有可原的，因为对于SQLException你还奢望客户端做些什么呢？（但是显然这种就象什么事情都没发生一样的做法是不可取的）那么有没有另外一种更加可行的方法呢？

public void dataAccessCode(){

try{

..some code that throws SQLException

}catch(SQLException ex){

throw new RuntimeException(ex);

}

}

上边的做法是把SQLException转换为RuntimeException，一旦SQLException被抛出，那么程序将抛出RuntimeException,此时程序被挂起并返回客户端异常信息。

如果你有足够的信心恢复它当SQLException被抛出的时候，那么你也可以把它转换为一个有意义的checked exception, 但是我发现在大多时候抛出RuntimeException已经足够用了。

3． 不要创建没有意义的异常（Try not to create new custom exceptions if they do not have useful information for client code.）

看看下面的代码有什么问题？

public class DuplicateUsernameException

extends Exception {}

它除了有一个“意义明确”的名字以外没有任何有用的信息了。不要忘记Exception跟其他的Java类一样，客户端可以调用其中的方法来得到更多的信息。

我们可以为其添加一些必要的方法，如下：

public class DuplicateUsernameException

extends Exception {

public DuplicateUsernameException

(String username){&#8230;.}

public String requestedUsername(){&#8230;}

public String[] availableNames(){&#8230;}

}

在新的代码中有两个有用的方法：reqeuestedUsername(),客户但可以通过它得到请求的名称；availableNames(),客户端可以通过它得到一组有用的usernames。这样客户端在得到其返回的信息来明确自己的操作失败的原因。但是如果你不想添加更多的信息，那么你可以抛出一个标准的Exception:

throw new Exception(&#8220;Username already taken&#8221;);

更甚的情况，如果你认为客户端并不想用过多的操作而仅仅想看到异常信息，你可以抛出一个unchecked exception:

throw new RuntimeException(&#8220;Username already taken&#8221;);

另外，你可以提供一个方法来验证该username是否被占用。

很有必要再重申一下，checked exception应该让客户端从中得到丰富的信息。要想让你的代码更加易读，请倾向于用unchecked excetpion来处理程序中的错误（Prefer unchecked exceptions for all programmatic errors）。

4． Document exceptions.

你可以通过Javadoc’s @throws 标签来说明（document）你的API中要抛出checked exception或者unchecked exception。然而，我更倾向于使用来单元测试来说明（document）异常。不管你采用哪中方式，你要让客户端代码知道你的API中所要抛出的异常。这里有一个用单元测试来测试IndexOutOfBoundsException的例子：

public void testIndexOutOfBoundsException() {

ArrayList blankList = new ArrayList();

try {

blankList.get(10);

fail(&#8220;Should raise an IndexOutOfBoundsException&#8221;);

} catch (IndexOutOfBoundsException success) {}

}

上边的代码在请求blankList.get(10)的时候会抛出IndexOutOfBoundsException,如果没有被抛出，将fail(&#8220;Should raise an IndexOutOfBoundsException&#8221;)显示说明该测试失败。通过书写测试异常的单元测试，你不但可以看到异常是怎样的工作的，而且你可以让你的代码变得越来越健壮。

下面作者将介绍界中使用异常的最佳实践（Best Practices for Using Exceptions）

1．  总是要做一些清理工作（Always clean up after yourself）

如果你使用一些资源例如数据库连接或者网络连接，请记住要做一些清理工作（如关闭数据库连接或者网络连接），如果你的API抛出Unchecked exception，那么你要用try-finally来做必要的清理工作：

public void dataAccessCode(){

Connection conn = null;

try{

conn = getConnection();

..some code that throws SQLException

}catch(SQLException ex){

ex.printStacktrace();

} finally{

DBUtil.closeConnection(conn);

}

}

class DBUtil{

public static void closeConnection

(Connection conn){

try{

conn.close();

} catch(SQLException ex){

logger.error(&#8220;Cannot close connection&#8221;);

throw new RuntimeException(ex);

}

}

}

DBUtil是一个工具类来关闭Connection.有必要的说的使用的finally的重要性是不管程序是否碰到异常，它都会被执行。在上边的例子中，finally中关闭连接，如果在关闭连接的时候出现错误就抛出RuntimeException.

2． 不要使用异常来控制流程（Never use exceptions for flow control）

下边代码中，MaximumCountReachedException被用于控制流程：

public void useExceptionsForFlowControl() {

try {

while (true) {

increaseCount();

}

} catch (MaximumCountReachedException ex) {

}

//Continue execution

}

public void increaseCount()

throws MaximumCountReachedException {

if (count &amp;gt;= 5000)

throw new MaximumCountReachedException();

}

上边的useExceptionsForFlowControl()用一个无限循环来增加count直到抛出异常，这种做法并没有说让代码不易读，但是它是程序执行效率降低。

记住，只在要会抛出异常的地方进行异常处理。

3． 不要忽略异常

当有异常被抛出的时候，如果你不想恢复它，那么你要毫不犹豫的将其转换为unchecked exception，而不是用一个空的catch块或者什么也不做来忽略它，以至于从表面来看象是什么也没有发生一样。

4． 不要捕获顶层的Exception

unchecked exception都是RuntimeException的子类，RuntimeException又继承Exception,因此，如果单纯的捕获Exception,那么你同样也捕获了RuntimeException,如下代码：

try{

..

}catch(Exception ex){

}

一旦你写出了上边的代码（注意catch块是空的），它将忽略所有的异常，包括unchecked exception.

5． Log exceptions just once

Logging the same exception stack trace more than once can confuse the programmer examining the stack trace about the original source of exception. So just log it once.

总结

这里给出了一些关于异常处理的一些最佳实践，我并不想开始另一轮的关于checked exception 和 unchecked exception的争论。你可以根据自己的实际情况定制自己异常处理，我坚信我们将有更好的办法来处理我们代码中的异常。

在此，我将感谢Bruce Eckel, Joshua Kerievsky, 和Somik Raha对于写这篇文章所给于我的支持。

参考资源：

Related Resources

&#8220;Does Java need Checked Exceptions?&#8221; by Bruce Eckel

&#8220;Exceptional Java,&#8221; by Alan Griffiths

&#8220;The trouble with checked exceptions: A conversation with Anders Hejlsberg, Part II&#8221; on Artima.com

&#8220;Checked exceptions are of dubious value,&#8221; on C2.com

Conversation with James Gosling by Bill Venners

关于作者：

Gunjan Doshi works with agile methodologies and its practices and is a Sun certified Java programmer.

2003年12月4日星期四 Jplateau 译于精博

p