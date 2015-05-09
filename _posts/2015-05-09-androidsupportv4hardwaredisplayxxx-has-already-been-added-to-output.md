---
layout: post
title: "android.support.v4.hardware.display.xxx has already been added to output"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Error Info:Error:Class android.support.v4.hardware.display.DisplayManagerJellybeanMr1 has already been added to output. Please remove duplicate copies.
Error:Class android.support.v4.hardware.display.DisplayManagerCompat has already been added to output. Please remove duplicate copies.
Error:Class android.support.v4.hardware.display.DisplayManagerCompat has already been added to output. Please remove duplicate copies.

Apparently I have to finger out why there are duplicate copies.

I use gradle in Android Studio to check the build process.

or use gradle -q :appname:dependencies

![](/assets/Gradle.jpeg)


It will give you some info like this.


>+--- LOCAL: commons-codec-1.9.jar
+--- LOCAL: universal-image-loader-1.8.6.jar
+--- LOCAL: android-support-v4.jar
+--- rosie_android:ViewPager:unspecified
|    \--- LOCAL: android-support-v4.jar
+--- rosie_android:stripe:unspecified
|    +--- LOCAL: gson-2.2.4.jar
|    \--- LOCAL: stripe-java-1.15.1.jar
+--- :hangit_sdk:
|    \--- LOCAL: android-async-http-1.4.6.jar
\--- com.google.android.gms:play-services:6.5.87



I have two android-support-v4.jar(s)

which in the ViewPager libs and LOCAL libs

so I just delete one of them and rebuild it.

done!