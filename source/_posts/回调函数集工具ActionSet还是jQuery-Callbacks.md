title: 回调函数集工具ActionSet还是jQuery Callbacks?
tags:
  - JavaScript
  - jQuery
  - Callbacks
  - 回调函数集
date: 2012-09-28 16:12:09
---

在知道jQuery.Callbacks()方法之前，碰见这样一个场景：

现有A、B、C3个模块， 分别写为3个js同名文件，由3个人分别开发。A和B完全独立，但是都依赖于C中一个ajax的回调数据。应对这种情况，于是写了一个ActionSets的对象，用于多个action的注册添加，与指定的时间地点触发执行 。使用的时候，在A、B分别注册一个Action，然后在C中的ajax回调中去统一执行这些Actions。

当时有两种设计想法，基于Object的key-value形式，一种是可以执行指定的Actions（action亦即callback一样的概念），但不能往Action内传参数，一种是只能执行全部的Actions，但是可以往每个Action传入参数。

因为需求中依赖C的ajax数据是存入cookies的，所以采用了第一种设计，看如下源码可以看出来：

<span id="more-982"></span>

<pre>/**
 *  @author ToFishes
 *  @description 全局的Actions注册与执行,可以用于多个不相关的模块
 *  @example
 *  var currentAppNameActions = ActionSet.createNew();
 *  currentAppNameActions.register('one-action', function() {});
 *  currentAppNameActions.register('two-action', function() {});
 *
 *  currentAppNameActions.excute();
 *  currentAppNameActions.excute('one-action');
 *  currentAppNameActions.excute('one-action', 'two-action');
 */
window.ActionSet = {
    createNew: function() {
        return {
            'actions': {},
            /**
             * @description 注册action方法
             * @param {String} name 设置一个action名称
             * @param {Function} action 需要执行的Action函数
             */
            'register': function(name, action) {
                ActionSet.register.apply(this, arguments);
            },
            /**
             * @description 执行action方法
             * @param {String, String, String...} name 需要执行的action名称，多个名称以逗号相隔
             */
            'excute': function() {
                ActionSet.excute.apply(this, arguments);
            }
        };
    },
    'register': function(name, action) {
        var _this = this;

        if (_this.actions[name]) {
            window.console &amp;&amp; window.console.error('action ' + name + ' has exist');
            return false;
        };
        if (action) {
            _this.actions[name] = action;
            return _this;
        };
    },
    'excute': function() {
        var actions = this.actions,
            length = arguments.length;

        if (length) {
            for (var i = 0; i &lt; length; i++) {
                var action = actions[arguments[i]];
                action &amp;&amp; action();
            };
        } else {
            for (var name in actions) {
                actions[name]();
            };
        };
    }
};</pre>

最终也解决了当时的需求。后来经过同事提醒说jQuery已经提供了这样一种机制，即Callbacks。然后就去了解了下，看源码Callbacks用是的数组存储，而非Object {}。Callbacks实现的亦即上面的第二种设计。当然，Callbacks实现的非常细致，提供了很多操作方法，不过采用数组存储导致有些地方需要遍历判断，感觉不如使用 Object {} 的好。于是乎，今天索性把两种设计糅合了一下，改了改上面的代码，并使用了Callbacks的方法命名，实现了一个既可以传参也可以执行指定Actions的对象。

自己感觉还不错，简单好用，如果有需求，可以参考Callbacks进行方法扩展 ，升级后的代码如下：

<pre>;(function(global) {
	/**
	 *  @author ToFishes
	 *  @description 全局的Actions注册与执行,可以用于多个不相关的模块
     *  实现与jquery.Callbacks一样的功能，但更丰富的是，通过ban方法可以有选择的执行
     *  所需要的Action
	 *  @example
        var action = ActionSet.createNew();
        action.add('one', function(data){console.info('one....' + data);})
            .add('two', function(data){console.info('two....' + data);});
        action.add('three', function(data){console.info('three....' + data);});

        action.fire('it is data');
        action.add('four', function(d){console.info('four...' + d);})
        action.ban('one', 'two').fire('testing ban...');
        action.fire('no ban to fire...');
        action.remove('three', 'four').fire();
	 */
	global.ActionSet = {
		createNew: function() {
			return {
				'actions': {},
                // before fire, this can ban some actions
                'banedActions': {},
				/**
				 * @description 注册action方法
				 * @param {String} name 设置一个action名称
				 * @param {Function} action 需要执行的Action函数
				 */
				'add': function(name, action) {
					ActionSet.add.apply(this, arguments);
                    return this;
				},
                // 在fire执行前，可以暂时禁止指定的actions
                // 参数形式： ban(name1, name2, name3...)
                'ban': function() {
                    ActionSet.ban.apply(this, arguments);
                    return this;
                },
                // 永久移除指定的actions
                // 参数形式： remove(name1, name2, name3...)
                'remove': function() {
                    ActionSet.remove.apply(this, arguments);
                    return this;
                },
				/**
				 * @description 执行action方法
                 * 可以传递任意参数给action
				 */
				'fire': function(args) {
					ActionSet.fire.apply(this, arguments);
                    return this;
				}
			};
		},
		'add': function(name, action) {
			var _this = this;

			if (_this.actions[name]) {
				global.console &amp;&amp; global.console.error('action ' + name + ' has exist');
				return false;
			};
			if (action) {
				_this.actions[name] = action;
				return _this;
			};
		},
        'ban': function() {
            var args = arguments;
            for (var index in args) {
                this.banedActions[args[index]] = true;
            };
        },
        'remove': function() {
            var args = arguments;
            for (var index in args) {
                delete this.actions[args[index]];
            };
        },
		'fire': function() {
			var actions = this.actions;
            for (var name in actions) {
                if (! this.banedActions[name]) {
                    actions[name].apply(this, arguments);
                } else {
                    delete this.banedActions[name];
                };
            };
		}
	};
})(window);</pre>

&nbsp;