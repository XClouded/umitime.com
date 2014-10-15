title: Spring3 mvc方法入参的介绍
tags:
  - java
  - spring3.1 mvc
date: 2012-04-17 19:28:23
---

**来源： http://flyer2010.iteye.com/blog/1294402**

Spring MVC 框架中你可以按任意顺序定义请求处理方法的入参（除了 Errors 和 BindingResult 必须紧跟在命令对象/表单参数后面以外），Spring MVC 会根据反射机制自动将对应的对象通过入参传递给请求处理方法。这种机制让开发者完全可以不依赖 Servlet API 开发控制层的程序，当请求处理方法需要特定的对象时，仅仅需要在参数列表中声明入参即可，不需要考虑如何获取这些对象，下面列举下spring mvc支持的处理方法参数。

<span id="more-724"></span>

* Java 基本数据类型 和String

默认情况下将按名称匹配的方式绑定到 URL 参数上，可以通过 @RequestParam 注解改变默认的绑定规则

* Request or response objects (Servlet API).

Request或者response 对象 (Servlet API)，选择任意的request或者response类型，例如：ServletRequest 、 HttpServletRequest.

* Session object (Servlet API).

session对象(Servlet API)：构成类型有HttpSession，这个类型的参数增强了当前通信的session，因此，这个参数永远不会null.

注意：session通道不是线程安全的，特别是在Servlet环境下，如果允许多个请求访问session的情况下建议将AnnotationMethodHandlerAdapter的synchronizeOnSession属性设置为&#8221;true&#8221;

* org.springframework.web.context.request.WebRequest or org.springframework.web.context.request.NativeWebRequest. Allows for generic request parameter access as well as request/session attribute access, without ties to the native Servlet/Portlet API.

使用spring封装的WebRequest或者NativeWebRequest，与使用原生的Servlet/Portlet API不相上下。

*.java.util.Locale

用于获得当前的请求区域。

*.java.io.InputStream / java.io.Reader for access to the request&#8217;s content. This value is the raw InputStream/Reader as exposed by the Servlet API.

值为原始的InputStream/Reader被Servlet API 可以借此访问 request 的内容

*.java.io.OutputStream / java.io.Writer for generating the response&#8217;s content. This value is the raw OutputStream/Writer as exposed by the Servlet API.

用于产生响应的内容,以此操作 response 的内容

*.java.security.Principal containing the currently authenticated user.

包含当前认证的用户。

*.@PathVariable annotated parameters for access to URI template variables

用于注解参数对应到地址栏的变量参数

例如：

@RequestMapping(value=&#8221;/owners/{ownerId}&#8221;, method=RequestMethod.GET)

public String findOwner(@PathVariable(&#8220;ownerId&#8221;) String ownerId, Model model) {

// implementation omitted

}

访问地址&#8221;/owners/{ownerId}&#8221; 指定了访问变量名称为ownerId，当控制器处理这个请求的时候，ownerId的值被设置到请求的地址栏，

例如：当请求来自/owners/fred，值fred被绑定到访问方法的参数String ownerId

*.@RequestParam

用于注解参数到Servlet 请求的参数，参数值对应到控制器方法中声明的参数。

<pre>@RequestMapping(method = RequestMethod.GET)
    public String setupForm(@RequestParam("id") int petId, ModelMap model) {
        Pet pet = this.clinic.loadPet(petId);
        model.addAttribute("pet", pet);
        return "petForm";
    }</pre>

例如：请求的参数id对应到方法中的petId.

*.@RequestHeader

注解参数用于具体的Servlet请求的http头部，参数值被转换到生命的方法参数类型。

请求头部信息例如：Connection，Accept-Language、Accept-Charset等

<pre>public void displayHeaderInfo(@RequestHeader("Connection") String connection,@RequestHeader("Accept-Encoding") String encoding) {
        System.out.println("connection:" + connection);
        System.out.println("Accept-Encoding:" + encoding);
}</pre>

*.@RequestBody

*.HttpEntity<!--?-->

*.java.util.Map / org.springframework.ui.Model / org.springframework.ui.ModelMap

它绑定 Spring MVC 框架中每个请求所创建的潜在的模型对象，它们可以被 Web 视图对象访问（如 JSP）.用于增强传递到web视图页面的model，也就是说他们作为方法的参数后，页面视图就可以直接根据key来取得相应的值。

*.org.springframework.validation.Errors / org.springframework.validation.BindingResult

BindingResult继承于Errors，为属性列表中的命令/表单对象的校验结果,返回错误信息到页面，设置为方法参数后，在视图页面上可以直接获取。

BindingResult result必须和@ModelAttribute同时使用，并且参数要紧挨着。否则会报错。Errors/BindingResult argument declared without preceding model attribute异常。

如下例：1、2报错; 3正常。

<pre>//test:1 
@RequestMapping(params="method=result1")
public String processSubmit1(User user,
    Model model, BindingResult result) {
    return "/demo1/listBoard";
}

//test:2 
@RequestMapping(params="method=result2")
public String processSubmit2(@ModelAttribute("user") User user,
        Model model, BindingResult result) {
    return "/demo1/listBoard";
}

//test:3 
@RequestMapping(params="method=result3")
public String processSubmit3(@ModelAttribute("user") User user,
        BindingResult result,Model model) {
    return "/demo1/listBoard";
}

* org.springframework.web.bind.support.SessionStatus status handle for marking form processing as complete, which triggers the cleanup of session attributes that have been indicated by the @SessionAttributes annotation at the handler type level.</pre>

可以通过该类型 status 对象显式结束表单的处理，这相当于触发 session 清除其中的通过 @SessionAttributes 定义的属性

例子：使用 SessionStatus 控制 Session 级别的模型属性

<pre>@RequestMapping(method = RequestMethod.POST)
public String processSubmit(@ModelAttribute Owner owner,
BindingResult result, SessionStatus status) {//&lt;——①
    new OwnerValidator().validate(owner, result);
    if (result.hasErrors()) {
        return "ownerForm";
    }
    else {
        this.clinic.storeOwner(owner);
        status.setComplete();//&lt;——②
        return "redirect:/owner.do?ownerId=" + owner.getId();
    }
}</pre>

processSubmit() 方法中的 owner 表单对象将绑定到 ModelMap 的“owner”属性中，result 参数用于存放检验 owner 结果的对象，

而 status 用于控制表单处理的状态。在 ② 处，我们通过调用 status.setComplete() 方法，

该 Controller 所有放在 session 级别的模型属性数据将从 session 中清空。