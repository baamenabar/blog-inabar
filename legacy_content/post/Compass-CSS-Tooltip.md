---
title: Accessible CSS Tooltip in Compass
subtitle: A tooltip that works with IE7+, screen readers and touch devices. A simple class made on Compass pre-processor.
date: 2013-07-05T15:07:58
tags: ['css', 'scss', 'compass', 'styling', 'accessibility']
---

# Accessible CSS Tooltip in Compass

Yes, yet again revisiting the ubiquitous _tooltip_ snippet. This one works on IE7+ probably on IE6 too. Is accessible with the keyboard and touch devices, semantic, and screen reader can read it. It also comes with a bit of styling and animation.

## Screen-reader, legacy, touch and keyboard friendly

Here we can see it working on Codepen with it's two flavours:

<pre class="codepen" data-height="265" data-type="result" data-href="gykwi" data-user="baamenabar" data-safe="true"><code></code><a href="http://codepen.io/baamenabar/pen/gykwi">Check out this Pen!</a></pre>
<script async src="http://codepen.io/assets/embed/ei.js"></script>

## Markup {#ac1}

```
<a href="javascript:;" class="tooltip">?<span>Here lies more explanation to this rather ambiguous question mark</span></a>
```

An anchor with a span inside. The `javascript:;` inside the `href` is for avoiding the scroll jumping to the top when you click on the anchor.

## The CSS Compass class {#ac2}

```
.tooltip{
	/* this is for actual behaviour */
	position: relative;
	overflow: hidden;
	@include inline-block;//it could be display:block;
	/* this is for mere presentation */
	padding:0.125em 0.25em;
	color:#FFF;
	text-decoration: none;
	border-radius: 3px;
	cursor:help;
	outline:none;//since it's obvious there's focus.
	background:#444;
	span{
		/* this is for actual behaviour */
		display:block;
//NOTE: this block must positions outside the parent's box to be hidden.
		position: absolute;
		left: 0;
		top: 2em;
		z-index: 99;
		width: 15em;
		/* this is for mere presentation */
		color: #444;
		background:#FFF;
		border-radius: 4px;
		border: 1px solid #888;
		padding:0.5em 1em;
		@include single-box-shadow(rgba(0,0,0,0.4), 2px, 2px, 4px, 0);
		@include single-transition(opacity, 0.5s, ease-in-out);
		opacity:0;
	}
	&:hover, &:active, &:focus{
		/* this is for actual behaviour */
		overflow: visible;
		/* this is for mere presentation */
		background:#000;
		span{
			opacity:1;
		}
	}
}
```

The key for this piece of code lies in the `overflow:hidden` which visibly hides content, but it can be read by screen readers, this is based on the **clip** techniques recommended by the [_Yahoo Accessibility Lab_](http://yaccessibilityblog.com/library/)

In the `&:hover, &:active, &:focus` bit is where the changes in visibility happen, not only on hover, but also on the `focus` an `active` states, which do exist on touch devices.

The other advantage of not using `display:none` is that it allows us to use css transitions.

This scss code comes with some basic presentation stiles besides the behavioural styles.
