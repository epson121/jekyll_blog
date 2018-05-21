---
layout: post
title: Simple markdown editor in Ember.js
date: 2013-10-09 16:53:02.000000000 +00:00
categories:
- emberjs
tags:
- emberjs
- markdown
author: luka
---
Recently I have been playing with Ember.js. It is a javascript front-end framework for developing web applications. You can find more details and really thorough guides on their website.
As my new blog post, I have decided to create a tutorial for building a simple markdown editor (in ember). Markdown, for those unaware of it, is a simple, lightweight markup language. I use it a lot for my github readme pages. While there are several great applications for editing markdown in real time out there, I thought it would be a good exercise to create one of my own, and since this is really easy to do in ember, I am showing it as a simple tutorial.

First thing you should do is download a starter kit from ember.js website. It has all the files we need. You can download it here.

Structure:

Ember.js application has a conventional structure of directories and files. It looks something like this:

```
/App/
--> /css
--> /js
--> /js/libs
--> index.html
```

index.html is our projects main html file. In there you put all your html code. If you have downloaded starter pack, you should have all the files (including index.html) ready to go. So lets add a skeleton html code of our application (delete everything from index.html and put this code in).

```html
<meta charset="utf-8" />
Ember Starter Kit
<link href="css/normalize.css" rel="stylesheet" />
<link href="css/style.css" rel="stylesheet" />
<link href="http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap-combined.no-icons.min.css" rel="stylesheet" /></pre>
<div class="navbar">
    <div class="navbar-inner">
        <a class="brand" href="#">Markup editor</a>
    </div>
</div>
<div class="row"></div>
<pre>
```

This is our starting point. Notice I am using twitter bootstrap for design and styling. Also **style.css** and **normalize.css** are added as stylesheets for this application. **style.css** is where your custom css goes, normalize.css is embers default css and comes with starter kit.

You can add this into your style.css file:

```css
html, body {
    margin: 20px;
}

.active {
    font-weight: bold;
}

[name="input"] {
    width: 100%;
    height: 500px;
    resize: none;
}
```

In your **js/** directory, as you'll probably notice, you have app.js file and lib/ folder. Lib folder holds all your dependencies for the application you are building. There is ember file, jquery library and handlebars.js.

Handlebars is a templating library that lets you embed your handlebars expressions directly into html file. For more information, check out the handlebars webpage.
app.js file is where your javascript goes, that is, your application logic and interaction with models. So in your app.js file put this code (delete everything from app.js if it is not empty):

```js
App = Ember.Application.create();

var showdown = new Showdown.converter();

Ember.Handlebars.helper('format-markdown', function(input) {
    if (input == undefined)
        return "";
    return new Handlebars.SafeString(showdown.makeHtml(input));
});
```

What we have done here is this:
We have created an emberjs application using the

```js
App = Ember.Application.create();
```

Next thing we do is create a showdown variable that holds a reference to Showdown converter. Showdown converter is a javascript port of markdown language and it enables us to easily write and display markdown.

Then we use this reference of showdown and create a helper function that will convert our markdown input and display it properly. We have to check for undefined value and return an empty string because we get an error when there is no markdown for converting. And that is all of our javascript. Now back to html.

First thing we should do is include our javascript dependencies into html file. Add this to the end of your html (just before the closing body tag):

```html
<script type="text/javascript" src="js/libs/jquery-1.9.1.js"></script><script type="text/javascript" src="js/libs/handlebars-1.0.0.js"></script>
<script type="text/javascript" src="js/libs/ember-1.0.0.js"></script><script type="text/javascript" src="http://cdnjs.cloudflare.com/ajax/libs/showdown/0.3.1/showdown.min.js"></script>
<script type="text/javascript" src="js/app.js"></script>
```

This includes all our dependencies like ember, jquery or showdown.js. Next thing we should do is create a textarea where our markdown is going to be written. Add this to your html file:

```html
<script id="md" type="text/x-handlebars">
{% raw %}
{{textarea name="input" value=input placeholder="Enter your markdown here. It will automatically be translated."}}
{% endraw %}
</script>
```

This is the handlebars expression for templating, mentioned earlier. We add a textarea template with few attributes like name, value and placeholder. We are now going to use our template and embed it with the rest of the html we have written earlier.

Edit your html file like this:

```html
<script type="text/x-handlebars">

<div class="navbar">
    <div class="navbar-inner">
        <a class="brand" href="#">Markup editor</a>
    </div>
</div>

<div class="row">
    <div class="span6" >
        {%raw%}
        {{partial 'md'}}
        {%endraw%}
    </div>
<div class="span6" style="border:1px solid black;height:500px;padding:3px;word-break: break-all;">
    {%raw%}
    {{format-markdown input}}
    {%endraw%}
</div>

</script>
```

We have surrounded our html with tag containing special attribute: text/x-handlebars. To use handlebars templates, they need to be inside script tag.
We have added a partial (our textarea template) and a format-markdown template with input as a parameter. If you check out our app.js file, you'll notice that we have defined a helper called format-markdown and it takes one argument and converts it. As you'll also notice, that same name (ie. input) is the value attribute of our textarea, so everything that gets written into textarea will be shown in div right next to it, but it will be converted.

Your final index.html should look like this:

```html
<meta charset="utf-8" />
Ember Starter Kit
<link href="css/normalize.css" rel="stylesheet" />
<link href="css/style.css" rel="stylesheet" />
<link href="http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap-combined.no-icons.min.css" rel="stylesheet" />

<script type="text/x-handlebars">

<div class="navbar" >
    <div class="navbar-inner">
        <a class="brand" href="#">Markup editor </a></div>
    </div>

    <div class="row">

    <div class="span6" >
        {%raw%}
        {{partial 'md'}}
        {%endraw%}
    </div>

    <div class="span6" style="border:1px solid black;height:500px;padding:3px;word-break: break-all;">
        {%raw%}
        {{format-markdown input}}
        {%endraw%}
    </div>
</div>

</script>

<script id="md" type="text/x-handlebars">
    {%raw%}
    {{textarea name="input" value=input placeholder="Enter your markdown here. It will automatically be translated."}}
    {%endraw%}
</script>

<script type="text/javascript" src="js/libs/jquery-1.9.1.js"></script><script type="text/javascript" src="js/libs/handlebars-1.0.0.js"></script>
<script type="text/javascript" src="js/libs/ember-1.0.0.js"></script><script type="text/javascript" src="http://cdnjs.cloudflare.com/ajax/libs/showdown/0.3.1/showdown.min.js"></script>
<script type="text/javascript" src="js/app.js"></script>
```

And that would be all. You now have your own markup editor. :)
This code is also available at github.