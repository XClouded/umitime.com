title: Spring3 MVC处理方法返回值的可选类型
tags:
  - java
  - spring3.1 mvc
date: 2012-04-17 19:23:20
---

**来源：http://flyer2010.iteye.com/blog/1294400**

spring mvc处理方法支持如下的返回方式：ModelAndView, Model, ModelMap, Map,View, String, void。下面将对具体的一一进行说明：

ModelAndView

@RequestMapping(&#8220;/show1&#8243;)

public ModelAndView show1(HttpServletRequest request,

HttpServletResponse response) throws Exception {

ModelAndView mav = new ModelAndView(&#8220;/demo2/show&#8221;);

mav.addObject(&#8220;account&#8221;, &#8220;account -1&#8243;);

return mav;

}

通过ModelAndView构造方法可以指定返回的页面名称，也可以通过setViewName()方法跳转到指定的页面 ,

使用addObject()设置需要返回的值，addObject()有几个不同参数的方法，可以默认和指定返回对象的名字。

调用addObject()方法将值设置到一个名为ModelMap的类属性，ModelMap是LinkedHashMap的子类，

具体请看类。

Model 是一个接口， 其实现类为ExtendedModelMap，继承了ModelMap类。

<span id="more-720"></span>

Map

@RequestMapping(&#8220;/demo2/show&#8221;)

public Map getMap() {

Map map = new HashMap();

map.put(&#8220;key1&#8243;, &#8220;value-1&#8243;);

map.put(&#8220;key2&#8243;, &#8220;value-2&#8243;);

return map;

}

在jsp页面中可直通过${key1}获得到值, map.put()相当于request.setAttribute方法。

写例子时发现，key值包括 &#8211; . 时会有问题.

View 可以返回pdf excel等，暂时没详细了解。

String 指定返回的视图页面名称，结合设置的返回地址路径加上页面名称后缀即可访问到。

注意：如果方法声明了注解@ResponseBody ，则会直接将返回值输出到页面。

例如：

@RequestMapping(value = &#8220;/something&#8221;, method = RequestMethod.GET)

@ResponseBody

public String helloWorld()  {

return &#8220;Hello World&#8221;;

}

上面的结果会将文本&#8221;Hello World &#8220;直接写到http响应流。

@RequestMapping(&#8220;/welcome&#8221;)

public String welcomeHandler() {

return &#8220;center&#8221;;

}

对应的逻辑视图名为“center”，URL= prefix前缀+视图名称 +suffix后缀组成。

void  如果返回值为空，则响应的视图页面对应为访问地址

@RequestMapping(&#8220;/welcome&#8221;)

public void welcomeHandler() {}

此例对应的逻辑视图名为&#8221;welcome&#8221;。

小结：

1.使用 String 作为请求处理方法的返回值类型是比较通用的方法，这样返回的逻辑视图名不会和请求 URL 绑定，具有很大的灵活性，而模型数据又可以通过 ModelMap 控制。

2.使用void,map,Model 时，返回对应的逻辑视图名称真实url为：prefix前缀+视图名称 +suffix后缀组成。

3.使用String,ModelAndView返回视图名称可以不受请求的url绑定，ModelAndView可以设置返回的视图名称。