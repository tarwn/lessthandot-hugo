---
title: Using Multiple jQuery UI Sliders on a Single Page
author: Alex Ullrich
type: post
date: 2010-06-10T14:00:00+00:00
url: /index.php/webdev/webdesigngraphicsstyling/using-multiple-jquery-ui-sliders-on-a-si/
views:
  - 42090
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Web Design, Graphics and Styling

---
I had a bit of frustration lately when dealing with jQuery UI&#8217;s slider widget, more specifically when trying to deal with many on the same page. I wanted to follow the same pattern shown in the example for [snap to increments][1] where I have a div for the slider, and the value is actually set to a text input for easy form submission. The individual elements look something like this:

<pre><div class="SliderControl"&gt;
	<label for="clarity"&gt;Clarity:</label&gt;
	<input type="text" class="SliderText" readonly="readonly" id="clarity" name="clarity"/&gt;
	<div class="RatingSlider" id="claritySlider"&gt;</div&gt;	
</div&gt;</pre>

Basically there&#8217;s an input, and a single slider per input. The slider&#8217;s name is {input name}Slider. I had some trouble reconciling this setup with the way that the example on the jQuery site is laid out:

<pre>$("#slider").slider({
	value:100,
	min: 0,
	max: 500,
	step: 50,
	slide: function(event, ui) {
		$("#amount").val('$' + ui.value);
	}
});
$("#amount").val('$' + $("#slider").slider("value"));</pre>

My problem was, I couldn&#8217;t find a way to derive the underlying element&#8217;s id from the &#8220;ui&#8221; parameter in the callback specified for &#8220;slide&#8221;. The way I ended up handling this was using the jquery&#8217;s .each function to configure the sliders one by one. Its&#8217; not ideal, but its&#8217; also not that much more code, and I doubt many pages I&#8217;ll be working on have so many sliders that it will become an issue.

<pre>$('.RatingSlider').each(function(idx, elm) {
	var name = elm.id.replace('Slider', '');
	$('#' + elm.id).slider({
		value: 3,
		min: 0,
		max: 5,
		step: .5,
		slide: function(event, ui) {
			$('#' + name).val(ui.value);
		}
	});
});</pre>

I&#8217;d be very interested in finding a way to avoid the .each, but I couldn&#8217;t find anything on the interwebs and this seems to be working for now.

**edit:** I threw together a quick demo per requests in the comments. You can download it here: [jQuery UI Multiple Slider Demo][2]. Hope it helps!

 [1]: http://jqueryui.com/demos/slider/#steps
 [2]: /wp-content/uploads/blogs/WebDev/using-multiple-jquery-ui-sliders-on-a-si/Sliders.zip "jQuery UI Multiple Slider Demo"