title: HTML5表单验证有效状态对象
tags:
  - JavaScript
  - validation
  - validity
date: 2014-08-29 12:24:38
---

HTML5增加对表单内容类型验证的支持，而通过js获取验证有效状态的API就是ValidityState对象。

获取该对象方法：

<pre>
var input = document.createElement('input');
console.info(input.validity)
</pre>

浏览器支持度见： [http://caniuse.com/#feat=form-validation](http://caniuse.com/#feat=form-validation)

那么由此我们就可以写出更简单的验证框架。该[对象拥有的属性](https://developer.mozilla.org/en-US/docs/Web/API/ValidityState)如下：

<span id="more-1222"></span>

<table class="standard-table">
<thead>
<tr>
<th scope="col">Name</th>
<th scope="col">Type</th>
<th scope="col">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>`customError`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The element&#8217;s custom validity message has been set to a non-empty string by calling the element&#8217;s `setCustomValidity()` method.</td>
</tr>
<tr>
<td>`badInput`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The element has incomplete input and the user agent does not think the user ought to be able to submit the form in its current state.</td>
</tr>
<tr>
<td>`patternMismatch`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The value does not match the specified `[pattern](/en-US/docs/Web/HTML/Element/input#attr-pattern)`.</td>
</tr>
<tr>
<td>`rangeOverflow`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The value is greater than the maximum specified by the `[max](/en-US/docs/Web/HTML/Element/input#attr-max)` attribute.</td>
</tr>
<tr>
<td>`rangeUnderflow`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The value is less than the minimum specified by the `[min](/en-US/docs/Web/HTML/Element/input#attr-min)` attribute.</td>
</tr>
<tr>
<td>`stepMismatch`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The value does not fit the rules determined by the `[step](/en-US/docs/Web/HTML/Element/input#attr-step)` attribute (that is, it&#8217;s not evenly divisible by the step value).</td>
</tr>
<tr>
<td>`tooLong`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>

The value exceeds the specified `maxlength` for [`HTMLInputElement`](/en-US/docs/Web/API/HTMLInputElement "The HTMLInputElement interface provides special properties and methods (beyond the regular HTMLElement interface it also has available to it by inheritance) for manipulating the layout and presentation of input elements.") or [ elements.&#8221;>`HTMLTextAreaElement`](/en-US/docs/Web/API/HTMLTextAreaElement "The HTMLTextAreaElement interface, which provides special properties and methods (beyond the regular HTMLElement interface it also has available to it by inheritance) for manipulating the layout and presentation of <textarea") objects.

<div class="note">
     **Note:** This will never be `true` in Gecko, because elements&#8217; values are prevented from being longer than `maxlength`.</div>
</td>
</tr>
<tr>
<td>`typeMismatch`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The value is not in the required syntax (when `[type](/en-US/docs/Web/HTML/Element/input#attr-type)` is `email` or `url`).</td>
</tr>
<tr>
<td>`valid`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The element meets all constraint validations, and is therefore considered to be valid.</td>
</tr>
<tr>
<td>`valueMissing`</td>
<td>[Boolean](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)</td>
<td>The element has a `[required](/en-US/docs/Web/HTML/Element/input#attr-required)` attribute, but no value.</td>
</tr>
</tbody>
</table>

更多内容： [https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Data_form_validation](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Data_form_validation)