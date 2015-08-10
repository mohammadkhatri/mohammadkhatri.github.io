---
layout: post
title: PHP Redirct Based on user device (Ios , Android Or Other)
description : PHP Redirct Based on user device Platform. Header is redirected to specified URL based on Specific device type like ios , android or other.
sitemap:
    priority: 0.9
    changefreq: 'weekly'
    lastmod: 2015-08-10T12:49:30-05:00
published: true
---

It's sometimes required to redirect to differnet urls based on device type. 
<p>i.e. In sharing message on some social network or any other media like whatsapp , Email or any other chat.</p>
Here is a small script which identifies users device by inspecting useragent of their browsers.

```PHP
<?PHP
//Detect special conditions devices
$iPod    = stripos($_SERVER['HTTP_USER_AGENT'],"iPod");
$iPhone  = stripos($_SERVER['HTTP_USER_AGENT'],"iPhone");
$iPad    = stripos($_SERVER['HTTP_USER_AGENT'],"iPad");
$Android = stripos($_SERVER['HTTP_USER_AGENT'],"Android");
$webOS   = stripos($_SERVER['HTTP_USER_AGENT'],"webOS");

//do something with this information
if( $iPod || $iPhone || $iPad){
  //browser reported as an iPhone/iPod touch -- open itunes store here
  header( 'Location: http://itunes.apple.com/app/clash-of-clans/id529479190?mt=8' );
  die();
 // echo "iphone";
  
}
else if($Android){
    //browser reported as an Android device -- open play store here
  //echo "android";
  header( 'Location: market://details?id=com.supercell.clashofclans' );
  die();
}
else
{
   // couldn't detect any mobile browsers - Default action 
  header( 'Location: https://play.google.com/store/apps/details?id=com.supercell.clashofclans&hl=ene' );
  die();
}
?>
```

As seen , the link is redirected to clash of clanes game based on android or ios device to pericular store.
Upload this script to server to see the effect. You can try it by spoofing useragent by [User Agent Switcher](https://www.google.co.in/search?q=user+agent+switcher&oq=user+agent+switcher).