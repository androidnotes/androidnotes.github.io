---
layout: post
title:  "Dependency Injection Starter"
date:   2016-01-05 00:00:00
categories: [starters, patterns]
---
Dependency injection, simply stated, is the process of providing objects to those who need them from an external source.

*Dependency* - objects
<br />*Injection* - process of providing objects

Let's use the process that produces our favorite snack, pizza, to illustrate dependency injection with code.

{% highlight java %}
public class PizzaAssembler {

 private List<Topping> toppings;
 private Crust crust;
 private Sauce sauce;

 public PizzaAssembler() {
    this.toppings = new ArrayList<>("pepperoni","pineapple");
    this.crust = new ThinCrust();
    this.sauce = new PizzaSauce();
 }

}
{% endhighlight %}

This is how we normally do it. Not in real life though. When we assemble pizzas, we just toss in our ingredients and not make them from scratch. This is how we apply dependency injection to our code as well. We will not make objects inside our class but instead delegate the task of creating them to external sources. One way to do it is to just let `PizzaAssembler` take in instances of `Crust`, `List<Topping>` and `Sauce` via its constructor.

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

}
{% endhighlight %}

There are still other patterns on how to apply dependency injection left for you to be discovered. As you apply those patterns, your code base will get longer, more complicated, and bloated with boilerplate code. To reap the benefits of DI while avoiding this problem, several dependency injection frameworks were made available by awesome teams and it's up to you to apply them. You can take a look at this Dev Skim of Gregory Kick's talk on Dagger 2 to get started.
