title: wordpress插件 – 实现文章发布的固定元数据信息添加
tags:
  - solution
  - WordPress
date: 2011-10-13 15:16:45
---

学习一款支付宝插件（http://www.izt.me/wordpress-alipay.html），在发布时候可以添加商品信息的功能。

通过改动其中一个文件meta-box.php来实现自己想要的插件功能。

WP插件个人通过实践以后总结为两步实现的套路：

1、定义自己的php功能函数。

2、通过wp的api挂载此函数 。到此完工。

一句话就是插件功能自己定义好函数，然后告知wp在合适的地方执行就行了。

<span id="more-457"></span>

于是实现一个在发布页面可以填写一个文章相关外链，并在查看文章页面使用到此链接。实践开始：

先在wp的plugins目录新建子目录为 link-mutual，目录下建立一个文件 link-mutual.php。

然后copy支付宝插件的meta-box.php的内容到link-mutual.php。

剩下的就是增删改一些代码，保存后去启用插件就ok了：

<pre class="brush:php">&lt;?php

/**
  第二步通过API挂载自己的函数， 完成。函数定义语句可以放到调用之后。
  API详情看wordpress官网介绍。如果仔细点，可以发现参数代表的意义。
  所以我没去看官网的API，直接在原插件基础上分析并进行改删。
  特别注意命名的唯一性，各插件命名是不能重复的，因为有报错过的经历。
*/
//发布或更新文章显示的表单域字段，调用函数vasee_create_meta_box
add_action( 'admin_menu', 'vasee_create_meta_box' );
//保存文章时候触发函数vasee_save_meta_data
add_action( 'save_post', 'vasee_save_meta_data' );

/**
  第一步，以下的代码就是定义自己用到的一些帮助函数，及UI输出。
*/
//vasee-post-meta-boxes命名也要注意，因为没改到，插件虽然正常启用，但是页面没出现表单
function vasee_create_meta_box() {
	add_meta_box( 'vasee-post-meta-boxes','外链到Vasee', 'vasee_post_meta_boxes', 'post', 'normal', 'high' );
	add_meta_box( 'vasee-page-meta-boxes', '外链到Vasee', 'vasee_page_meta_boxes', 'page', 'normal', 'high' );
}
//定义字段，比如以下就是个 input name='vasee_link' type="text"
function vasee_post_boxes() {
	$meta_boxes = array(
        array(
            "name"             =&gt; "vasee_link",
            "title"            =&gt; "链接到Vasee",
            "desc"             =&gt; "填写一个完整的Vasee活动页面，http://开头",
            "type"             =&gt;    "text",
            "capability"       =&gt; "manage_options"
        )
    );
	return apply_filters( 'vasee_post_boxes', $meta_boxes );
}
function vasee_page_boxes() {
	$meta_boxes = array(
        array(
            "name"             =&gt; "vasee_link",
            "title"            =&gt; "链接到Vasee",
            "desc"             =&gt; "填写一个完整的Vasee活动页面，http://开头",
            "type"             =&gt;    "text",
            "capability"       =&gt; "manage_options"
        )
    );
	return apply_filters( 'vasee_page_boxes', $meta_boxes );
}
function vasee_post_meta_boxes() {
	global $post;
	$meta_boxes = vasee_post_boxes();
?&gt;
	&lt;table class="form-table"&gt;
	&lt;?php foreach ( $meta_boxes as $meta ) :
		$value = get_post_meta( $post-&gt;ID, $meta['name'], true );
		if ( $meta['type'] == 'text' )
			vasee_meta_text_input( $meta, $value );
	endforeach; ?&gt;
	&lt;/table&gt;
&lt;?php
}
function vasee_page_meta_boxes() {
	global $post;
	$meta_boxes = vasee_page_boxes();
?&gt;
	&lt;table class="form-table"&gt;
	&lt;?php foreach ( $meta_boxes as $meta ) :
		$value = stripslashes( get_post_meta( $post-&gt;ID, $meta['name'], true ) );
		if ( $meta['type'] == 'text' )
			vasee_meta_text_input( $meta, $value );
		endforeach; ?&gt;
	&lt;/table&gt;
&lt;?php
}
function vasee_meta_text_input( $args = array(), $value = false ) {
	extract( $args ); ?&gt;
	&lt;tr&gt;
		&lt;th style="width:10%;"&gt;
			&lt;label for="&lt;?php echo $name; ?&gt;"&gt;&lt;?php echo $title; ?&gt;&lt;/label&gt;
		&lt;/th&gt;
		&lt;td&gt;
			&lt;input type="text" name="&lt;?php echo $name; ?&gt;" id="&lt;?php echo $name; ?&gt;" value="&lt;?php echo wp_specialchars( $value, 1 ); ?&gt;" size="30" tabindex="30" style="width: 97%;" /&gt;
			&lt;input type="hidden" name="&lt;?php echo $name; ?&gt;_noncename" id="&lt;?php echo $name; ?&gt;_noncename" value="&lt;?php echo wp_create_nonce( plugin_basename( __FILE__ ) ); ?&gt;" /&gt;
			&lt;br /&gt;
			&lt;p class="description"&gt;&lt;?php echo $desc; ?&gt;&lt;/p&gt;
		&lt;/td&gt;
	&lt;/tr&gt;
	&lt;?php
}
function vasee_save_meta_data( $post_id ) {
	if ( 'page' == $_POST['post_type'] )
		$meta_boxes = array_merge( vasee_page_boxes() );
	else
		$meta_boxes = array_merge( vasee_post_boxes() );
		foreach ( $meta_boxes as $meta_box ) :
		if ( !wp_verify_nonce( $_POST[$meta_box['name'] . '_noncename'], plugin_basename( __FILE__ ) ) )
			return $post_id;
		if ( 'page' == $_POST['post_type'] &amp;&amp; !current_user_can( 'edit_page', $post_id ) )
			return $post_id;
		elseif ( 'post' == $_POST['post_type'] &amp;&amp; !current_user_can( 'edit_post', $post_id ) )
			return $post_id;
		$data = stripslashes( $_POST[$meta_box['name']] );
		if ( get_post_meta( $post_id, $meta_box['name'] ) == '' )
			add_post_meta( $post_id, $meta_box['name'], $data, true );
		elseif ( $data != get_post_meta( $post_id, $meta_box['name'], true ) )
			update_post_meta( $post_id, $meta_box['name'], $data );
		elseif ( $data == '' )
			delete_post_meta( $post_id, $meta_box['name'], get_post_meta( $post_id, $meta_box['name'], true ) );
	endforeach;
}
?&gt;</pre>