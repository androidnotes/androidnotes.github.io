---
layout: post
title:  "Android Perf Patterns - Skim 2 - Battery"
date:   2016-04-23 00:00:00
categories: [starters, skims, tools]
description: "A performant app does not only improve the user's perceived speed of the application but also conserves the device's battery."
---

A performant app does not only improve the user's perceived speed of the application but also conserves the device's battery. The [Android Performance Patterns Series](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE) gives developers information on how to best tweak apps to prevent common battery issues.

## Battery is a limited resource

- The hardware pulls energy from the battery to execute a task. The energy that is consumed depends on the action. Every time the phone is awake (the screen is lit, the radio is opened, etc.), battery energy is consumed.
- Battery consumption is at its minimum during sleep. Sleep saves battery life.

## How to be friends with the battery

1. Use the [PowerManager.WakeLock API](http://developer.android.com/reference/android/os/PowerManager.WakeLock.html) carefully. Sometimes, wake lock is not released due to errors and other edge cases. It is recommended to use the [acquire(long timeout)](http://developer.android.com/reference/android/os/PowerManager.WakeLock.html#acquire(long)) method and provide a timeout value. The wake lock is released when the time is up.

2. Use inexact timers to kick off events. The [AlarmManager](http://developer.android.com/reference/android/app/AlarmManager.html) has support for setting future events which can be delayed to be group with other apps' events.

3. Use the [JobScheduler API](http://developer.android.com/reference/android/app/job/JobScheduler.html) to batch and defer jobs. It is intelligent enough to wait for better conditions (e.g. when phone is charging, when on WiFi, when device is already awake) before executing the logic for the jobs.

## How to check for battery issues

1. **Battery Details screen in app settings**
  One can check on this screen how much time an app keeps a phone awake. Bear in mind that the Uninstall button is also located on this screen so you better not screw up the battery.
2. [Battery Historian](http://developer.android.com/tools/performance/batterystats-battery-historian/index.html)
  A developer tool that shows wake lock logic order, how often wakes are kicked off, who initialized them and how long they lasted.

### References
  - [Android Performance Patterns Youtube Channel](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE)
