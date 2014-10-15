title: 使微博通WordPress插件可以同步特色图片
tags:
  - solution
  - WordPress
  - wbto
  - 微博通
date: 2011-10-10 17:28:29
---

微博通官网： wbto.cn

参考API： http://www.wbto.cn/wiki/index.php/发布图片微博

从论坛下载他们的 插件文件： wbto.php，只同步文字版，不能同步特色图片，所以去看了下他们的api，研究了下源码，改一下就好了（说起来容易，研究了半天呢）

主要修改一处，添加两行代码：

<pre class="brush:php">//找到源码中的这几行

$fields['content'] = urlencode($posted-&gt;post_title.'::'.mb_strimwidth(strip_tags($excerpt),0,150,'...').$posted-&gt;guid);

//添加这两行代码，目的是获取到特色图片src
$imgThumb = wp_get_attachment_image_src( get_post_thumbnail_id( $post-&gt;ID ), 'post-thumbnail' );
$fields['imgurl'] = $imgThumb[0];

$ch = curl_init();
//修改一处，这里的url修改为upload.json，而不是原来的update.json
curl_setopt($ch, CURLOPT_URL, "http://wbto.cn/api/upload.json");
curl_setopt($ch, CURLOPT_USERPWD, "$username:$password");</pre>

修改完，就ok了，至少偶的是ok了。

<span id="more-452"></span>

插件就一个文件wbto.php，修改后全文代码如下：

<pre class="brush:php">&lt;?php
/*
Plugin Name: 微博通同步发布
Plugin URI: http://www.wbto.cn
Description: 自动把你的博客文章同步到微博通，微博通将同步至你所绑定的各个平台。
Version: 1.0
Author: yige &lt;abcwuwuwu@qq.com&gt;
Author URI: http://t.qq.com/abcwuwuwu

Date: 2011年3月17日 23:07:30
发微薄三种方式：文章自定义域weibo，文章摘要，文章内容前70个字
*/
function send_to_wbto($post_ID) {
	$username = get_option('wbto_username');
	$password = get_option('wbto_password');
	$posted = get_post($post_ID);

	$fields = array();
	$excerpt=stripslashes(get_option('weibo'));
	if(!$excerpt)
		//$excerpt=get_the_excerpt($post_ID);
		$excerpt=$posted-&gt;post_excerpt;
	if(!$excerpt)$excerpt=mb_strimwidth(strip_tags($posted-&gt;post_content),0,150,'...');
	$fields['source'] = 'wordpress';
	$fields['content'] = urlencode($posted-&gt;post_title.'::'.mb_strimwidth(strip_tags($excerpt),0,150,'...').$posted-&gt;guid);
    //不知道能否获取到图片。。。。
    $imgThumb = wp_get_attachment_image_src( get_post_thumbnail_id( $post-&gt;ID ), 'post-thumbnail' );
	$fields['imgurl'] = $imgThumb[0];

	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, "http://wbto.cn/api/upload.json");
	curl_setopt($ch, CURLOPT_USERPWD, "$username:$password");
	curl_setopt($ch, CURLOPT_FAILONERROR, TRUE);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,TRUE);
	curl_setopt($ch, CURLOPT_TIMEOUT, 10);
	curl_setopt($ch, CURLOPT_POST, TRUE);
	curl_setopt($ch, CURLOPT_POSTFIELDS, $fields);
	$result = curl_exec($ch);
	curl_close($ch);  
}
function wbto_menu() {
  add_options_page('微博通同步设置', '微博通同步', 8, __FILE__, 'wbto_options');
}

function wbto_options() {
	echo '&lt;div class="wrap"&gt;';
	echo '&lt;h2&gt;微博通同步&lt;/h2&gt;';

	echo '&lt;form method="post" action="options.php"&gt;';
	echo wp_nonce_field('update-options');

	echo '&lt;table class="form-table"&gt;';

	echo '&lt;tr valign="top"&gt;';
	echo '&lt;th scope="row"&gt;用户名 &lt;a href="http://www.wbto.cn/?app=wp"&gt;注册&lt;/a&gt;&lt;/th&gt;';
	echo '&lt;td&gt;&lt;input type="text" name="wbto_username" value="'.get_option('wbto_username').'" /&gt;&lt;/td&gt;';
	echo '&lt;/tr&gt;';

	echo '&lt;tr valign="top"&gt;';
	echo '&lt;th scope="row"&gt;密码&lt;/th&gt;';
	echo '&lt;td&gt;&lt;input type="password" name="wbto_password" value="'.get_option('wbto_password').'" /&gt;&lt;/td&gt;';
	echo '&lt;/tr&gt;';

	echo '&lt;/table&gt;';

	echo '&lt;input type="hidden" name="action" value="update" /&gt;';
	echo '&lt;input type="hidden" name="page_options" value="wbto_username,wbto_password" /&gt;';

	echo '&lt;p class="submit"&gt;';
	echo '&lt;input type="submit" name="submit" id="submit" class="button-primary" value="保存更改" /&gt;';
	echo '&lt;/p&gt;';

	echo '&lt;/form&gt;';
	echo '&lt;/div&gt;';
}
function if_send_weibo($post_ID){
	$wbto = get_post_meta($post_ID, 'wbto', true);
	if($wbto) return;
	send_to_wbto($post_ID);
	add_post_meta($post_ID, 'wbto', '本条微博已经同步到微博通，如果你要更新微博，可以删除本定义栏目，重新撰写weibo栏目，然后更新文章即可。', true);
}
add_action('admin_menu', 'wbto_menu');
add_action('publish_post', 'if_send_weibo');
?&gt;</pre>