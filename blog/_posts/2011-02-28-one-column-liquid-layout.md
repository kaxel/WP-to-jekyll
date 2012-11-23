---
layout: blog
title: one column liquid layout
permalink: 2011/02/one-column-liquid-layout
categories: [Code]
---

<p>I am using this for the Android app I&#039;m working on, and it is spot on.</p>
<p>HTML found here: <a href="http://www.maxdesign.com.au/articles/css-layouts/one-liquid/" title="http://www.maxdesign.com.au/articles/css-layouts/one-liquid/">http://www.maxdesign.com.au/articles/css-layouts/one-liquid/</a></p>
<p>and the css (which I have added to a little bit compared to the original):</p>
<pre>

#container
{
	margin: 0 30px;
	background: #fff;
}

#header
{
	background: #ccc;
	padding: 20px;
}

#header h1 { margin: 0; text-align: center;}

#navigation
{
	float: left;
	width: 100%;
	background: #333;
}

#navigation ul
{
	margin: 0;
	padding: 0;
}

#navigation ul li
{
	list-style-type: none;
	display: inline;
}

#navigation li a
{
	display: block;
	float: left;
	padding: 5px 10px;
	color: #fff;
	text-decoration: none;
	border-right: 1px solid #fff;
}

#navigation li a:hover { background: #383; }

#content
{
	clear: left;
	padding: 20px;
}

#message
{
	background-color: #ccc;
	padding: 10px;
}

.field
{
	border-left: 4px solid red;
	padding-left: 20px;
}

.username {font-weight: bold;}

#content h2
{
	color: #000;
	font-size: 160%;
	margin: 0 0 .5em;
}

#footer
{
	background: #ccc;
	text-align: right;
	padding: 20px;
	height: 1%;
}

</pre>