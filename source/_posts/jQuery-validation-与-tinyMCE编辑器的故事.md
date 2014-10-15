title: jQuery validation 与 tinyMCE编辑器的故事
tags:
  - jQuery
  - solution
  - tinyMCE
  - validation
date: 2010-12-30 16:27:31
---

项目使用了jQuery validation验证插件，但是与tinyMCE拍档时，就来问题了。

1、验证错误信息显示在编辑器上方了。

2、tinyMCE的值不能及时赋给绑定的输入框，非空验证第一次总是失败的。

自己开始时候想了几个解决方案，但是统统失败了，查两个各自的api，浩如烟海，不是那么容易找到自己需要的。

后来Google到官方作者的解决方案，恩，很好。

DEMO: [http://jquery.bassistance.de/validate/demo/tinymce/ ](http://jquery.bassistance.de/validate/demo/tinymce/)

<span id="more-204"></span>

记录下代码以备后用：

<pre class="brush:as3">&lt;script type="text/javascript" src="../../lib/jquery.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="../../jquery.validate.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="tiny_mce.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript"&gt;
	tinyMCE.init({
		mode : "textareas",
		theme : "simple",
		// update validation status on change
		onchange_callback: function(editor) {
			tinyMCE.triggerSave();
			$("#" + editor.id).valid();
		}
	});
	$(function() {
		var validator = $("#myform").submit(function() {
			// update underlying textarea before submit validation
			tinyMCE.triggerSave();
		}).validate({
			rules: {
				title: "required",
				content: "required"
			},
			errorPlacement: function(label, element) {
				// position error label after generated textarea
				if (element.is("textarea")) {
					label.insertAfter(element.next());
				} else {
					label.insertAfter(element)
				}
			}
		});
		validator.focusInvalid = function() {
			// put focus on tinymce on submit validation
			if( this.settings.focusInvalid ) {
				try {
					var toFocus = $(this.findLastActive() || this.errorList.length &amp;&amp; this.errorList[0].element || []);
					if (toFocus.is("textarea")) {
						tinyMCE.get(toFocus.attr("id")).focus();
					} else {
						toFocus.filter(":visible").focus();
					}
				} catch(e) {
					// ignore IE throwing errors when focusing hidden elements
				}
			}
		}
	})
&lt;/script&gt;</pre>