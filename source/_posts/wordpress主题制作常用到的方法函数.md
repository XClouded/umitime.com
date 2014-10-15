title: wordpress主题制作常用到的方法函数
tags:
  - code
  - WordPress
  - wordpress theme
  - wordpress主题
date: 2012-05-10 21:49:58
---

_页面_编码：&lt;meta charset=&#8221;&lt;?php bloginfo( &#8216;charset&#8217; ); ?&gt;&#8221; /&gt;;

页面标题：wp_title( &#8216;|&#8217;, true, &#8216;right&#8217; );

博客名称：bloginfo( &#8216;name&#8217; );

博客描述：&lt;?php bloginfo( &#8216;description&#8217; ); ?&gt;

博客样式：&lt;link rel=&#8221;stylesheet&#8221; media=&#8221;all&#8221; href=&#8221;&lt;?php bloginfo( &#8216;stylesheet_url&#8217; ); ?&gt;&#8221; /&gt;

博客主页地址：&lt;a href=&#8221;&lt;?php echo home_url( &#8216;/&#8217; ); ?&gt;&#8221;&gt;&lt;/a&gt;

博客主题地址：&lt;link rel=&#8221;shortcut icon&#8221; type=&#8221;image/x-icon&#8221; href=&#8221;&lt;?php bloginfo(&#8216;template_url&#8217;)?&gt;/img-res/favicon.ico&#8221; /&gt;

博客导航，一步生成 :&lt;?php wp_nav_menu( array( &#8216;container_class&#8217; =&gt; &#8216;menu-header&#8217;, &#8216;theme_location&#8217; =&gt; &#8216;primary&#8217; ) ); ?&gt;

全局当前页码：global $page, $paged;

wordpress官方列举的全局变量列表： http://codex.wordpress.org/Global_Variables

<span id="more-756"></span>

自定义输出分类：

<pre class="brush:php">&lt;?php
    $args=array(
        'orderby' =&gt; 'name',
        'order' =&gt; 'ASC'
    );
    $categories = get_categories($args);
    foreach($categories as $category) {
        echo '&lt;a href="' . get_category_link( $category-&gt;term_id ) . '" title="' . sprintf( __( "View all posts in %s" ), $category-&gt;name ) . '" ' . '&gt;' . $category-&gt;name.'&lt;/a&gt;';
    }
?&gt;</pre>

搜索表单一步生成：&lt;?php get_search_form(); ?&gt;

在Loop中自定义文章显示：

<pre class="brush:php">&lt;article  id="post-&lt;?php the_ID(); ?&gt;" &lt;?php post_class(); ?&gt;&gt;
    &lt;h2&gt;&lt;a href="&lt;?php the_permalink(); ?&gt;" title="&lt;?php printf( esc_attr__( 'Permalink to %s', 'twentyten' ), the_title_attribute( 'echo=0' ) ); ?&gt;" rel="bookmark"&gt;&lt;?php the_title(); ?&gt;&lt;/a&gt;&lt;/h2&gt;

    &lt;section&gt;
        &lt;?php if ( is_archive() || is_search() ) : // Only display excerpts for archives and search. ?&gt;
            &lt;?php the_excerpt(); ?&gt;
        &lt;?php else : ?&gt;
            &lt;?php the_content( __( 'Continue reading &lt;span class="meta-nav"&gt;&amp;rarr;&lt;/span&gt;', 'twentyten' ) ); ?&gt;
            &lt;?php wp_link_pages( array( 'before' =&gt; '&lt;div class="page-link"&gt;' . __( 'Pages:', 'twentyten' ), 'after' =&gt; '&lt;/div&gt;' ) ); ?&gt;
        &lt;?php endif; ?&gt;

        &lt;p class="t-c"&gt;
            &lt;a class="btn" href="&lt;?php the_permalink(); ?&gt;"&gt;继续阅读~&lt;/a&gt;
            &lt;a class="btn" href="&lt;?php comments_link(); ?&gt;"&gt;&lt;?php comments_number( '参与评论~', '评论(%)', '评论(%)' ); ?&gt;&lt;/a&gt;
        &lt;/p&gt;
    &lt;/section&gt;

    &lt;aside&gt;
        &lt;?php edit_post_link( __( '[Edit]', 'twentyten' ), '&lt;span class="r-f"&gt;', '&lt;/span&gt;' ); ?&gt;
        &lt;time&gt;&lt;?php the_time('Y.m.d');?&gt;&lt;/time&gt;
        &lt;span&gt;分类: &lt;?php the_category(', '); ?&gt;&lt;/span&gt;
        &lt;span&gt;标签: &lt;?php the_tags('',',',''); ?&gt;
        &lt;/span&gt;
    &lt;/aside&gt;
&lt;/article&gt;</pre>

不用插件，自己实现数字显示的分页页码：

<pre class="brush:php">&lt;?php if ( $wp_query-&gt;max_num_pages &gt; 1 ) : ?&gt;
&lt;p class="page panel-light"&gt;
&lt;?php
    $args = array(
        'base'         =&gt; '%_%',
        'format'       =&gt; '/page/%#%',
        'total'        =&gt; 1,
        'current'      =&gt; $page,
        'end_size'     =&gt; 2,
        'mid_size'     =&gt; 5,
        'prev_next'    =&gt; false,
     );
    echo paginate_links( $args )
?&gt;
&lt;/p&gt;&lt;?php endif; ?&gt;</pre>

自定义搜索表单，在主题文件夹下新建名为searchform.php的文件，即可替换wp默认表单：

<pre class="brush:php">&lt;form role="search" method="get" id="searchform" action="&lt;?php echo home_url( '/' ); ?&gt;"&gt;
    &lt;label class="screen-reader-text" for="s" /&gt;搜索：&lt;/label&gt;
    &lt;input type="text" value="&lt;?php the_search_query(); ?&gt;" name="s" id="s" /&gt;
    &lt;input type="submit" id="searchsubmit" title="搜索" value="" /&gt;
&lt;/form&gt;</pre>

分页，不需要插件，利用wordpress自带的函数paginate_links实现分页显示，再封装一下作为新函数，在functions.php中添加：

<pre>//自定义的分页函数，可以用于各种页面
function show_pagination() {
    global $wp_query, $wp_rewrite, $paged;
    $current = max(1, $paged); 

    $pagination = array(
        'base' =&gt; @add_query_arg('paged','%#%'),
        'format' =&gt; '',
        'total' =&gt; $wp_query-&gt;max_num_pages, //必须，总页数，默认为1是不会显示页码的
        'current' =&gt; $current,
        'end_size'     =&gt; 2,
        'mid_size'     =&gt; 5,
        'prev_next'    =&gt; false
    ); 

    if( $wp_rewrite-&gt;using_permalinks() )
        $pagination['base'] = user_trailingslashit( trailingslashit( remove_query_arg('s',get_pagenum_link(1) ) ) . 'page/%#%/', 'paged'); 

    if( !empty($wp_query-&gt;query_vars['s']) )
        $pagination['add_args'] = array('s'=&gt;get_query_var('s')); 

    if ( $wp_query-&gt;max_num_pages &gt; 1 ) :
        echo '&lt;p class="pager panel-light"&gt;'.paginate_links($pagination).'&lt;/p&gt;';
    endif;
}</pre>

获取评论者头像Avatar的图片地址，而不是直接获取标签，修改get_avatar()为get_avatar_url()，加入到functions.php文件：

<pre>if ( !function_exists( 'get_avatar_url' ) ) :

function get_avatar_url( $id_or_email, $size = '96', $default = '', $alt = false ) {
	if ( ! get_option('show_avatars') )
		return false;

	if ( false === $alt)
		$safe_alt = '';
	else
		$safe_alt = esc_attr( $alt );

	if ( !is_numeric($size) )
		$size = '96';

	$email = '';
	if ( is_numeric($id_or_email) ) {
		$id = (int) $id_or_email;
		$user = get_userdata($id);
		if ( $user )
			$email = $user-&gt;user_email;
	} elseif ( is_object($id_or_email) ) {
		// No avatar for pingbacks or trackbacks
		$allowed_comment_types = apply_filters( 'get_avatar_comment_types', array( 'comment' ) );
		if ( ! empty( $id_or_email-&gt;comment_type ) &amp;&amp; ! in_array( $id_or_email-&gt;comment_type, (array) $allowed_comment_types ) )
			return false;

		if ( !empty($id_or_email-&gt;user_id) ) {
			$id = (int) $id_or_email-&gt;user_id;
			$user = get_userdata($id);
			if ( $user)
				$email = $user-&gt;user_email;
		} elseif ( !empty($id_or_email-&gt;comment_author_email) ) {
			$email = $id_or_email-&gt;comment_author_email;
		}
	} else {
		$email = $id_or_email;
	}

	if ( empty($default) ) {
		$avatar_default = get_option('avatar_default');
		if ( empty($avatar_default) )
			$default = 'mystery';
		else
			$default = $avatar_default;
	}

	if ( !empty($email) )
		$email_hash = md5( strtolower( $email ) );

	if ( is_ssl() ) {
		$host = 'https://secure.gravatar.com';
	} else {
		if ( !empty($email) )
			$host = sprintf( "http://%d.gravatar.com", ( hexdec( $email_hash[0] ) % 2 ) );
		else
			$host = 'http://0.gravatar.com';
	}

	if ( 'mystery' == $default )
		$default = "$host/avatar/ad516503a11cd5ca435acc9bb6523536?s={$size}"; // ad516503a11cd5ca435acc9bb6523536 == md5('unknown@gravatar.com')
	elseif ( 'blank' == $default )
		$default = includes_url('images/blank.gif');
	elseif ( !empty($email) &amp;&amp; 'gravatar_default' == $default )
		$default = '';
	elseif ( 'gravatar_default' == $default )
		$default = "$host/avatar/s={$size}";
	elseif ( empty($email) )
		$default = "$host/avatar/?d=$default&amp;s={$size}";
	elseif ( strpos($default, 'http://') === 0 )
		$default = add_query_arg( 's', $size, $default );

	if ( !empty($email) ) {
		$out = "$host/avatar/";
		$out .= $email_hash;
		$out .= '?s='.$size;
		$out .= '&amp;d=' . urlencode( $default );

		$rating = get_option('avatar_rating');
		if ( !empty( $rating ) )
			$out .= "&amp;r={$rating}";

		$avatar = $out;
	} else {
		$avatar = $default;
	}

	return apply_filters('get_avatar', $avatar, $id_or_email, $size, $default, $alt);
}
endif;</pre>