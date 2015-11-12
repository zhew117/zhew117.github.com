---
layout: post
title: "android.support.v4.hardware.display.xxx has already been added to output"
description: ""
category: 
tags: []
---
{% include JB/setup %}


-  Error Info:Error:Class android.support.v4.hardware.display.DisplayManagerJellybeanMr1 has already been added to output. Please remove duplicate copies.
Error:Class android.support.v4.hardware.display.DisplayManagerCompat has already been added to output. Please remove duplicate copies.
Error:Class android.support.v4.hardware.display.DisplayManagerCompat has already been added to output. Please remove duplicate copies.

Apparently I have to finger out why there are duplicate copies.

I use gradle in Android Studio to check the build process.

or use comnand line which is **gradle -q :appname:dependencies**

![](/assets/Gradle.jpeg)


It will give you some info like this.


![](/assets/GradleInfo.jpeg)

I have had two android-support-v4.jar(s) before clear

which in the ViewPager libs and LOCAL libs

so I just delete one of them and rebuild it.

done!