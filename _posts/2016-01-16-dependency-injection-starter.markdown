---
layout: post
title:  "Dependency Injection Starter"
date:   2016-01-16 00:00:00
categories: [starters, patterns]
description: "A quick way to wrap your head around dependency injection in Java."
---
A **dependency** is a type where another type is dependent on.
**Dependency injection**, simply stated, is a way of providing dependencies to types that need them.

## Pizza Dependencies

To further illustrate dependency injection, let's use everyone's favorite snack. We will have a class that assembles the pizza's ingredients, the class' dependencies, for us.

{% highlight java %}
public class PizzaAssembler {

 private List<Topping> toppings;
 private Crust crust;
 private Sauce sauce;

 public PizzaAssembler() {
    this.toppings = new ArrayList<>("pepperoni","pineapple");
    this.crust = new Crust("thin");
    this.sauce = new Sauce("tomato");
 }

 public Pizza assemble() {
   // call this method to finally get a pizza
 }

}
{% endhighlight %}

This is how we usually do it. We create instances of the ingredients/dependencies that we need inside the class, usually in the constructor. You can argue that it is the fastest way but it's definitely not the best one. I'll give you a couple of reasons:

1. We are forced to always assemble pineapple pepperoni pizzas. I mean, we love all kinds of pizzas, right? Hard-coding the instantiation of dependencies inside a class makes the class inflexible and not reusable.
2. Since we cannot reuse `PizzaAssembler`, we will always create a copy of it for each unique combination of ingredients that we want. If the `Crust` class suddenly required us to pass another argument (eg. flour type), we have no choice but to update all our pizza assemblers. Talk about maintenance nightmares.

Even in real life, when we assemble pizzas, we just toss in our ingredients and not make them from scratch!

## Fixing the Pizza Assembler

The obvious solutions to our problems is to not instantiate objects in our class and to delegate the task of creating them to external classes instead. One way to do it is to just let `PizzaAssembler` take in instances of `Crust`, `List<Topping>` and `Sauce` via its constructor.

{% highlight java %}
public class PizzaAssembler {

 private List<Topping> toppings;
 private Crust crust;
 private Sauce sauce

 public PizzaAssembler(List<Topping> toppings, Crust crust, Sauce sauce){
    this.toppings = toppings;
    this.crust = crust;
    this.sauce = sauce
 }

  public Pizza assemble() {
    // call this method to finally get a pizza
  }

}
{% endhighlight %}

Both of our problems are gone now.  We have just set the pizza assembler free from the weight of worrying about how to make ingredients and let it focus only on assembling. Aside from constructors, there are still other ways or patterns on how to provide dependencies (e.g. factories, services) left for you to discover. They all basically aim to make classes decoupled, reusable, and easy to maintain. As we apply those patterns, our code bases will get longer, more complicated, and bloated with boilerplate code. To avoid these problems while reaping the benefits, awesome teams came up with dependency injection frameworks/libraries.

## No more DIY DI
Dependencies, most likely, have dependencies of their own and those dependencies of dependencies might have dependencies as well. This creates a complicated web/graph of dependencies called the object/dependency graph. DI frameworks help you generate this graph by defining and providing dependencies beforehand via configuration files or annotations. Aside from this, they also support several ways of doing injection such as constructor injection or injector methods. Gregory Kick, a member of the core Java team at Google delivered a talk that briefly touched existing Java DI frameworks and their capabilities. You can take a look at this [dev skim](http://androidnotes.github.io/skims/libraries/2016/01/05/dagger2-new-type-of-dependency-injection.html) for a summary of the talk.

## The JSR 330
In an effort to standardize dependency injection in Java, a team of experts came up with a Java Specification Request. It standardized the way DI is done in Java by defining a set of annotations and a user-friendly API for injection. All of these can be found under the `javax.inject` package. JSR 330 is being followed by well-known DI frameworks such as Spring, Guice, Dagger and Dagger 2. Learning about this specification makes understanding just any DI framework in Java easier.

### References
JSR 330: Dependency Injection for Java -  [https://jcp.org/en/jsr/detail?id=330](https://jcp.org/en/jsr/detail?id=330)
Dependency Injection Wiki - [https://en.wikipedia.org/wiki/Dependency_injection](https://en.wikipedia.org/wiki/Dependency_injection)
