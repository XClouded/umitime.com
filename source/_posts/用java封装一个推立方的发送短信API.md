title: 用java封装一个推立方的发送短信API
tags:
  - code
  - sms api
  - 推立方
  - 短信发送
date: 2012-03-01 10:41:13
---

推立方发送短信，提供的是一个http uri地址，可以使用get或post方式提交短信内容。

具体的可以去看推立方的文档。PS一句，推立方发短信蛮贵的。。。1毛一条。

其中用到第三方JSON解析包FastJSON，可以google得之。其余的上代码。

<span id="more-624"></span>

<pre class="brush:java">package com.cssor.util;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

public class SMSUtil {

/*	调用地址：
	UTF8：http://www.tui3.com/api/send/?k=发送密钥&amp;r=执行结果格式&amp;p=短信产品id&amp;t=接收手机号&amp;c=发送内容
	GBK：http://www.tui3.com/api/send/?k=发送密钥&amp;r=执行结果格式&amp;p=短信产品id&amp;t=接收手机号&amp;cn=发送内容
	参数说明：
	    k:  32位随机值，由系统自动产生，该值请到产品配置中获取。该值请注意安全保管，勿泄露。
	    p: 1:“推信”; 2:“推信DIY”。
	    r: 调用后返回的结果格式。json(默认)  xml
	    t:接收手机列表，多个手机号码请用,分隔(最多支持100个手机号)
	    c: 发送内容（utf8格式编码） cn: 发送内容(gbk格式编码)
*/
	private static final String API_KEY = "d08c3*******************38b374";
	private static final String PRODUCT_TYPE = "1"; /*1:“推信”; 2:“推信DIY”。*/
	private static final String RESULT_FORMAT = "json";

	private static final String API_URL = "http://www.tui3.com/api/send/";

	/**
	 * 向一个手机号码发送短信
	 * @param mobile 手机号码
	 * @param content 短信内容
	 */
	public static void sendSMS(String mobile, String content) {
		String[] mobiles = {mobile};
		sendSMS(mobiles, content);
	}

	/**
	 * 向多个手机发送短信，群发。
	 * @param mobiles 手机号码数组
	 * @param content 短信内容
	 */
	public static void sendSMS(String[] mobiles, String content) {
		/*推立方最多支持100个手机号*/
		if(mobiles.length &gt; 99) {
			throw new RuntimeException("推立方最多支持100个手机号，请检查手机号是否过多。");
		}

		try {
			URL url = new URL(API_URL);

			HttpURLConnection connection = (HttpURLConnection) url.openConnection();
			HttpURLConnection.setFollowRedirects(true);

			connection.setDoInput(true);
			connection.setDoOutput(true);

			connection.setRequestMethod("POST");
			connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded; charset=utf-8");

			content = URLEncoder.encode(content, "utf-8");
			String query = "k=" + API_KEY + "&amp;r=" + RESULT_FORMAT + "&amp;p=" + PRODUCT_TYPE
					+ "&amp;c=" + content + "&amp;t=" + Arrays.toString(mobiles).replaceAll("[\\[\\]\\s]", "");

			DataOutputStream output = new DataOutputStream(
					connection.getOutputStream());
			output.writeBytes(query);

			int resp_code = connection.getResponseCode();

			if(resp_code != 200) {
				throw new RuntimeException("推立方服务器返回码不是200 ok，请求响应错误！");
			}

			BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));

			String result = "", r;

			while ((r = reader.readLine()) != null) {
				result += r;
			}
			reader.close();

			JSONObject resultJSON = JSON.parseObject(result);
			if(resultJSON.getIntValue("err_code") != 0) {
				throw new RuntimeException(resultJSON.getString("err_msg"));
			}
		} catch (IOException e) {
			throw new RuntimeException("网路IO错误。", e);
		}
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		Date date = new Date();
		SimpleDateFormat dateFormat = new SimpleDateFormat("MM-dd HH:mm:ss");

		String[] mobiles = {"158********","135********"};
		String content = "t3test 这个短信同时发给多个人，编号002 - cssor.com - 发送时间：" + dateFormat.format(date);

		SMSUtil.sendSMS(mobiles, content);
	}

}</pre>

代码进化版，当然有些地方还是写死上去的，不够灵活，不过满足当前业务为重，新增短信余额提醒功能，并把短信发送作为新线程执行，即使发短信过程产生错误也不影响主线程功能：

<pre class="brush:js">package com.cssor.util;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.PropertyResourceBundle;
import java.util.ResourceBundle;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

public class SMSUtil {

//	调用地址：
//	UTF8：http://www.tui3.com/api/send/?k=发送密钥&amp;r=执行结果格式&amp;p=短信产品id&amp;t=接收手机号&amp;c=发送内容
//	GBK：http://www.tui3.com/api/send/?k=发送密钥&amp;r=执行结果格式&amp;p=短信产品id&amp;t=接收手机号&amp;cn=发送内容
	/*
	    参数说明：
		k:  32位随机值，由系统自动产生，该值请到产品配置中获取。该值请注意安全保管，勿泄露。
		 p: 1:“推信”; 2:“推信DIY”。
		 r: 调用后返回的结果格式。json(默认)  xml
		 t:接收手机列表，多个手机号码请用,分隔(最多支持100个手机号)
		c: 发送内容（utf8格式编码） cn: 发送内容(gbk格式编码)
	 */
	private static final String API_URL = "http://www.tui3.com/api/send/";
	private static final String QUERY_URL = "http://www.tui3.com/api/query/";

	private static final String API_KEY = "d08c3********************";
	private static final String RESULT_FORMAT = "json";
	private static String PRODUCT_TYPE; //1:“推信”; 2:“推信DIY”。

	private static String MOBILE_REMIND;
	private static int MIN_BALANCE;

	static {
		try {
			ResourceBundle bundle = PropertyResourceBundle.getBundle("smsconfig");
			MOBILE_REMIND = bundle.getString("mobile_remind");
			MIN_BALANCE = Integer.valueOf(bundle.getString("min_balance"));
			PRODUCT_TYPE = bundle.getString("product_type");	

		} catch (Exception e) {
			e.printStackTrace();
			MOBILE_REMIND = "135********";
			MIN_BALANCE = 30;

			PRODUCT_TYPE = "1"; //1:“推信”; 2:“推信DIY”。
		}
	}

	/**
	 * 向一个手机号码发送短信
	 * @param mobile 手机号码
	 * @param content 短信内容
	 */
	public static void sendSMS(String mobile, String content) {
		String[] mobiles = {mobile};
		sendSMS(mobiles, content);
	}

	/**
	 * 向多个手机发送短信，群发。
	 * @param mobiles 手机号码数组
	 * @param content 短信内容
	 */
	public static void sendSMS(String[] mobiles, String content) {
		ExecutorPool.getInstance().getExecutorService().execute(new SMSRunner(mobiles, content));
	}

	/**
	 * 发送短信核心方法，同时增加了短信余额预警通知
	 * @param mobiles 手机号数组
	 * @param content  短信内容
	 */
	public static void coreSendSMS(String[] mobiles, String content) {
		//推立方最多支持100个手机号
		if(mobiles.length &gt; 99) {
			throw new IllegalArgumentException("推立方最多支持100个手机号，请检查手机号是否过多。");
		}
		if(mobiles.length == 0) {
			throw new IllegalArgumentException("至少需要一个手机号码。");
		}

		try {
			content = URLEncoder.encode(content, "utf-8");

			Map&lt;String, String&gt; params = new HashMap&lt;String, String&gt;();
			params.put("k", API_KEY);
			params.put("r", RESULT_FORMAT);
			params.put("p", PRODUCT_TYPE);
			params.put("c", content);
			params.put("t", Arrays.toString(mobiles).replaceAll("[\\[\\]\\s]", ""));

			Response response = SMSUtil.postRequset(API_URL, params);

			if(response.getStatus() != 200) {
				throw new RuntimeException("推立方服务器返回码不是200 ok，请求响应错误！");
			}

			JSONObject resultJSON = JSON.parseObject(response.getContent());
			if(resultJSON.getIntValue("err_code") != 0) {
				throw new RuntimeException(resultJSON.getString("err_msg"));
			}

		} catch (IOException e) {
			throw new RuntimeException("网路IO错误。", e);
		}
	}

	/**
	 * 短信余额将完预警通知
	 */
	public static void notifyBalance() {
		int balance = queryBalance();
		if(balance != -1 &amp;&amp; balance &lt;= MIN_BALANCE) {
			//短信内容修改，注意要符合推立方备案格式
			String tip = "推立方短信可发送仅剩余" + balance + "条，请尽快充值！";
			String[] mobiles = {MOBILE_REMIND};
			SMSUtil.coreSendSMS(mobiles, tip);
		}
	}

	/**
	 * 短信余额查询
	 * @return
	 */
	public static int queryBalance() {
		Map&lt;String, String&gt; params = new HashMap&lt;String, String&gt;();
		params.put("k", API_KEY);
		params.put("r", RESULT_FORMAT);

		try {
			Response response = SMSUtil.postRequset(QUERY_URL, params);

			JSONObject resultJSON = JSON.parseObject(response.getContent());

			if(resultJSON.getIntValue("err_code") != 0) {
				throw new RuntimeException(resultJSON.getString("err_msg"));
			}

			return resultJSON.getIntValue("count" + PRODUCT_TYPE);
		} catch (Exception e) {
			e.printStackTrace();
			return -1;
		}
	}

	/**
	 * 简单使用，更复杂可以封装为Response对象
	 * @param uri
	 * @param params
	 * @return
	 * @throws IOException
	 */
	public static Response postRequset(String uri, Map&lt;String, String&gt; params) throws IOException {

		URL url = new URL(uri);

		HttpURLConnection connection = (HttpURLConnection) url.openConnection();
		HttpURLConnection.setFollowRedirects(true);

		connection.setDoInput(true);
		connection.setDoOutput(true);

		connection.setRequestMethod("POST");
		connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded; charset=utf-8");

		StringBuffer query = new StringBuffer(512);
		for(String key : params.keySet()) {
			query.append("&amp;" + key + "=" + params.get(key));
		}
		query.delete(0, 1);

		DataOutputStream output = new DataOutputStream(
				connection.getOutputStream());
		output.writeBytes(query.toString());

		int resp_code = connection.getResponseCode();

		if(resp_code != 200) {
			throw new RuntimeException("推立方服务器返回码不是200 ok，请求响应错误！");
		}
 		// get ready to read the response from the url
		BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));

		String result = "", r;

		// read in each character until end-of-stream is detected
		while ((r = reader.readLine()) != null) {
			result += r;
		}
		reader.close();

		Response response = new Response();
		response.setStatus(resp_code);
		response.setContent(result);

		return response;
	}

	public static class Response {
		private int status;
		private String content;

		public int getStatus() {
			return status;
		}
		public void setStatus(int status) {
			this.status = status;
		}
		public String getContent() {
			return content;
		}
		public void setContent(String content) {
			this.content = content;
		}
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
	    System.err.println("余额： " + SMSUtil.queryBalance());;

	}

}</pre>

SMSRuner.java

<pre class="brush:js">public class SMSRunner implements Runnable {

	private String[] mobiles;
	private String content;
	/**
	 * 
	 * 开新线程发送短信的构造方法
	 * @param mobiles 1个或多个手机号数组
	 * @param content  短信内容
	 */
	public SMSRunner(String[] mobiles, String content) {
		this.mobiles = mobiles;
		this.content = content;
	}

	@Override
	public void run() {
		System.err.println("SMS 短信发送。。。。。。");
		SMSUtil.coreSendSMS(this.mobiles, this.content);
		SMSUtil.notifyBalance();
	}
}</pre>

线程池简单实现，一个同事写的，偶直接拿来用了：

<pre class="brush:js">import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorPool {
	 private static ExecutorPool instance;
     // 连接池大小
     private int POOL_SIZE = 2;
     private ExecutorService executorService;

     private ExecutorPool() {
             initExecutors();
     }

     public static ExecutorPool getInstance() {
             if (instance == null) {
                     instance = new ExecutorPool();
             }
             return instance;
     }

     public ExecutorService getExecutorService() {
             return executorService;
     }

     public void initExecutors() {
             try {
                     executorService = Executors.newFixedThreadPool(POOL_SIZE);
             } catch (Exception e) {
                  //异常处理
             }
     }

}</pre>

需要在class根目录增加一个smsconfig.properties文件，用IDE的话建立在src根目录会自动编译到class目录，内容如下：

<pre class="brush:plain">#被通知需要充值的人的电话
mobile_remind=135********
#短信最小剩余条数，小于30条则发送短信通知充值
min_balance=30
#使用的产品类型 1:“推信”; 2:“推信DIY”
product_type=1</pre>

&nbsp;