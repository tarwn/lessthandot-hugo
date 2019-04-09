---
title: Using Multiple jQuery UI Sliders on a Single Page
author: Alex Ullrich
type: post
date: 2010-06-10T14:00:00+00:00
ID: 814
url: /index.php/webdev/webdesigngraphicsstyling/using-multiple-jquery-ui-sliders-on-a-si/
views:
  - 42090
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Web Design, Graphics and Styling

---
I had a bit of frustration lately when dealing with jQuery UI's slider widget, more specifically when trying to deal with many on the same page. I wanted to follow the same pattern shown in the example for [snap to increments][1] where I have a div for the slider, and the value is actually set to a text input for easy form submission. The individual elements look something like this:

```html
<div class="SliderControl">
	<label for="clarity">Clarity:</label>
	<input type="text" class="SliderText" readonly="readonly" id="clarity" name="clarity"/>
	<div class="RatingSlider" id="claritySlider"></div>	
</div>
```

Basically there's an input, and a single slider per input. The slider's name is {input name}Slider. I had some trouble reconciling this setup with the way that the example on the jQuery site is laid out:

```javascript
$("#slider").slider({
	value:100,
	min: 0,
	max: 500,
	step: 50,
	slide: function(event, ui) {
		$("#amount").val('$' + ui.value);
	}
});
$("#amount").val('$' + $("#slider").slider("value"));
```
My problem was, I couldn't find a way to derive the underlying element's id from the “ui” parameter in the callback specified for “slide”. The way I ended up handling this was using the jquery's .each function to configure the sliders one by one. Its' not ideal, but its' also not that much more code, and I doubt many pages I'll be working on have so many sliders that it will become an issue.

```javascript
$('.RatingSlider').each(function(idx, elm) {
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
});
```

I'd be very interested in finding a way to avoid the .each, but I couldn't find anything on the interwebs and this seems to be working for now.

**edit:** I threw together a quick demo per requests in the comments. You can download it here: [jQuery UI Multiple Slider Demo][2]. Hope it helps!

 [1]: http://jqueryui.com/demos/slider/#steps
 [2]: /wp-content/uploads/blogs/WebDev/using-multiple-jquery-ui-sliders-on-a-si/Sliders.zip "jQuery UI Multiple Slider Demo"