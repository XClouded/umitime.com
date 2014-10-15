title: 用Hadoop MapReduce进行大数据分析
tags:
  - java
  - 拓展
  - Hadoop
  - MapReduce
  - 大数据分析
date: 2012-04-17 16:25:16
---

来源：http://www.ibm.com/developerworks/cn/java/j-javadev2-15/index.html

Google 在 2001 年发布图像搜索功能时，只有 2.5 亿索引图像，不到 10 年，这个巨大的搜索功能已经可以检索超过 100 亿个图像了，每分钟有 35 小时的内容上传到 YouTube。据称，Twitter 每天平均处理 5500 万 tweet。今年早些时候，搜索功能每天记录 6 亿条查询记录。_这_ 就是我们讨论大数据的意义所在。

<span id="more-705"></span>

<div class="ibm-container ibm-alt-header dw-container-sidebar">

## 关于本系列

<div class="ibm-container-body">

从 Java 技术首次亮相以来，Java 开发的格局已经发生了巨大的变化。得益于成熟的开源框架和可靠的租用式部署基础设施，现在已经可以迅速经济地汇编、测试、运行和维护 Java 应用程序了。在 [本系列](http://www.ibm.com/developerworks/cn/views/java/libraryview.jsp?search_by=Java+%E5%BC%80%E5%8F%91+2.0) 中，Andrew Glover 将探索使这种全新开发范例成为可能的各种技术和工具。

</div>
</div>

如此大规模的数据一度仅限于大企业、学校和政府机构 — 这些机构有能力购买昂贵的超级计算机、能够雇用员

工保障其运行。今天，由于存储成本的降低和处理能力的商品化，一些小公司，甚至个人都可以存储和挖掘同样的数据，推动新一轮的应用程序创新。

大数据革命技术之一是 MapReduce，一个编程模式，是 Google 针对大规模、分布式数据而开发的。在本文中，我将介绍 Apache 的开源 MapReduce 实现，Hadoop，也有人将其称之为云计算的杀手应用程序。

<a name="N100A1"><span class="atitle">关于 Hadoop</span></a>

Apache 的 Hadoop 框架本质上是一个用于分析大数据集的机制，不一定位于数据存储中。Hadoop 提取出了 MapReduce 的大规模数据分析引擎，更易于开发人员理解。Hadoop 可以扩展到无数个节点，可以处理所有活动和相关数据存储的协调。

Hadoop 的众多特性和配置使其成为一个十分有用且功能强大的框架，其用途和功能令人惊讶。Yahoo! 以及其他许多组织已经找到了一个高效机制来分析成堆的字节数。在单个节点上运行 Hadoop 也很容易；您所需要的只是一些需要分析的数据，以及熟悉一般的 Java 代码。Hadoop 也可和 Ruby、Python 以及 C++ 一起使用。

<div class="ibm-container ibm-alt-header dw-container-sidebar">

## 了解更多 MapReduce

<div class="ibm-container-body">

如果您是本系列的读者，您可能已经见过 MapReduce 一两次了。在 “[通过 CouchDB 和 Groovy 的 RESTClient 实现 REST](http://www.ibm.com/developerworks/cn/java/j-javadev2-5/index.html)” 中，我介绍了 CouchDB 如何利用 MapReduce 进行查看，接着在 “[MongoDB：拥有 RDBMS 特性的 NoSQL 数据存储](http://www.ibm.com/developerworks/cn/java/j-javadev2-12/index.html)” 中我再次提到 MapReduce，处理 MongoDB 文档的机制。

</div>
</div>

作为处理大数据集的概念框架，MapReduce 对于使用许多计算机来解决分布式问题而言是高度优化的。顾名思义，这个框架由两个函数构成。`map` 函数专用于获取大数据输入，并将其分成小片段，然后交由其他进程进行操作。`reduce` 函数整理 `map` 收集的各个回应，然后显示最后的输出。

在 Hadoop 中，您可以通过扩展 Hadoop 自身的基类来定义 `map` 和 `reduce` 实现。实现和输入输出格式被一个指定它们的配置联系在一起。Hadoop 非常适合处理包含结构数据的大型文件。Hadoop 可以对输入文件进行原始解析，这一点特别有用，这样您就可以每次处理一行。定义一个 `map` 函数实际上只是一个关于确定您从即将输入的文本行中捕获什么内容的问题。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N100E2"><span class="atitle">数据，无处不在的数据！</span></a>

美国政府产生大量数据，只有一部分是普通民众所感兴趣的。各种政府机构免费发布关于 US 经济健康状况和更改社会人口统计资料的数据。U.S. Geological Survey (USGS) 发布国内外地震数据。

世界各地每天都有很多个小型地震发生。其中大多数发生在地壳深处，没有人能感觉到，尽管如此，但是监听站仍然会进行记录。USGS 以 CSV（或逗号分隔值）文件的格式发布每周地震数据。

每周文件平均不是很大 — 只有大约 100 KB 左右。但是，它可以作为学习 Hadoop 的基础。记住，Hadoop 有能力处理_更_ 大的数据集。

<a name="N100F6"><span class="smalltitle">跟踪震动</span></a>

我近期从 USGS 网站下载的 CSV 文件有大约 920 多行。如 清单 1 所示：

<a name="listing1">**清单 1\. 清单 1\.  一个 USGS 地震数据文件的行数统计**</a>

<pre class="displaycode">$&gt; wc -l eqs7day-M1.txt
  920 eqs7day-M1.txt</pre>

CVS 文件内容如 清单 2 所示（这是前两行）：

<a name="listing2">**清单 2\. 清单 2\.  CVS 文件的前两行**</a>

<pre class="displaycode">$&gt; head -n 2 eqs7day-M1.txt
Src,Eqid,Version,Datetime,Lat,Lon,Magnitude,Depth,NST,Region
ci,14896484,2,"Sunday, December 12, 2010 23:23:20 UTC",33.3040,-116.4130,1.0,11.70,22,
  "Southern California"</pre>

这就是我称之为_信息丰富_ 的文件，尤其是当您想到它总共有 920 行记录时。然而我只想知道在该文件报告的这一周内每一天有多少次地震发生。我想知道在这 7 天内哪个区域是地震频发区。

我第一个想到的就是使用简单的 `grep` 命令来搜索每天的地震数。

看看这个文件，我发现数据记录是从 12 月 12

开始的。因此我对该字符串执行了一次 `grep -c`，其结果如清单 3 所示：

<a name="listing3">**清单 3\. 清单 3\. 12 月 12 有多少次地震发生？**</a>

<pre class="displaycode">$&gt; grep -c 'December 12' eqs7day-M1.txt
98</pre>
<div class="ibm-container ibm-alt-header dw-container-sidebar">

## 安装 Hadoop

<div class="ibm-container-body">

如果您之前没有安装 Hadoop，那么现在就装。第一步，[下载最新版二进制文件](http://hadoop.apache.org/mapreduce/releases.html#Download)，解压，然后在您的路径上设置 Hadoop 的 `bin` 目录。完成这些您就可以直接执行 `hadoop` 命令了。使用 Hadoop 要求您执行它的 `hadoop` 命令，而不是像您所见到的那样调用 `java` 命令。您可以向 `hadoop` 命令传选项，诸如在哪里可以找到您的 Java 二进制文件（例如，表示您的 `map` 和 `reduce` 实现）。在我的示例中，我创建了一个 jar 文件，告诉 Hadoop 我想在我的 jar 文件内运行哪个任务。我也向 Hadoop 类路径添加了一些运行我的应用程序所需的附加二进制文件。

</div>
</div>

现在，我知道在 12 月 12 日有 98 条记录，也就是说有 98 次地震。我只能沿着这条记录向下，对 12 月 10 日的记录执行一次 `grep`，接着是 11 号，等等。这听起来有点乏味。更糟糕的是，我还需要知道在该文件中的是哪几天。我确实不关心这些，甚至有时候我可能无法获取该信息。事实上，我只想知道在七天这样一个时间段内任何一天的地震次数，使用 Hadoop 我就可以很容易的获取这一信息。

Hadoop 只需要几条信息就可以回答我的第一个和第二个问题：即，要处理哪条输入以及如何处理 `map` 和 `reduce`。我也必须提供了一个可以将每件事都联系起来的作业。在我开始处理这些代码之前，我需要花点时间确定我的 CSV 数据整齐有序。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N10179"><span class="atitle">使用 opencsv 进行数据解析</span></a>

除了地震 CSV 文件的第一行之外，第一行是文件头，每一行都是一系列逗号分隔数据值。我只对数据的 3 个部分感兴趣：日期、地点和震级。为了获取这些资料，我将使用一个很棒的开源库 `opencsv`，它将会帮助我分析 CSV 文件。

作为一个测试优先的工具，我首先编写一个快捷 JUnit 测试，确认我可以从 CSV 文件的一个样例行获取的我所需要的信息，如清单 4 所示：

<a name="listing4">**清单 4\. 清单 4\. 解析一个 CSV 行**</a>

<pre class="displaycode">public class CSVProcessingTest {

 private final String LINE = "ci,14897012,2,\"Monday, December 13, 2010 " +
            "14:10:32 UTC\",33.0290,-115." +
            "5388,1.9,15.70,41,\"Southern California\"";

 @Test
 public void testReadingOneLine() throws Exception {
  String[] lines = new CSVParser().parseLine(LINE);

  assertEquals("should be Monday, December 13, 2010 14:10:32 UTC",
    "Monday, December 13, 2010 14:10:32 UTC", lines[3]);

  assertEquals("should be Southern California",
    "Southern California", lines[9]);

  assertEquals("should be 1.9", "1.9", lines[6]);
 }
}</pre>

正如您在 [清单 4](#listing4) 中所看到的，`opencsv` 处理逗号分隔值非常容易。该解析器仅返回一组 `String`，所以有可能获取位置信息（别忘了，在 Java 语言中数组和集合的访问是从零开始的）。

<a name="N101A2"><span class="smalltitle">转换日期格式</span></a>

当使用 MapReduce 进行处理时，`map` 函数的任务是选择一些要处理的值，以及一些键。这就是说，`map` 主要处理和返回两个元素：一个键和一个值。回到我之前的需求，我首先想知道每天会发生多少次地震。因此，当我在分析地震文件时，我将发布两个值：键是日期，值是一个计数器。`reduce` 函数将对计数器（只是一些值为 1 的整数）进行总计。因此，提供给我的是在目标地震文件中某一个日期出现的次数。

由于我只对 24 小时时段内的信息感兴趣，我得剔除每个文件中的日期的时间部分。在 清单 5 中，我编写了一个快速测试，验证如何将一个传入文件中的特定日期信息转换成一个更一般的 24 小时日期：

<a name="listing5">**清单 5\. 清单 5\. 日期格式转换**</a>

<pre class="displaycode">@Test
public void testParsingDate() throws Exception {
 String datest = "Monday, December 13, 2010 14:10:32 UTC";
 SimpleDateFormat formatter = new SimpleDateFormat("EEEEE, MMMMM dd, yyyy HH:mm:ss Z");
 Date dt = formatter.parse(datest);

 formatter.applyPattern("dd-MM-yyyy");
 String dtstr = formatter.format(dt);
 assertEquals("should be 13-12-2010", "13-12-2010", dtstr);
}</pre>

在 [清单 5](#listing5) 中，我使用了 `SimpleDateFormat` Java 对象，将 CSV 文件中格式为 Monday, December 13, 2010 14:10:32 UTC 的日期 `String` 转换成了更一般的 13-12-2010。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N101D6"><span class="atitle">Hadoop 的 map 和 reduce</span></a>

现在我已经找到了处理 CSV 文件以及其日期格式的解决方法。我要开始在 Hadoop 中实施我的 `map` 和 `reduce` 函数了。这个过程需要理解 Java 泛型，因为 Hadoop 选择使用显式类型，为了安全起见。

当我使用 Hadoop 定义一个映射实现时，我只扩展 Hadoop 的 `Mapper` 类。然后我可以使用泛型来为传出键和值指定显式类。类型子句也指定了传入键和值，这对于读取文件分别是字节数和文本行数。

`EarthQuakesPerDateMapper` 类扩展了 Hadoop 的 `Mapper` 对象。它显式地将其输出键指定为一个 `Text` 对象，将其值指定为一个 `IntWritable`，这是一个 Hadoop 特定类，实质上是一个整数。还要注意，class 子句的前两个类型是 `LongWritable` 和 `Text`，分别是字节数和文本行数。

由于类定义中的类型子句，我将传入 `map` 方法的参数类型设置为在 `context.write` 子句内带有该方法的输出。如果我想指定其他内容，将会出现一个编译器问题，或 Hadoop 将输出一个错误消息，描述类型不匹配的消息。

<a name="listing6">**清单 6\. 清单 6\. 一个映射实现**</a>

<pre class="displaycode">public class EarthQuakesPerDateMapper extends Mapper&lt;LongWritable,
  Text, Text, IntWritable&gt; {
 @Override
 protected void map(LongWritable key, Text value, Context context) throws IOException,
   InterruptedException {

  if (key.get() &gt; 0) {
   try {
     CSVParser parser = new CSVParser();
     String[] lines = parser.parseLine(value.toString());

     SimpleDateFormat formatter =
       new SimpleDateFormat("EEEEE, MMMMM dd, yyyy HH:mm:ss Z");
     Date dt = formatter.parse(lines[3]);
     formatter.applyPattern("dd-MM-yyyy");

     String dtstr = formatter.format(dt);
     context.write(new Text(dtstr), new IntWritable(1));
   } catch (ParseException e) {}
  }
 }
}</pre>

[清单 6](#listing6) 中的 `map` 实现比较简单：本质上是，Hadoop 为在输入文件中找到的每一行文本调用这个类。为了避免除了 CSV 头部，首先检查是否字节数（`key` 对象）为零。然后执行清单 4 和 5 中的步骤：捕获传入日期，进行转换，然后设置为传出键。我也提供了一个数：1。就是说，我为每个日期编写一个计数器，当 `reduce` 实现被调用时，获取一个键和一系列值。在本例中，键是日期及其值，如 清单 7 所示：

<a name="listing7">**清单 7\. 清单 7\. 一个 map 输出和 reduce 输入的逻辑视图**</a>

<pre class="displaycode">"13-12-2010":[1,1,1,1,1,1,1,1]
"14-12-2010":[1,1,1,1,1,1]
"15-12-2010":[1,1,1,1,1,1,1,1,1]</pre>

注意，`context.write(new Text(dtstr), new

IntWritable(1))`（在 [清单 6](#listing6) 中）构建了如 [清单 7](#listing7) 所示的逻辑集合。正如您所了解的，`context` 是一个保存各种信息的 Hadoop 数据结构。`context` 被传递到 `reduce` 实现，`reduce` 获取这些值为 1 的值然后总和起来。因此，一个 `reduce` 实现逻辑上创建如 清单 8 所示的数据结构：

<a name="listing8">**清单 8\. 清单 8\. 一个 reduce 输出视图 **</a>

<pre class="displaycode">"13-12-2010":8
"14-12-2010":6
"15-12-2010":9</pre>

我的 `reduce` 实现如 [清单 9](#listing9) 所示。与 Hadoop 的 `Mapper` 一样，`Reducer` 被参数化了：前两个参数是传入的键类型（`Text`）和值类型（`IntWritable`），后两个参数是输出类型：键和值，这在本例中是相同的。

<a name="listing9">**清单 9\. 清单 9\.  reduce 实现**</a>

<pre class="displaycode">public class EarthQuakesPerDateReducer extends Reducer&lt;Text, IntWritable, Text,
  IntWritable&gt; {
 @Override
 protected void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context)
  throws IOException, InterruptedException {
  int count = 0;
  for (IntWritable value : values) {
   count++;
  }
  context.write(key, new IntWritable(count));
 }
}</pre>

我的 `reduce` 实现非常简单。正如我在 [清单 7](#listing7) 中所指出的，传入的是实际上是一个值的集合，在本例中是 1 的集合，我所做的就是将它们加起来，然后写出一个新键值对表示日期和次数。我的 `reduce` 代码可以挑出您在 [清单 8](#listing8) 中所见到的这几行。逻辑流程看起来像这样：

<pre class="displaycode">"13-12-2010":[1,1,1,1,1,1,1,1] -&gt; "13-12-2010":8</pre>

当然，这个清单的抽象形式是 `map -&gt; reduce`。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N102B0"><span class="atitle">定义一个 Hadoop Job</span></a>

现在我已经对我的 `map` 和 `reduce` 实现进行了编码，接下来所要做的是将所有这一切链接到一个 Hadoop `Job`。定义一个 `Job` 比较简单：您需要提供输入和输出、`map` 和 `reduce` 实现（如 [清单 6](#listing6) 和 [清单 9](#listing9) 所示）以及输出类型。在本例中我的输出类型和 `reduce` 实现所用的是同一个类型。

<a name="listing10">**清单 10\. 清单 10\. 一个将 map 和 redece 绑在一起的 Job **</a>

<pre class="displaycode">public class EarthQuakesPerDayJob {

 public static void main(String[] args) throws Throwable {

  Job job = new Job();
  job.setJarByClass(EarthQuakesPerDayJob.class);
  FileInputFormat.addInputPath(job, new Path(args[0]));
  FileOutputFormat.setOutputPath(job, new Path(args[1]));

  job.setMapperClass(EarthQuakesPerDateMapper.class);
  job.setReducerClass(EarthQuakesPerDateReducer.class);
  job.setOutputKeyClass(Text.class);
  job.setOutputValueClass(IntWritable.class);

  System.exit(job.waitForCompletion(true) ? 0 : 1);
 }
}</pre>

在 [清单 10](#listing10) 中，我使用一个 `main` 方法将所有这一切绑在一起，该方法有两个参数：地震 CSV 文件的目录，以及生成报告的输出目录（Hadoop 更喜欢创建该目录）。

为了执行这个小框架，我需要将这些类打包。我还需要告知 Hadoop 在哪里可以找到 `opencsv` 二进制文件。然后可以通过命令行执行 Hadoop ，如 清单 11 所示：

<a name="listing11">**清单 11\. 清单 11\. 执行 Hadoop**</a>

<pre class="displaycode">$&gt; export HADOOP_CLASSPATH=lib/opencsv-2.2.jar
$&gt; hadoop jar target/quake.jar com.b50.hadoop.quake.EarthQuakesPerDayJob
   ~/temp/mreduce/in/ ~/temp/mreduce/out</pre>

运行这些代码，Hadoop 开始运行时您将可以看到一堆文本在屏幕上一闪而过。我所用的 CSV 文件相比专门用于处理这种情况的 Hadoop，那真是小巫见大巫

！hadoop 应该可以在几秒钟内完成，具体取决于您的处理功能。

完成这些后，您可以使用任何编辑器查看输出文件内容。还可以选择直接使用 `hadoop` 命令。正如 清单 12 所示：

<a name="listing12">**清单 12\. 清单 12\. 读取 Hadoop 输出**</a>

<pre class="displaycode">$&gt; hadoop dfs -cat part-r-00000
05-12-2010      43
06-12-2010      143
07-12-2010      112
08-12-2010      136
09-12-2010      178
10-12-2010      114
11-12-2010      114
12-12-2010      79</pre>

如果您像我一样，在 [清单 12](#listing12) 中首先会注意到的就是每天地震数 — 12 月 9 日就有 178 次地震。希望您也会注意到 Hadoop 实现了我所想要的：整齐地列出我的研究范围内每天的地震次数。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N10326"><span class="atitle">编写另一个 Mapper</span></a>

接下来，我想找到地震发生在哪里，以及如何快速计算出在我的研究范围内记录地震次数最多的是哪个区域。当然，您已经猜到了，Hadoop 可以轻松地做到。在这个案例中，键不再是日期而是区域。因此，我编写了一个新的 `Mapper` 类。

<a name="listing13">**清单 13\. 清单 13\. 一个新的 map 实现**</a>

<pre class="displaycode">public class EarthQuakeLocationMapper extends Mapper&lt;LongWritable, Text, Text,
  IntWritable&gt; {
 @Override
 protected void map(LongWritable key, Text value, Context context) throws IOException,
  InterruptedException {
  if (key.get() &gt; 0) {
   String[] lines = new CSVParser().parseLine(value.toString());
   context.write(new Text(lines[9]), new IntWritable(1));
  }
 }
}</pre>

和之前获取日期然后进行转换相比，在 [清单 13](#listing13) 中我所作的是获取位置，这是 CSV 阵列中的最后一个条目。

相比一个庞大的位置和数字列表，我将结果限制在那些 7 天内出现 10 次的区域。

<a name="listing14">**清单 14\. 清单

14\. 哪里的地震较多？**</a>

<pre class="displaycode"> public class EarthQuakeLocationReducer extends
Reducer&lt;Text, IntWritable, Text, IntWritable&gt; { @Override protected void
reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws
IOException, InterruptedException { int count = 0; for (IntWritable value :
values) { count++; } if (count &gt;= 10) { context.write(key, new
IntWritable(count)); } } }</pre>

[清单 14](#listing14) 中的代码和 [清单 9](#listing9) 中的代码非常类似；然而，在本例中，我限制了输出大于或等于 10。接下来，我将 `map` 和 `reduce`，以及其他 `Job` 实现绑在一起，进行打包，然后和平常一样执行 Hadoop 获取我的新答案。

使用 `hadoop dfs` 目录显示我所请求的新值：

<a name="listing15">**清单 15\. 清单 15\. 地震区域分布**</a>

<pre class="displaycode">$&gt; hadoop dfs -cat part-r-00000
Andreanof Islands, Aleutian Islands, Alaska     24
Arkansas        40
Baja California, Mexico 101
Central Alaska  74
Central California      68
Greater Los Angeles area, California    16
Island of Hawaii, Hawaii        16
Kenai Peninsula, Alaska 11
Nevada  15
Northern California     114
San Francisco Bay area, California      21
Southern Alaska 97
Southern California     115
Utah    19
western Montana 11</pre>

从 [清单 15](#listing15) 还可以得到什么？首先，北美洲西海岸，从墨西哥到阿拉斯加是地震高发区。其次，阿肯色州明显位于断带层上，这是我没有意识到的。最后，如果您居住在北部或者是南加州（很多软件开发人员都居住于此），您周围的地方每隔 13 分钟会震动一次。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N1037F"><span class="atitle">结束语</span></a>

使用 Hadoop 分析数据轻松且高效，对于它对数据分析所提供的支持，我只是了解皮毛而已。Hadoop 的设计旨在以一种分布式方式运行，处理运行 `map` 和 `reduce` 的各个节点之间的协调性。作为示例，本文中我只在一个 JVM 上运行 Hadoop，该 JVM 仅有一个无足轻重的文件。

Hadoop 本身是一个功能强大的工具，围绕它还有一个完整的、不断扩展的生态系统，可以提供子项目至基于云计算的 Hadoop 服务。Hadoop 生态系统演示了项目背后丰富的社区活动。来自社区的许多工具证实了大数据分析作为一个全球业务活动的可行性。有了 Hadoop，分布式数据挖掘和分析对所有软件创新者和企业家都是可用的，包括但不限于 Google 和 Yahoo! 这类大企业。