---
layout: default
permalink: /blog/simple-php-captcha-tutorial-part2-reload-without-refresh.html
image: stopspam2.png
description: This is 2nd part of Simple PHP captcha tutorial in which we will reload captcha without refreshing whole page.
category: EN, PHP, Beginner, jQuery
comments: true
---

# Simple PHP captcha tutorial - part 2

Before reading this tutorial, I assume you read [part 1](/blog/simple-php-captcha-tutorial.html) of this tutorial. Here we will introduce few easy techniques to make captcha reload without refreshing whole page using jQuery. jQuery is javascript library which is widely used, so we will use it as well. We need to include it in our page, put this line in your head part of document:

```html
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
```

You could also download jQuery library and reference it from your own website. Let's move on. In this tutorial we will modify only demo.html page from previous tutorial, as php files generates captcha just fine.  

Let's go and add id attribute to our captcha image so we can locate it more easily. Our captcha image should look like this:

```html
<img src="captcha.php" id="captcha" /> 
```

Now let's remove onclick function from demo.html file and just add id attribute to the button.

```html
<input type="button" id="reload" value="Reload" />    
```

Note that this doesn't have to be a button, but for simplicity I'll use a existing button from previous tutorial. Now let's make jQuery script that will change captcha image. We will write it after document is loaded, so we will use some form of `document.ready` function. jQuery have handy form for this it goes like this:

```js
$(function() {
    // Handler for .ready() called.
});
```

We will put this code just before closing body tag between <script></script> tags. Inside we will put handler for click on our "reload" button. Code:

```html
<script>
    $(function() {
        $('#reload').click(function(){
            //here goes code for reloading image
        });
    });
</script>
</body>
</html>
```

Now comes the interesting part. In order to reload image, all we need to do is call the same url, "captcha.php", and just add query string to it, so web browser will think it's a new image and reload it, but we will actually generate new image in the same "captcha.php" file. In order to generate new query string every time, we will use javascript `Date.getTime()` function which return time in milliseconds since 1970/01/01, that way it will be unique value every time you hit reload button. Let's see it.

```js
<script>
    $(function() {
        $('#reload').click(function(){
            var d = new Date();
            $('img').attr('src', 'captcha.php?' + d.getTime());
        });
    });
</script>
```

That's it. In just few lines of code we made functional captcha which can be reloaded without refreshing whole page. Enjoy, and don't forget to check next part of tutorial where I will show how to limit number of attempts user can try to enter captcha.  

Now complete demo.html source:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
        <title>Simple PHP captcha demo</title>
        <meta name="viewport" content="width=device-width; initial-scale=1.0" />
        <script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
    </head>
    <body>
        <div>
            <header>
                <h1>Simple PHP captcha demo</h1>
            </header>
            <div>
                <img src="captcha.php" id="captcha" />
                <form action="captcha-validate.php" method="post">
                    <input type="text" name="answer" placeholder="Enter captcha here" />
                    <input type="submit" value="CHECK" />
                    <input type="button" id="reload" value="Reload" />
                </form>
            </div>
            <footer>
                <p>
                    &copy; Copyright  by <a href="http://www.ivebe.com">Danijel Petrovic</a>
                </p>
            </footer>
        </div>
        <script>
        $(function() { // Handler for .ready() called.
            $('#reload').click(function(){
                $('#captcha').attr('src', 'captcha.php?' + (new Date).getTime());
            });
        });
        </script>
    </body>
</html>
```





<ul class="blog-info">
<li><i class="fa fa-user"></i> By Dan</li>
<li><i class="fa fa-calendar"></i> {{ page.date | date: '%B %d, %Y' }}</li>
<li><i class="fa fa-tags"></i> {{ page.categories }}</li>
</ul>

{% if page.comments %}
{% include comments.md %}
{% endif %}