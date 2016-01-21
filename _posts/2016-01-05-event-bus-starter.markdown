---
layout: post
title:  "Event Bus Starter"
date:   2016-01-05 00:00:00
categories: [starters, patterns]
description: "Learn about the basics of the popular event bus pattern!"
---
An event bus is basically an implementation of the publish-subscribe pattern.

One has to remember these components when working with event buses:

1. Publishers - classes that publish events
2. Subscribers - classes that listen/subscribe to events
3. Bus - the middle man (usually made available by an event bus library) that manages the events
4. Events - can be anything - a button click event, a successful server response event, a database loading failure event, etc which can be represented by POJOs

To know how events are relayed via an event bus, consider the following flow:

1. A publisher calls the bus and publishes an event through it.
2. A subscriber (or subscribers) hooks to the bus and listens for a specific event (or events) that interests it.
3. The bus distributes the event/s posted by the publisher to all subscribers listening to the same event.

![Event Bus Event Flow](/images/eventbusflow.gif)

A simple real-life example would be the process of delivering newspapers. The newspaper company (the publisher) prepares the day's papers (the events). The local news boy (the bus) then rides his bicycle and delivers the newspaper to the doors of those who are subscribed to the newspaper (the subscribers). There is no direct communication between the publisher and the subscriber.

![Event Bus is like newspaper delivery](/images/eventbusnews.gif)

For a concrete Android example, let's say we have an application that has an activity that houses a fragment. The fragment contains a button that must be clicked to initiate a login. The method that executes the login logic, for some reason, is in the activity. The fragment must then communicate with the activity to start the login.

![Simple Android example without Event Bus](/images/eventbusandroidsamplenobus.png)

Aside from using interfaces, the fragment and the activity can talk with each other using an event bus:

1. The fragment (the publisher) detects a login button click and calls the bus to publish a LoginStart event.
2. The activity (the subscriber) registers to the bus and listens for the LoginStart event.
3. Upon receiving the LoginStart event from the publisher (see #1), the bus looks for all subscribers waiting for LoginStart (see #2) then pushes the event to them.

![Simple Android example with Event Bus](/images/eventbusandroidsamplewithbus.png)

The two most popular event bus libraries for Android are [EventBus by Green Robot](https://github.com/greenrobot/EventBus) and [Otto by Square](http://square.github.io/otto/). Specific details on how to use them are found on their respective websites as well as information on their performance and advanced features.

We can use an event bus when components need to talk to each other and when the use of interfaces is cumbersome or impossible. They can be used to facilitate communication between services, activities, fragments or broadcast receivers.

Event buses however, make code extra difficult to debug as subscribers and producers can be anywhere in the project. Otto solves this problem by providing an [IntelliJ plugin](https://github.com/square/otto-intellij-plugin) for easier producer-subscriber navigation.
