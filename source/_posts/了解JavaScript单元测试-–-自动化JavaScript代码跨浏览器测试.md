title: 了解JavaScript单元测试 – 自动化JavaScript代码跨浏览器测试
tags:
  - JavaScript
  - javascript单元测试
  - js跨浏览器测试
  - 单元测试
date: 2012-04-09 16:39:41
---

来源：高效 JavaScript 单元测试 http://www.ibm.com/developerworks/cn/opensource/os-jstesting/index.html

<a name="N1006D"><span class="atitle">一个损坏的 JavaScript 代码示例</span></a>

Web 应用程序面临的一个最大挑战是支持不同版本的 Web 浏览器。能在 Safari 上运行的 JavaScript 代码不一定能在 Windows® Internet Explorer (IE)、Firefox 或 Google Chrome 上运行。这个挑战的根源是呈现层中的 JavaScript 代码从一开始就没有进行测试。如果没有对代码进行单元测试，那么在升级或支持新浏览器后，组织可能需要花钱反复测试 Web 应用程序。本文将展示如何通过高效的 JavaScript 代码单元测试降低测试成本。

<span id="more-671"></span>

一个常见用例是登录表单 JavaScript 验证。考虑 [清单 1](listing1) 中的表单。

<a name="listing1">**清单 1\. 登录表单**</a>

<pre class="brush:xml">**&lt;form&gt;
    &lt;table&gt;
        &lt;tr&gt;
            &lt;td&gt;Username&lt;/td&gt;
            &lt;td&gt;&lt;input type="text" id="username"/&gt;&lt;/td&gt;
            &lt;td&gt;&lt;span id="usernameMessage"&gt;&lt;/span&gt;&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Password&lt;/td&gt;
            &lt;td&gt;&lt;input type="password" id="password"/&gt;&lt;/td&gt;
            &lt;td&gt;&lt;span id="passwordMessage"&gt;&lt;/span&gt;&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;&lt;input type="button" onclick="new appnamespace.
			ApplicationUtil().validateLoginForm()" value="Submit"/&gt;&lt;/td&gt;
        &lt;/tr&gt;
    &lt;/table&gt;
&lt;/form&gt;**</pre>

** **

这个表单很简单，仅包含用户名和密码字段。单击提交按钮时，将通过 `ApplicationUtil` 执行一个特定的表单验证。以下是负责验证 HTML 表单的 JavaScript 对象。[清单 2](#listing2) 显示了 `ApplicationUtil` 对象的代码。

<a name="listing2">**清单 2\. 损坏的 ApplicationUtil 对象代码**</a>

<pre class="brush:js">appnamespace = {};

appnamespace.ApplicationUtil = function() {};

appnamespace.ApplicationUtil.prototype.validateLoginForm =  function(){
	var error = true;
	document.getElementById("usernameMessage").innerText = "";
	document.getElementById("passwordMessage").innerText = "";	

	if (!document.getElementById("username").value) {
		document.getElementById("usernameMessage").innerText =
		"This field is required";
		error = false;
	}

	if (!document.getElementById("password").value) {
		document.getElementById("passwordMessage").innerText =
		"This field is required";
		error = false;
	}		

	return error;
};</pre>

在清单 2 中，`ApplicationUtil` 对象提供一个简单验证：用户名和密码字段都已填充。如果某个字段为空，就会显示一条错误消息：`This field is required`。

上面的代码能够在 Internet Explorer 8 和 Safari

5.1 上工作，但无法在 Firefox 3.6 上工作，原因是 Firefox 不支持 `innerText` 属性。通常，（上述代码和其他类似 JavaScript 代码中的）主要问题是不容易发现编写的 JavaScript 代码是不是跨浏览器兼容的。

这个问题的一个解决方案是进行自动化单元测试，检查代码是不是跨浏览器兼容。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N100B8"><span class="atitle">

JsTestDriver</span></a>

JsTestDriver library 是最好的 JavaScript 单元测试框架之一，它为 JavaScript 代码提供了跨浏览器测试。[图 1](#fig1) 展示了 JsTestDriver 的架构。

<a name="fig1">**图 1\. JsTestDriver 架构**</a>

![JsTestDriver 架构](http://www.ibm.com/developerworks/cn/opensource/os-jstesting/figure1.gif)

<div class="ibm-container ibm-alt-header dw-container-sidebar">

## jsTestDriver 是开源项目

<div class="ibm-container-body">

jsTestDriver 是 [Apache 2.0 许可](http://www.opensource.org/licenses/Apache-2.0) 下的一个开源项目，托管在 Google Code 上，后者是一个类似于 SourceForge 的项目存储库。只要使用 Open Source

Initiative 批准的 [许可](http://www.opensource.org/licenses/alphabetical)，开发人员就能在这个存储库中创建和管理公共项目。

还有许多其他 JavaScript 单元测试工具，请参见下面的 [参考资料](#resources) 部分中的其他工具，比如 Dojo Objective Harness

(DOH)。

</div>
</div>

捕获不同的浏览器之后，服务器会负责将 JavaScript 测试用例运行程序代码加载到浏览器中。可以通过命令行捕获浏览器，也可以通过将浏览器指向服务器 URL 来捕获浏览器。一旦捕获到浏览器，该浏览器就被称为从属浏览器。服务器可以加载 JavaScript 代码，在每个浏览器上执行测试用例，然后将结果返回给客户端。

客户端（命令行）需要以下两个主要项目：

1.  JavaScript 文件，即源文件和测试文件
2.  配置文件，用于组织源文件和测试文件的加载

这个架构比较灵活，允许单个服务器从网络中的其他机器捕获任意数量的浏览器。例如，如果您的代码在 Linux 上运行但您想针对另一个 Windows 机器上的 Microsoft Internet Explorer 运行您的测试用例，那么这个架构很有用。

要使用 JsTestDriver 库，请先下载最新版的 [JsTestDriver 1.3.2](http://js-test-driver.googlecode.com/files/JsTestDriver-1.3.2.jar)。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N1010B"><span class="atitle">编写单元测试代码</span></a>

现在开始编写 JavaScript 测试用例。为简单起见，我将测试以下用例：

*   用户名和密码字段均为空。
*   用户名为空，密码不为空。
*   用户名不为空，密码为空。

[清单 3](#listing3) 显示了表示 TestCase 对象的 `ApplicationUtilTest` 对象的部分代码。

<a name="listing3">**清单 3\. ApplicationUtilTest 对象代码的一部分**</a>

&nbsp;

<pre class="brush:js">ApplicationUtilTest = TestCase("ApplicationUtilTest");

ApplicationUtilTest.prototype.setUp = function () {
/*:DOC += &lt;FORM action=""&gt;&lt;table&gt;&lt;tr&gt;&lt;td&gt;Username&lt;/td&gt;&lt;td&gt;
&lt;input type="text" id="username"/&gt;&lt;/td&gt;&lt;td&gt;&lt;span id="usernameMessage"&gt;
&lt;/span&gt;&lt;/td&gt;&lt;/tr&gt;&lt;tr&gt;&lt;td&gt;Password&lt;/td&gt;&lt;td&gt;
&lt;input type="password" id="password"/&gt;&lt;/td&gt;&lt;td&gt;&lt;span id="passwordMessage"
&gt;&lt;/span&gt;&lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;&lt;/FORM&gt;*/
};

ApplicationUtilTest.prototype.testValidateLoginFormBothEmpty = function () {
	var applicationUtil = new appnamespace.ApplicationUtil();

	/* Simulate empty user name and password */
	document.getElementById("username").value = "";
	document.getElementById("password").value = "";	

	applicationUtil.validateLoginForm();

	assertEquals("Username is not validated correctly!", "This field is required",
	document.getElementById("usernameMessage").innerHTML);
	assertEquals("Password is not validated correctly!", "This field is required",
	document.getElementById("passwordMessage").innerHTML);
};</pre>

`ApplicationUtilTest` 对象通过 JsTestDriver `TestCase` 对象创建。如果您熟悉 JUnit 框架，那么您肯定熟悉 `setUp` 和 `testXXX` 方法。`setUp` 方法用于初始化测试用例。对于本例，我使用该方法来声明一个 HTML 片段，该片段将用于其他测试用例方法。

`DOC` 注释是一个 JsTestDriver 惯用语，可以用于轻松声明一个 HTML 片段。

在 `testValidateLoginFormBothEmpty` 方法中，创建了一个 `ApplicationUtil` 对象，并在测试用例方法中使用该对象。然后，代码通过检索用户名和密码的 DOM 元素并将它们的值设置为空值来模拟输入空用户名和密码。可以调用 `validateLoginForm` 方法来执行实际表单验证。最后，将

调用 `assertEquals` 来确保 `usernameMessage` 和 `passwordMessage` span 元素中的消息是正确的，即：`This field is required`。

在 JsTestDriver 中，可以使用以下构件：

*   `fail("msg")`：表明测试一定会失败，消息参数将显示为一条错误消息。
*   `assertTrue("msg", actual)`：断定实际参数正确。否则，消息参数将显示为一条错误消息。
*   `assertFalse("msg", actual)`：断定实际参数错误。否则，消息参数将显示为一条错误消息。
*   `assertSame("msg", expected, actual)`：断定实际参数与预期参数相同。否则，消息参数将显示为一条错误消息。
*   `assertNotSame("msg", expected, actual)`：断定实际参数与预期参数不相同。否则，消息参数将显示为一条错误消息。
*   `assertNull("msg", actual)`：断定参数为空。否则，消息参数将显示为一条错误消息。
*   `assertNotNull("msg", actual)`：断定实际参数不为空。否则，消息参数将显示为一条错误消息。

其他方法的代码包含其他测试用例。[清单 4](#listing4) 显示了测试用例对象的完整代码。

<a name="listing4">**清单 4\. ApplicationUtil 对象完整代码**</a>

<pre class="brush:js">**ApplicationUtilTest = TestCase("ApplicationUtilTest");

ApplicationUtilTest.prototype.setUp = function () {
/*:DOC += &lt;FORM action=""&gt;&lt;table&gt;&lt;tr&gt;&lt;td&gt;Username&lt;/td&gt;&lt;td&gt;
&lt;input type="text" id="username"/&gt;&lt;/td&gt;&lt;td&gt;&lt;span id="usernameMessage"&gt;
&lt;/span&gt;&lt;/td&gt;&lt;/tr&gt;&lt;tr&gt;&lt;td&gt;Password&lt;/td&gt;&lt;td&gt;
&lt;input type="password" id="password"/&gt;&lt;/td&gt;&lt;td&gt;&lt;span id="passwordMessage"
&gt;&lt;/span&gt;&lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;&lt;/FORM&gt;*/
};

ApplicationUtilTest.prototype.testValidateLoginFormBothEmpty = function () {
	var applicationUtil = new appnamespace.ApplicationUtil();

	/* Simulate empty user name and password */
	document.getElementById("username").value = "";
	document.getElementById("password").value = "";	

	applicationUtil.validateLoginForm();

	assertEquals("Username is not validated correctly!", "This field is required",
	document.getElementById("usernameMessage").innerHTML);
	assertEquals("Password is not validated correctly!", "This field is required",
	document.getElementById("passwordMessage").innerHTML);
};

ApplicationUtilTest.prototype.testValidateLoginFormWithEmptyUserName = function () {
	var applicationUtil = new appnamespace.ApplicationUtil();

	/* Simulate empty user name and password */
	document.getElementById("username").value = "";
	document.getElementById("password").value = "anyPassword";	

	applicationUtil.validateLoginForm();

	assertEquals("Username is not validated correctly!",
	"This field is required", document.getElementById("usernameMessage").innerHTML);
	assertEquals("Password is not validated correctly!",
	"", document.getElementById("passwordMessage").innerHTML);
};

ApplicationUtilTest.prototype.testValidateLoginFormWithEmptyPassword = function () {
	var applicationUtil = new appnamespace.ApplicationUtil();

	document.getElementById("username").value = "anyUserName";
	document.getElementById("password").value = "";	

	applicationUtil.validateLoginForm();

	assertEquals("Username is not validated correctly!",
	"", document.getElementById("usernameMessage").innerHTML);
	assertEquals("Password is not validated correctly!",
	"This field is required", document.getElementById("passwordMessage").
	innerHTML);
};**</pre>

** **

&nbsp;

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N101BA"><span class="atitle">配置用于测试的不同浏览器</span></a>

测试 JavaScript 代码的一个推荐实践是将 JavaScript 源代码和测试代码放置在不同的文件夹中。对于 [图 2](#fig2) 中的示例，我将 JavaScript 源文件夹命名为 &#8220;js-src&#8221;，将 JavaScript 测试文件夹命名为 &#8220;js-test&#8221;，它们都位于 &#8220;js&#8221; 父文件夹下。

<a name="fig2">**图 2\. JavaScript 测试文件夹结构**</a>

![JavaScript 测试文件夹结构](http://www.ibm.com/developerworks/cn/opensource/os-jstesting/figure2.jpg)

组织好源和测试文件夹后，必须提供配置文件。默认情况下，`JsTestDriver` 运行程序会寻找名为 jsTestDriver.conf 的配置文件。您可以从命令行更改配置文件名称。[清单 5](#listing5) 显示了 `JsTestDriver` 配置文件的内容。

<a name="listing5">**清单 5\. JsTestDriver 配置文件内容**</a>

<pre class="displaycode">server: http://localhost:9876

load:
  - js-src/*.js
  - js-test/*.js</pre>

配置文件采用 YAML 格式。`server` 指令指定测试服务器的地址，`load` 指令指出了将哪些 JavaScript 文件加载到浏览器中以及加载它们的顺序。

现在，我们将在 IE、Firefox 和 Safari 浏览器上运行测试用例类。

要运行测试用例类，需要启动服务器。您可以使用以下命令行启动 `JsTestDriver` 服务器：

<pre class="brush:shell">java -jar JsTestDriver-1.3.2.jar --port 9876 --browser "[Firefox Path]","[IE Path]","[Safari Path]"</pre>

使用这个命令行，服务器将以 Port 9876 启动，捕获您的机器上的 Firefox、IE 和 Safari 浏览器。

启动并捕获浏览器后，可以通过以下命令行运行测试用例类：

<pre class="brush:shell">java -jar JsTestDriver-1.3.2.jar --tests all</pre>

运行命令后，您将看到第一轮结果，如 [清单 6](#listing6) 所示。

<a name="listing6">**清单 6\. 第一轮结果**</a>

<pre class="displaycode">Total 9 tests (Passed: 6; Fails: 3; Errors: 0) (16.00 ms)
  Firefox 3.6.18 Windows: Run 3 tests (Passed: 0; Fails: 3; Errors 0) (8.00 ms)
    ApplicationUtilTest.testValidateLoginFormBothEmpty failed (3.00 ms):
	AssertError: Username is not validated correctly! expected "This field
	is required" but was "" Error("Username is not validated correctly!
	expected \"This field is required\" but was \"\"")@:0()@http://localhost
	:9876/test/js-test/TestApplicationUtil.js:16

    ApplicationUtilTest.testValidateLoginFormWithEmptyUserName failed (3.00 ms):
	AssertError: Username is not validated correctly! expected "This field is
	required" but was "" Error("Username is not validated correctly! expected
	\"This field is required\" but was \"\"")@:0()@http://localhost:9876/test
	/js-test/TestApplicationUtil.js:29

    ApplicationUtilTest.testValidateLoginFormWithEmptyPassword failed (2.00 ms):
	AssertError: Password is not validated correctly! expected "This field is
	required" but was "" Error("Password is not validated correctly! expected
	\"This field is required\" but was \"\"")@:0()@http://localhost:9876/test/
	js-test/TestApplicationUtil.js:42

  Safari 534.50 Windows: Run 3 tests (Passed: 3; Fails: 0; Errors 0) (2.00 ms)
  Microsoft Internet Explorer 8.0 Windows: Run 3 tests (Passed: 3; Fails: 0;
  Errors 0) (16.00 ms)
Tests failed: Tests failed. See log for details.</pre>

注意，在清单 6 中，主要问题出在 Firefox 上。测试在 Internet Explorer 和 Safari 上均可顺利运行。

<div class="ibm-alternate-rule">

* * *

</div>

[回页首](#ibm-pcon)

<a name="N1022D"><span class="atitle">修复 JavaScript 代码并重新运行测试用例</span></a>

我们来修复损坏的 JavaScript 代码。我们将使用 `innerHTML` 替代 `innerText`。[清单 7](#listing7) 显示了修复后的 `ApplicationUtil` 对象代码。

<a name="listing7">**清单 7\. 修复后的 ApplicationUtil 对象代码**</a>

<pre class="brush:js">appnamespace = {};

appnamespace.ApplicationUtil = function() {};

appnamespace.ApplicationUtil.prototype.validateLoginForm =  function(){
	var error = true;
	document.getElementById("usernameMessage").innerHTML = "";
	document.getElementById("passwordMessage").innerHTML = "";	

	if (!document.getElementById("username").value) {
		document.getElementById("usernameMessage").innerHTML =
		"This field is required";
		error = false;
	}

	if (!document.getElementById("password").value) {
		document.getElementById("passwordMessage").innerHTML =
		"This field is required";
		error = false;
	}		

	return error;
};</pre>

使用 `--test all` 命令行参数重新运行测试用例对象。[清单 8](#listing8) 显示了第二轮运行结果。

<a name="listing8">**清单 8\. 第二轮运行结果**</a>

<pre class="displaycode">Total 9 tests (Passed: 9; Fails: 0; Errors: 0) (9.00 ms)
  Firefox 3.6.18 Windows: Run 3 tests (Passed: 3; Fails: 0; Errors 0) (9.00 ms)
  Safari 534.50 Windows: Run 3 tests (Passed: 3; Fails: 0; Errors 0) (5.00 ms)
  Microsoft Internet Explorer 8.0 Windows: Run 3 tests (Passed: 3; Fails: 0; Errors 0)
  (0.00 ms)</pre>

如清单 8 所示，JavaScript 代码现在在 IE、Firefox 和 Safari 上都能正常运行。

<div class="ibm-alternate-rule">

* * *

</div>

&nbsp;

<a name="N10269"><span class="atitle">结束语</span></a>

在本文中，您了解了如何使用一个最强大的 JavaScript 单元测试工具 (JsTestDriver) 在不同的浏览器上测试 JavaScript 应用程序代码。还了解了什么是 JsTestDriver，如何配置它，以及如何在 Web 应用程序中使用它来确保应用程序的 JavaScript 代码的质量和可靠性。