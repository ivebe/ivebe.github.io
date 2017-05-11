---
layout: default
permalink: /blog/simple-php-captcha-tutorial.html
image: stopspam.png
description: When user open the page on your website, new session on server side will be generated. In that session we will store random string, and at the same create image with PHP displaying the same text as in session. When user submit text that he sees on the image we will check if submitted text match one stored in session. If does, user is human and validation passed, if not then user did not solve captcha and we can consider user to be spammer script. Of course tweaks are possible, and they will be shown in Advanced PHP captcha tutorial, where more advanced cases will be considered.
category: EN, PHP, Beginner
comments: true
---

# Simple PHP captcha tutorial
<blockquote>Take a look at <a href="/blog/simple-php-captcha-tutorial-part2-reload-without-refresh.html">part 2</a> of this tutorial.</blockquote>

## Introduction
If you are having problems with someone spamming your website comments, email, or any other form where you expect user input, you should put so called CAPTCHA submission in order to stop spammers. Every web site should have captcha system regardless of did someone started to spam, or just as preventive method.  

In this tutorial I will show you to how to create your own captcha system very easily. Lets get started.

## How captcha works
When user open the page on your website, new session on server side will be generated. In that session we will store random string, and at the same time create image with PHP displaying the same text as in session. When user submit text that he sees on the image we will check if submitted text match one stored in session. If does, user is human and validation passed, if not then user did not solve captcha and we can consider user to be spammer script. Of course tweaks are possible, and they will be shown in Advanced PHP captcha tutorial, where more advanced cases will be considered.

```js
/* captcha.php file */
session_start();
header("Expires: Tue, 01 Jan 2013 00:00:00 GMT");
header("Last-Modified: " . gmdate("D, d M Y H:i:s") . " GMT");
header("Cache-Control: no-store, no-cache, must-revalidate");
header("Cache-Control: post-check=0, pre-check=0", false);
header("Pragma: no-cache");
```

We are going to start a session, and set headers for no caching, as captcha image shouldn't be cached, it should always show new image.

```js
$chars = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
$randomString = '';
for ($i = 0; $i < 5; $i++)
{
    $randomString .= $chars[rand(0, strlen($chars)-1)];
}
$_SESSION['captcha'] = strtolower( $randomString );
```

Next step is to generate random string. In chars variable you can specify which characters you want to appear in the captcha image. You can add special characters if you wish. Length of random string is set in the for loop, and in this tutorial it's 5. Length of string is up to you to decide, I personally don't put to big length as real user don't wanna be bothered with entering long captcha. After generating random string, we will store it in session as lowercase string, as we don't want case sensitive captcha. If you want case sensitive captcha, just remove function strtolower and save random string as it is.

<img src="/assets/imgs/captcha_bg.png" />

Now create captcha background in any photo editor (Ex. Photoshop). Captcha background should have random patterns so it can be hard for spammers scripts to read it. Next step is to select font we are going to use. In this tutorial I used "larabiefont.ttf". So used font should be True Type Font.

```js
$im = @imagecreatefrompng("captcha_bg.png");
imagettftext($im, 30, 0, 10, 38, imagecolorallocate ($im, 0, 0, 0), 'larabiefont.ttf', $randomString);
header('Content-type: image/png');
imagepng($im, NULL, 0);
imagedestroy($im);
```

$im represent drawing area for our captcha image. First we are loading background. Next step is to draw random string on it with custom font. For that we are using function imagettftext. Now I'll explain parameters of this function. First one is drawing area of an image. Second one is size of text. Here you should be careful, depending on GD version installed on your server this can be in px for GD1 or points GD2. Just try one or other until you are satisfied with results. Next tree are angle of text, X position and Y position, respectively. Sixth one is color of text, function is self explanatory, three parameters are RGB values. And last two parameters are font file, and string.  

With this, our captcha file is done. Now we are going to write few more lines for validating user input against captcha image.

```js
/* captcha-validate file */
session_start();
if(strtolower($_POST['answer']) == $_SESSION['captcha'])
    echo 'Captcha solved successfully, now you can allow this user to submit comment/vote/upload/etc.';
else
    echo 'Sorry, captcha not solved. Offer user captcha again or what ever.';

unset($_SESSION['captcha'])
//EDIT: I haven't posted this before but you should unset captcha from session, or destroy session.
//You can do this with unset($_SESSION['captcha']). That way malicious user won't be able to recreate your old session.
```

This is all you need to do in order to validate captcha! Just check user input against captcha value stored in session. If you used case insensitive captcha leave strtolower as it is, otherwise remove that function. Pretty simple isn't it. All you need to implement captcha on your existing website is this one if condition, and one file we just wrote, captcha.php. Now last file we are going to write will be example how you can implement this captcha system.

```html
<img src="captcha.php" />
<form action="captcha-validate.php" method="post">
    <input type="text" name="answer" placeholder="Enter captcha here" />
    <input type="submit" value="CHECK" />
</form>
```

Please note that in order to keep simplicity of this tutorial there wasn't any bigger security check, that is left for next serial of this tutorial in intermediate or advanced category.

<blockquote>Take a look at <a href="/blog/simple-php-captcha-tutorial-part2-reload-without-refresh.html">part 2</a> of this tutorial.</blockquote>

<ul class="blog-info">
<li><i class="fa fa-user"></i> By Dan</li>
<li><i class="fa fa-calendar"></i> {{ page.date | date: '%B %d, %Y' }}</li>
<li><i class="fa fa-tags"></i> {{ page.categories }}</li>
</ul>

{% if page.comments %}
{% include comments.md %}
{% endif %}