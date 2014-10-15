title: Hibernate 3深度解析
tags:
  - java
  - hibernate 3
date: 2012-04-17 16:38:44
---

来源： http://www.ibm.com/developerworks/cn/java/j-lo-hibernate3/index.html

Hibernate 作为 Java ORM 模式的优秀开源实现， 当下已经成为一种标准，为饱受 JDBC 折磨的 Java 开发者带来了“福音。快速的版本更新，想开发者所想，Hibernate 的开发团队俨然就是我们开发者的知音呀。随着 Hibernate 的用户群的越来越壮大，Hibernate 的功能也变得更加强大和健硕起来，下面就对于我们比较常用和关心的一些内容做一些比较深入的探讨，我们的宗旨就是不但会灵活应用，还能知其所以然，不要求大家一定要把 Hibernate 的原理弄的很清楚，但其中一些好的模式，好的设计方法及其中重要功能的实现原理和方法还是对我们很有帮助的。我们将会从 Hibernate 的对象关系映射，Hibernate 的事务处理，Hibernate 对大数量的处理方法以及 Hibernate 根据不同情况怎样来调整性能等方面来和大家共同探讨。下面我们开始讲解 Hibernate 的对象关系映射。

<span id="more-707"></span>

<a name="major1.2"></a><span class="atitle">深度探索一（Hibernate 的对象关系映射）</span>

首先我们新建一个 Java project，命名为 hibernate-deep-research。我们知道现在通过数据表反生成的 Java 对象的工具很多，比如 Jboss Tools 的 HibernateTools 等，选择你熟悉的就好了，都大同小异的，为了更好的掌握基本知识，本文没使用这些工具， 第一步我们首先完成 Hibernate 的配置文件 &#8211; hibernate.cfg.xml,, 该文件内容见清单 1 所示：

<a name="listing1"></a>**清单 1.hibernate.cfg.xml 内容**

<pre class="displaycode"> &lt;?xml version="1.0" encoding="utf-8"?&gt;
 &lt;!DOCTYPE hibernate-configuration PUBLIC
"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd"&gt;
 &lt;hibernate-configuration&gt;
    &lt;session-factory name="sessionFactory"&gt;
        &lt;property name="hibernate.bytecode.use_reflection_optimizer"&gt;false&lt;/property&gt;
        &lt;property name="hibernate.connection.driver_class"&gt;org.h2.Driver&lt;/property&gt;
        &lt;property name="hibernate.connection.url"&gt;jdbc:h2:~/test&lt;/property&gt;
 &lt;property name="hibernate.connection.username"&gt;sa&lt;/property&gt;
    &lt;property name="hibernate.dialect"&gt;org.hibernate.dialect.H2Dialect&lt;/property&gt;
    &lt;mapping class="org.ibm.hibernate.deep.research.Test" /&gt;
    &lt;/session-factory&gt;
 &lt;/hibernate-configuration&gt;</pre>

从这个 Hibernate 的配置文件中我们可以看出本文使用的数据库为 H2，所以我们要将 H2 的 jar 包引到本工程中。关于 H2 的使用方法，这里不做详细介绍了，它是 HSQLDB 的重写，速度更快些。我们还需要一个 POJO 对象类 Test，值得注意的是我们这里 mapping 属性设置的是 class 而不是 resource，如果是 resource 我们就需要指定 hbm 的 xml 文件，下面我们看下 Test 这个类，POJO Test 类的详细代码见清单 2 所示：

<a name="listing2"></a>**清单 2.Test 对象类内容**

<pre class="displaycode">@Entity
@Table(name = "TEST", schema = "PUBLIC", catalog = "TEST")
public class Test implements java.io.Serializable {
	private int id;private String name;
	public Test() {}
	public Test(int id) {this.id = id;}
	public Test(int id, String name) {
		this.id = id;
		this.name = name;}
	@Id
	@Column(name = "ID", unique = true, nullable = false)
	public int getId() {
		return this.id;}
	public void setId(int id) {
		this.id = id;}
	@Column(name = "NAME", length = 10)
	public String getName() {
		return this.name;}
	public void setName(String name) {
		this.name = name;}

}</pre>

Test 这个 POJO 类我们使用 annotation 来完成与数据表的映射关系，@Entity 表示这是一个实体类，@Table 指定与那个数据表产生映射关系，@Id 指明主键，@Column 来指明 POJO 对象中的变量与数据表中字段的对应关系，当然 annotation 还有很多，这里就不一一介绍了。从中我们可以知道 Test 这个 POJO 类与数据库中的 test 表产生映射关系，那么这样的映射关系 Hibernate 是怎样来维护的呢？还有上面的 Hibernate 配置文件 hibernate.cfg.xml 又是什么时候，怎样加载的呢？配置文件中的 mapping 属性有的是 resource，有的是 class，Hibernate 又是怎么来区分它们的呢？ resource 与 class 加载的不同点又在什么地方呢 ? 如果能回答这些问题，你就基本了解了 Hibernate 的对象映射关系，你可以跳过这部分了，呵呵，如果你还没有个明确答案，那么请继续，下面我们就通过一个测试来一一解答上面的这些疑问。新建一个单元测试类命名为 HibernateTestCase, 继承自 TestCase，下面我们来看下 HibernateTestCase 的 setUp 方法，代码见清单 3 所示：

<a name="listing3"></a>**清单 3.HibernateTestCase 代码**

<pre class="displaycode"> Transaction tx;
 protected void setUp() throws Exception {
 Configuration cfg = new Configuration();
 cfg.configure("/org/hibernate.cfg.xml");
		 SessionFactory sf  = cfg.buildSessionFactory();
 session = sf.openSession();
       tx = session.beginTransaction();
 }</pre>

然后我们执行这个测试用例，跟踪下执行过程，我们会发现虽然在创建 configration 实例时我们没有指定任何资源文件，但 Hibernate 会自动设置 hibernate.properties 为默认加载文件，Hibernate 是在哪里又是怎样来设置的呢？我们来看下 Environment 这个类，在这个类的第 608 行有这么段代码 ConfigHelper.getResourceAsStream(&#8220;/hibernate.properties&#8221;)，这段代码是被 Configration 类的 reset 方法调用的，我们回过头来再看下 Environment 中的这段代码，如清单 4 所示：

<a name="listing4"></a>**清单 4.Environment 中的代码**

<pre class="displaycode"> ISOLATION_LEVELS.put( new Integer(Connection.TRANSACTION_NONE), "NONE" );
 ISOLATION_LEVELS.put(
    new Integer(Connection.TRANSACTION_READ_UNCOMMITTED), "READ_UNCOMMITTED" );
 ISOLATION_LEVELS.put(
    new Integer(Connection.TRANSACTION_READ_COMMITTED), "READ_COMMITTED" );
 ISOLATION_LEVELS.put(
    new Integer(Connection.TRANSACTION_REPEATABLE_READ), "REPEATABLE_READ" );
 ISOLATION_LEVELS.put(
    new Integer(Connection.TRANSACTION_SERIALIZABLE), "SERIALIZABLE" );</pre>

这段代码是初始化事务隔离级别的，我们在讲到事务那一节会用到，在这里先简单提一下，有个印象。

我们接下来再看下 Hibernate 是怎样加载我们指定的配置文件的，来看下 ConfigHelper 这个类的 getResourceAsStream 方法，见清单 5 所示：

<a name="listing5"></a>**清单 5.getResourceAsStream 方法**

<pre class="displaycode"> public static InputStream getResourceAsStream(String resource) {
 String stripped = resource.startsWith("/") ?
				 resource.substring(1) : resource;
 InputStream stream = null;
 ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
 if (classLoader!=null) {
			 stream = classLoader.getResourceAsStream( stripped );}
 if ( stream == null ) {stream = Environment.class.getResourceAsStream( resource );}
 if ( stream == null ) {stream =
     Environment.class.getClassLoader().getResourceAsStream( stripped );}
 if ( stream == null ) {throw new HibernateException( resource + " not found" );}
 return stream;}</pre>

首先我们看下这个方法的第一行，它的作用是去掉传进来参数的第一个”/”, 为什么要去掉呢，大家发现没有 Hibernate 在指定默认文件时，用的方式是 ConfigHelper.getResourceAsStream(&#8220;/hibernate.properties&#8221;)，默认也要加上”/”, 怎么总感觉 Hibernate 有点自虐倾向，呵呵，那么 Hibernate 为什么要去掉这个”/”, 我们在看下清单 5，代码中是通过 ClassLoader 来处理资源文件的，对于 Class 和 ClassLoader 加载资源文件的区别，我想各位一定清楚吧，Class 处理资源文件前面是可以带”/”的，而通过 ClassLoader 处理资源文件前面是不能有”/”, 否则将永远找不到文件。接下来我们来看下 configration 这个类中的 parseMappingElement 方法，代码见清单 6 所示：

<a name="listing6"></a>**清单 6.parseMappingElement**

<pre class="displaycode">private void parseMappingElement(Element mappingElement, String name) {
final Attribute resourceAttribute=mappingElement.attribute("resource" );
final Attribute fileAttribute = mappingElement.attribute( "file" );
final Attribute jarAttribute = mappingElement.attribute( "jar" );
final Attribute packageAttribute = mappingElement.attribute( "package" );
final Attribute classAttribute = mappingElement.attribute( "class" );
if ( resourceAttribute != null ) {
    final String resourceName = resourceAttribute.getValue();
    log.debug( "
    session-factory config [{}] named resource [{}] for mapping", name, resourceName );
    addResource( resourceName );}
else if ( fileAttribute != null )
    {final String fileName = fileAttribute.getValue();
    log.debug( "
    session-factory config [{}] named file [{}] for mapping", name, fileName );
    addFile( fileName );}
else if ( jarAttribute != null )
    {final String jarFileName = jarAttribute.getValue();
    log.debug( "
    session-factory config [{}] named jar file [{}] for mapping", name, jarFileName );
    addJar( new File( jarFileName ) );
}else if ( packageAttribute != null ) {
    final String packageName = packageAttribute.getValue();
    log.debug( "
    session-factory config [{}] named package [{}] for mapping", name, packageName );
    addPackage( packageName );}
else if ( classAttribute != null ) {
	final String className = classAttribute.getValue();
	log.debug( "
	session-factory config [{}] named class [{}] for mapping", name, className );
	try {addAnnotatedClass( ReflectHelper.classForName( className ) );}
    catch ( Exception e ) {throw new MappingException(
       "Unable to load class [ "
        + className +
         "] declared in Hibernate configuration &lt;mapping/&gt; entry",</pre>

这个方法是被 `parseSessionFactory 方法调用，调用语句见清单 7 所示：`

<a name="listing7"></a>**清单 7\. 调用 parseMappingElement 代码块**

<pre class="displaycode"> if ( "mapping".equals( subelementName ) ) {
				 parseMappingElement( subelement, name );
			 }</pre>

我们再来看下清单 6 中的代码，通过代码我们知道 mapping 支持的属性类型有五种，分别是 resource，file，jar，package 和 class，当属性是 class 时候会调用 addAnnotatedClass 方法将该 Class 加载，并放入 List 中。当然其它的三种方式也会执行不同的操作的，这里建议应该把判断 class 这个条件提到 resource 的后边，性能会更好一些。至此 Hibernate 加载资源文件及 POJO 与数据表的映射关系我们就讲完了，我们知道映射关系中有好多属性，只要你根据以上这个思路去跟踪 Hibernate 的执行过程，一切都会明白的，我们在这里就不一一介绍了。下面我们开始讲解关于 Hibernate 事务的一些知识。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="major1.3"></a><span class="atitle">深度探索二（Hibernate 的事务管理）</span>

我们知道 Hiberante 是对 JDBC 的轻量级封装，Hiberante 本身并不具备事务管理能力，

它是将事务的管理委托给底层的 JDBC 或 JTA 来实现事物的管理和调度的。也就是说 Hibernate 支持的事务类型有这么两种 JDBC 和 JTA, 那么这两种事务之间又有什么区别呢？ Hibernate 的 JDBC 事务是基于 JDBC Connection 实现的，它的生命周期是在 Connection 之内的，也就是说 Hibernate 的 JDBC 事务是不能跨 Connection 的。而 Hibernate 的 JTA 事务类型是由 JTA 容器来管理的，JTA 容器对当前加入事务的众多 Connection 进行调度，实现其事务性要求。JTA 的事务周期可横跨多个 JDBC Connection 生命周期。了解了 Hibernate 的事务类型后，我们再来跟踪下 Hibernate 是怎样来实现这两种事务类型的，下面我们就来讲解下 Hibernate 是怎样来实现 JDBC 事务管理的。看下清单 3 中的最后第二行代码 session = sf.openSession()，在 open Session 时候 Hibernate 都做了些什么呢？它首先调用 SessionFactoryImpl 的 openSession 方法，在 openSession 方法中会获得 connection 连接，相当于 JDBC 中的 getConnection(), 然后设置了 AutoCommit 为 false，设置代码见清单 8 所示：

<a name="listing8"></a>**清单 8.SessionImpl 部分代码**

<pre class="displaycode"> SessionImpl(
	final Connection connection,
	final SessionFactoryImpl factory,
	final boolean autoclose,
	final long timestamp,
	final Interceptor interceptor,
	final EntityMode entityMode,
	final boolean flushBeforeCompletionEnabled,
	final boolean autoCloseSessionEnabled,
	final ConnectionReleaseMode connectionReleaseMode) {
		super( factory );
		this.rootSession = null;
		this.timestamp = timestamp;
		this.entityMode = entityMode;
		this.interceptor = interceptor;
		this.listeners = factory.getEventListeners();
		this.actionQueue = new ActionQueue( this );
		this.persistenceContext = new StatefulPersistenceContext( this );
		this.flushBeforeCompletionEnabled = flushBeforeCompletionEnabled;
		this.autoCloseSessionEnabled = autoCloseSessionEnabled;
		this.connectionReleaseMode = connectionReleaseMode;
		this.jdbcContext = new JDBCContext( this, connection, interceptor );
		loadQueryInfluencers = new LoadQueryInfluencers( factory );
		if ( factory.getStatistics().isStatisticsEnabled() ) {
			factory.getStatisticsImplementor().openSession();}
		if ( log.isDebugEnabled() ) {
		  log.debug( "opened session at timestamp: " + timestamp );}}</pre>

autoclose 传过来的值是 false，其实这个值是在 Settings 这个类中设置的。然后我们再来看下清单 3 中的最后一行代码 tx = session.beginTransaction() 这行代码，首先调用的是 SessionImpl 这个类的 beginTransaction 方法，代码见清单 9 所示：

<a name="listing9"></a>**清单 9.beginTransaction 代码段**

<pre class="displaycode"> errorIfClosed();
 if ( rootSession != null ) {
 // todo : should seriously consider not allowing a txn to begin from a child session
 //      can always route the request to the root session...
 log.warn( "Transaction started on non-root session" );
		 }
		 Transaction result = getTransaction();
		 result.begin();
 return result;</pre>

我们主要看下最后面的第三行和第四行代码，Transaction result = getTransaction() 我们可以看到，result 为 org.hibernate.transaction.JDBCTransaction，那么 result.begin() 调用的就是 JDBCTransaction 中的 begin 方法，这里就不列出方法代码了，这个方法的主要功能就是再一次检查一下 AutoCommit 是否为 false，如果不是就设置成 false，可以看到 Hibernate 在 openSession 和 begin transaction 中都需要检查事务是否是 auto commit 的。然后会调用 DriverManagerConnectionProvider 的 getConnection 方法，方法代码见清单 10 所示：

<a name="listing10"></a>**清单 10.getConnection 方法体**

<pre class="displaycode"> public Connection getConnection() throws SQLException {
    if ( log.isTraceEnabled() ) log.trace( "
        total checked-out connections: " + checkedOut );
    synchronized (pool) {
	    if ( !pool.isEmpty() ) {
		  int last = pool.size() - 1;
		  if ( log.isTraceEnabled() ) {
			log.trace("using pooled JDBC connection, pool size: " + last);
			checkedOut++;}
		  Connection pooled = (Connection) pool.remove(last);
    if (isolation!=null) pooled.setTransactionIsolation(isolation.intValue() );
    if ( pooled.getAutoCommit()!=autocommit ) pooled.setAutoCommit(autocommit);
    return pooled;}}
    log.debug("opening new JDBC connection");
    Connection conn = DriverManager.getConnection(url, connectionProps);
    if (isolation!=null) conn.setTransactionIsolation( isolation.intValue() );
    if ( conn.getAutoCommit()!=autocommit ) conn.setAutoCommit(autocommit);
    if ( log.isDebugEnabled() ) {
	    log.debug( "created connection to: " + url + ", Isolation Level:
	    " + conn.getTransactionIsolation() );}
    if ( log.isTraceEnabled() ) checkedOut++;return conn;}</pre>

从这段代码中更能证明我们上面说的，Hibernate 只是对 JDBC 的封装，它本身不做任何额外的事情的，通过 DriverManager 来获得 connection 连接和 JDBC 一样，我们再来看下这段代码中的一个变量 Isolation，这个变量我们在清单 4 中提起过，是关于事务隔离级别的，还记得事务隔离级别都有那些吗？我们该如何设置事务隔离级别呢？目前这个例子没有设置事务隔离级别，所以 Isolation 的值为 null，我们再来看下清单 4 中的不同事务隔离级别都表示什么意思？

TRANSACTION_READ_UNCOMMITTED 表示可以读到未提交事物的数据，也就是常说的脏读；TRANSACTION_READ_COMMITTED 表示未提交事务之前数据库中读不到该记录；TRANSACTION_REPEATABLE_READ 表示此记录不能被修改，但可以读到已经添加的记录；TRANSACTION_SERIALIZABLE 表示所有情况都加锁，事务是一个接一个的串行执行的，保证不出现脏读，不可重复读，幻影等情况，但要付出很大代价，性能不好。那么 Hibernate 是怎样来设置隔离级别的呢？我们只需在 Hibernate 配置文件中添加 hibernate.connection.isolation 这个属性就可以，它的值是数值型的，可设置成 1,2,4,8 对应上面的介绍顺序。不同的数据库默认的隔离级别是不一样的，所以要根据不同的数据库来设置它所支持的数据库隔离级别。对于 JTA 事务类型大家根据讲解 JDBC 的方法可以自己去看看 Hibernate 是怎样实现的。下面我们我开始讲解下 Hibernate 对于大数据量是采用什么方式来处理的。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="major1.4"></a><span class="atitle">深度探索三（Hibernate 对大数据量的处理）</span>

我们在项目中经常会发现一旦要访问的数据量达到一定数量级后，系统会变得异常的慢，这样的情况多数产生在批量操作中的，如果只是单纯地从用户操作界面发送的请求的话，这种情况基本不会发生的，除非你没有应用分页机制，下面我们就具体讲解下，对于这种批量的，大数据量该用什么方式来处理。现在有一个需求就是要求将年龄大于 40 岁的所有记录删除，这样的数据将会有 5 万条，通常的做法，应该像清单 11 那样：

<a name="listing11"></a>**清单 11\. 菜鸟级的大数据量删除方式**

<pre class="displaycode"> Iterator it = session.createQuery("
 from Employee where age &gt;40").iterate();
 while (it.hasNext()) {
      Employee employee = (Employee) it.next();
      session.delete(employee);
			 }
   tx.commit();
   session.close();</pre>

我们会发现以上的操作 Hibernate 执行了大量的 SQL 语句，并将这些对象做了持久化，除了消耗大量内存外，执行起来还相当的慢，有的人说，我们可以用 evict 方法呀，每执行完一次删除后将内存中的持久化对象清理掉，不错，可以这么做，这样解决了消耗大量内存的问题，但是如果跟踪 Hibernate 执行过程的话，你会发现大量的 SQL 语句依然存在，那么我们有没有一种方法只执行一条语句就删除掉那 5 万多条记录呢？当然有，我们来看下 Hibernate3 给我提供的批量操作方法，修改后的代码见清单 12 所示：

<a name="listing12"></a>**清单 12.Hibernate3 提供的批量操作方法**

<pre class="displaycode"> int it = session.createQuery("delete Employee where age &gt;40").executeUpdate();</pre>

这个方法，避免了上面提出的两个缺点，我们来看下 Hibernate 是怎样来实现这个功能的，看下 Hibernate 自带的 BasicExecutor 类的 execute 方法，方法体见清单 13 所示：

<a name="listing13"></a>**清单 13.BasicExecutor 的 execute 方法片段**

<pre class="displaycode"> st = session.getBatcher().prepareStatement( sql );
 Iterator parameterSpecifications = this.parameterSpecifications.iterator();
 int pos = 1;
 while ( parameterSpecifications.hasNext() ) {
 final ParameterSpecification paramSpec =
     ( ParameterSpecification ) parameterSpecifications.next();
 pos += paramSpec.bind( st, parameters, session, pos );}
 if ( selection != null ) {
 if ( selection.getTimeout() != null )
    {st.setQueryTimeout( selection.getTimeout().intValue() );}}
 return st.executeUpdate();
			 }</pre>

这段代码是不是有种似曾相识的感觉，呵呵，那说明你 JAVA 基础还可以，的确上面代码和我们最早用 JDBC API 来操作批量数据方式是一样的。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="major1.2"></a><span class="atitle">深度探索四（Hibernate 性能调优）</span>

我们知道 Hibernate 3 去掉了 find 方法，之所以去掉也是考虑到性能问题。因为 find 方法对于缓存只写不读，性能低下，如果您有用到 Hibernate 3 以前的版本，要注意一下。

Hibernate 3 以前版本在普通情况下持久化对象时会把该对象的属性全都加载进来，即使有些大对象我们用不到。因为延迟加载属性只能作用在类级别，Hibernate 3 允许我们在属性级别上应用延迟加载功能，这样就可以有选择的加载对象的属性了。设置也很简单，只需在属性的 get 方法上加入 @Basic(fetch = FetchType.LAZY) 这行代码就可以了，然后我们需要增强该类，通过 Ant 脚本来加强就可，很简单，就不给出代码了，要不然加入的注释将被忽略。如果你使用 hbm 格式来映射文件的话，那么在属性行加入 lazy=true 即可。以上讲的是属性级别的延迟加载，类级别的延迟加载大家也要灵活应用，毕竟它对提升性能还是很有帮助的。

我们上面也提到了关于大数据量缓存的问题，提到对于那些不常用的数据要及时清理缓存，我们可以用 session 的 clear 或 evict 方法，养成好习惯，省着一旦出现性能瓶颈时盲目。对于监控性能的一些工具大家可以自己研究一下，比如 loadrunner 等都是很不错的。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="major1.5"></a><span class="atitle">总结</span>

本文章通过新建一个 Java 工程 hibernate-deep-research，然后通过一例子带领大家逐步讲解 Hibernate 3 关于对象映射、事务管理、大数据量等方面的原理和执行过程，每一次的揭秘都是通过一个简单的测试单元来进行的。通过对 Hibernate 执行过程的跟踪来和您共同分享其中的奥秘，通过实例的方式能使大家更清楚，更容易的理解。我们知道仅仅通过一篇文章来很详细的将 Hibernate 的方方面面都阐述的很清楚，那是不可能的。本文章提供了最基本，最基础的讲解开发过程，任何复杂的事务归根结底还是源于基础，有句话是这样说的， “授之以鱼，不如授之以渔”，我想只要方向对了，知道如何下手了，就不会有大的失误，最后祝大家工作顺利。