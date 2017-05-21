---
title: activity生命周期学习
date: 2017-05-06 01:22:27
tags:
---


# activity生命周期学习

activity 是android的交互界面接口，我们在界面布局显示依托activity。
所以我们要把activity学好。

首先我们要了解activity的生命周期：
![image](http://op7g58imx.bkt.clouddn.com/activity-lifecycle.png)

上面这幅图很清新的描述了activity的生命周期。

==注意：有很多博文说在activity显示dialog，会onPause，经过demo测试 并不会onPause。
 只有在跳转到theme为dialog且透明或半透明的activity，才会onPause==

 