title: ToFishes系列jQuery插件之textarea自适应高度和顺滑淡出
tags:
  - jQuery
  - easeout
  - textarea自适应高度
  - 顺滑淡出
date: 2012-12-11 12:24:31
---

两个很简单的插件，可以认识下写插件的基本套路，甚至是固定的套路。

<pre>
// textarea自动根据内容自适应高度
// 例子： $('textarea').autoHeight();
$.fn.autoHeight = function(options) {
    var _this = this;
    $(window).on('resize.textarea-auto-height', function() {
        _this.trigger('autoheighthandller');
    });
    return this.each(function() {
        var $this = $(this);
        if (! $this.data('origin-height')) {
            $this.data('origin-height', $this.height());
        };
        var scrollHeight = $this[0].scrollHeight;
        if($this.height() !== scrollHeight) $this.height(scrollHeight);
        $this.on('input keyup paste cut autoheighthandller', function(e) {
            // 检查高度，自动适应
            $this.height($this.data('origin-height'));
            var scrollHeight = $this[0].scrollHeight;
            if($this.height() !== scrollHeight) $this.height(scrollHeight);
        });
    });
};

// 比fadeOut更顺滑的淡出
// duration 是持续时间, 默认是300ms
// callback 是完成后的回调
$.fn.easeOut = function(duration, callback) {
    duration = duration || 300;
    return this.animate({
        'margin': 0,
        'height': 0,
        'opacity': 0,
        'padding': 0,
        'border': 0
    }, duration, function() {
        callback &#038;&#038; callback.apply(this, arguments);
    });
};
</pre>

&nbsp;