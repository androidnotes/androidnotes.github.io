---
layout: post
title:  "UX Design for Mobile Developers"
date:   2016-01-05 00:00:00
categories: [skims, design]
description: "Mobile developers ultimately care for the users. If we want to build apps that cater to users' needs, then it makes sense to design our apps for them."
---
Mobile developers ultimately care for the users. If we want to build apps that cater to users' needs, then it makes sense to design our apps for them. [UX Design for Mobile Developers](https://www.udacity.com/course/ux-design-for-mobile-developers--ud849) by Google at Udacity is a course targeted to us developers to help us build our next 5-star app.


##LESSON 1 - UI IS NOT UX

- UI is for designers (refers to visual representations). UX is for everyone (refers to overall experience)
- Don't think too detailed immediately.
- Create a low res wireframe first that sums up the ultimate experience of the user (ex: pizza app: from hungry to satisfied)

##LESSON 2 - USE PERSONAS TO AVOID BIASES

- Personas and their characteristics are determined by UX researchers or by the target market
- Sample Persona: Dorm Room Debbie
- Sample Characteristics: lives in a dorm, doesn’t have a car, lives in a budget
- Create use cases for all the personas
- Use cases must be consistent with personas
- Derive feature list from use cases and personas made

##LESSON 3 - CONSIDER MOBILE CONSTRAINTS ON DESIGNS

1. Battery and network limit - power and connectivity are precious resources
 * Conserve battery
 * Decide on what to sync or cache
 * Make decisions based on network connection  
2. Small screen - most mobile devices are small
 * Avoid imbalances, excessive white spaces, and very long texts which are unnatural to be read.
 * Some solutions:
       * Two pane layouts: Master detail views
       * Macro reflows: alternate layout for independent components (eg. image above text in portrait mode to image beside text in landscape mode)
       * Micro reflows: alternate representation; list in portrait mode to grid in landscape  
3. Handedness- consider the natural finger reach when using mobile phones
  * Touch areas must be big enough for fingers to press.
  * Decide on an experience which can be:
        * one-handed (thumb only)
        * two-handed (like when texting or sending chat messages)
        * two-handed assisted (using one hand to hold the phone and another hand to touch and scroll as in web browsing)  
4. Divided user attention - users have short attention spans
 - Deliver concise and straight to the point content
 - Make relevant commands available
 - Consider using notifications so that the user doesn’t always need to open the app

##LESSON 4 - MAKE APPS CONTEXT-AWARE

If context can be deduced, use it as much as possible.
Use Google APIs such as the Location API or built-in sensors such as proximity to know about the context of the app usage
