---
layout: post
title:  "Android Perf Patterns - Skim 1 - Memory"
date:   2016-03-30 00:00:00
categories: [starters, skims, tools]
description: "In the first installment of several skims, I'll summarize the content of the first five episodes of season 1 which is all about memory."
---

The [Android Developers Youtube Channel](https://www.youtube.com/user/androiddevelopers) is putting up so much valuable information regarding Android development. One of their best series is the [Android Performance Patterns Series](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE) which focuses on helping developers write faster and more performant Android applications. At the time of writing, it already has 5 hours of jam-packed content in 73 videos.

In the first installment of several skims, I'll summarize the content of the first five episodes of season 1 which is all about memory.

# Memory

## Some Theories

- Android memory heaps are segmented into spaces with set sizes based on the type of allocation:

  For Lollipop and up (ART):

  - Zygote Space
  - Non Moving Space
  - Image Space
  - Main Allocation Space
  - Large Object Space

  For less than Lollipop (Dalvik):

  - Linear Allocation Space
  - Zygote Space
  - Allocation Space

  As new objects are allocated, they are placed into the best space based on their characteristics/type and how they can best be organized for future memory releases.

- The Android runtime is a managed-memory environment. By tracking all the allocations, memory is automatically released by the system through a process called **garbage collection**. As memory in a space grows, the system triggers a GC event to accommodate future allocations.

- GC was invented by John McCarthy around 1959 for Lisp.

  - The GC Strategy

    1. Find objects which cannot be accessed (i.e. unused objects).
    2. Reclaim the resources from them.

  - GC events behave differently based on Android runtime:

    - Dalvik : GC events are blocking and cause large pauses.
    - ART: GC events are concurrent but may still cause small pauses at the end.

- A **memory leak** happens when allocated memory cannot be freed.
When an object is expected to be unused but there is at least one reference to it, GC will fail to recognize it as unused. It stays in memory which makes the available space smaller and thus, triggers the GC again and again.

- GC events may affect rendering and cause frame skips if they took longer than expected or if they happen too often. The more GC events, the less time for other operations such as rendering.

- When a high amount of objects is allocated and freed in a small amount of time, it is considered as a **memory churn**.

## Fix Them Leaks!

### To check for GC event issues:

  - Use the [Memory Monitor Tool](http://developer.android.com/tools/performance/memory-monitor/index.html) which shows how your app is using memory over time.

  - How to Launch: Android Studio > Tools > Android > Memory Monitor

  - Light blue is free memory or memory available to be used.

  - Dark blue is allocated memory or memory being used.

  - Every drop/dip in the graph is a garbage collection event. Lots of dips in a short amount of time signal performance problems.

  - If there are problems, consider using the Heap tool and the Allocation Tracker tool.


### To detect and fix memory leaks:

  Assume we have an activity that has bitmaps that have to be GCed after the activity is destroyed:

  1. Create a blank activity with a low or known memory allocation and create a way for the bitmap activity to transition into it.
  2. Use the [Heap Viewer](http://developer.android.com/tools/performance/heap-viewer/index.html) to get a snapshot of how memory normally looks like in the activity that has bitmaps.
  3. Transition into the blank activity and click "Cause GC" to force garbage collection which should remove allocations from the bitmap activity.
  4. Once you're in the blank activity, do another heap dump and inspect if there are allocations that shouldn't be there.
  5. If there are suspects, switch to the [Allocation Tracker](http://developer.android.com/tools/performance/allocation-tracker/index.html) tool to know what allocations are occurring.
  6. To use allocation tracker, Press Start Allocation Tracking, open the bitmap activity, transition to the blank activity, press "Cause GC", then press Stop Allocation Tracking.
  7. After a while, the tool will display a list of all objects that were created, the order in which they were created and where they were created.
  8. You can now track down the allocations on your code and figure out why they are not being freed.

### To prevent memory churn:

  1. Avoid allocation in inner loops.
  2. Avoid onDraw allocations.
  3. Consider using an object pool. The pool allocates a group of objects and allows them to be grabbed and returned once usage is done. Objects are pulled from the pool instead of allocating them again on the memory heap.

## References
Android Performance Patterns Youtube Channel - [https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE)
