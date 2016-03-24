---
layout: post
title:  "Dagger 2 Starter - Part 2 - Requesting Dependencies"
date:   2016-01-23 00:00:00
categories: [starters, libraries]
description: "In the previous article, we learned about what Dagger 2 is and the two ways to provide dependencies to the object graph. This time, we will tackle the ways on how to request for dependencies (a.k.a. injection) from the object graph."
---

##Dagger 2 Starter Series   
[Part 0 - Dependency Injection Starter](http://androidnotes.github.io/starters/patterns/2016/01/16/dependency-injection-starter.html)   
[Part 1 - Declaring Dependencies](http://localhost:4000/starters/libraries/2016/01/17/dagger-2-starter-part-1.html)   
Part 2 - Requesting Dependencies   
Part 3 - Scoping   

_____________________________________________

In the previous article, we learned about what Dagger 2 is and the two ways to provide dependencies to the object graph. This time, we will tackle the ways on how to request for dependencies (a.k.a. injection) from the object graph.

## Requesting Dependencies
To request for dependencies using Dagger 2, we mainly use the `@Inject` annotation which is part of the JSR-330 specification. Think of this step as a way to mark parameters or fields for injection. The steps are pretty straightforward but make sure though that Dagger knows how to supply the types that you are requesting by following the steps in the previous part of the series.

### 1. Via Constructor Injection
Adding `@Inject` to the constructor of a class will inject and instantiate all of the constructor's parameters. You can only annotate one constructor in a class and annotating one will also add the type itself to the object graph. With constructor injection, dependencies are injected automatically by Dagger.

{% highlight java %}
public class CheeseGrater {

  @Inject
  public CheeseGrater(Cheese cheese) { // The cheese constructor parameter will get injected
    // whatever you do to initialize a cheese grater
  }
  
}
{% endhighlight %}

### 2. Via Field Injection
You can also put `@Inject` annotations on class fields. This is very useful if you have no access to the class' constructor but still want to do injection. In this mode, injection is not automatically done by Dagger but has to be triggered manually via *components* (which we will talk about later). The fields will be `null` unless injection takes place.

{% highlight java %}
public class CheeseGrater {

  @Inject
  Cheese cheese; //A field. Cheese will get injected via a component.

  public CheeseGrater() {
    // whatever you do to initialize a cheese grater
  }
}
{% endhighlight %}

### 3. Via Method Injection
Method parameters can also be injected by annotating the method with `@Inject`. This is useful if you need things to get injected only after the object is created. Take note that method injection always happens after constructor injection and field injection.

{% highlight java %}
public class CheeseGrater {

  //...

  @Inject
  public void grateCheese(Cheese cheese){ //cheese will get injected
    // grate the cheese
  }
}
{% endhighlight %}

## Components

Only constructor parameters and method parameters of classes with the '@Inject' annotation in its constructor are injected by Dagger automatically. For fields to be injected, we need to do it manually through components. Components are objects that can deliver your dependencies from the object graph to injection targets. They also control `scoping` which you will be learning next time. To be able to use components, follow the following steps:

### 1. Define a component interface or abstract class
Write an interface that uses the `@Component` annotation. Make sure to include the modules that this component will use as well.

{% highlight java %}
@Component {
  modules = {
    MozarellaModule.class
  }
}
public interface CheeseComponent {
  //..
}
{% endhighlight %}

### 2. Add methods to the component interface
We need to "expose" the types that we want to be injected from the modules that we have included to the component. To do this, we need to define methods that either (A) return the type that we want or (B) accept an instance of a object whose members are injection targets. To achieve this, we must follow either one of the contracts below. We can define a mixture of both contracts in a single component.

#### A. Provision Methods Contract (a.k.a. return the type that I need contract)
Define a method that returns the type that we want to be injected, has a valid method name, and has no parameters.

{% highlight java %}
@Component {
  modules = {
    MozarellaModule.class
  }
}
public interface CheeseComponent {
  Mozarella getMozarellaCheese();
}
{% endhighlight %}

#### B. Member-injection Methods Contract (a.k.a. inject dependencies to all members of my class contract)
Define a method that has a valid name, has one parameter that contains fields that are injection targets, and has `void` or the parameter's type as return type.

{% highlight java %}
public class Pizza {
  @Inject
  MozarellaCheese cheese;

  //..
}

@Component {
  modules = {
    MozarellaModule.class
  }
}
public interface CheeseComponent {
  void inject(Pizza pizza); // has one parameter and returns void
  Pizza inject (Pizza pizza); // has one parameter and returns the same type as the parameter
}
{% endhighlight %}

### 3. Use Dagger's generated component to inject
After defining a component, build your project so that Dagger can generate an implementation for your component. It contains code that does the actual injection, controls instances, and other things that they don't want us to worry about. To finally do field injection, call the methods that you have defined using an instance of the generated component class whose name uses the `DaggerYourComponentName` format.

{% highlight java %}
@Component {
  modules = {
    MozarellaModule.class
  }
}
public interface CheeseComponent {
  void inject(Pizza pizza); // using contract A
  Pizza inject (Pizza pizza); // using contract B
}

public class Pizza {
  @Inject
  MozarellaCheese cheese; // this guy gets injected once the inject() method is called

  public void startPizzaApp() {
      DaggerCheeseComponent component = DaggerCheeseComponent.create(); // creates an instance of the component
      component.inject(this); // injects dependency to cheese since it's a member that wants to get injected (contract B)

      MozarellaCheese anotherCheese = component.getMozarellaCheese(); // gets another cheese (contract A)
  }
}

{% endhighlight %}

If one of the modules that you have included in your component needs another object in order to be instantiated (i.e. it has state), the `create()` method will not be available. You can instead get an instance of the generated component by using the component's builder which allows you to provide arguments to your modules as well.

{% highlight java %}
@Module
public class CheddarModule { // module with constructor

   private Milk milk;

   public CheddarModule(Milk milk){ //module constructor has parameters
     this.milk = milk;
   }

   @Provides
   public CheddarCheese provideCheddarCheese() {
      return new CheddarCheese(milk);
   }
}

@Component {
  modules = {
    CheddarModule.class
  }
}
public interface CheeseComponent {
  void inject(Pizza pizza);
}

public class Pizza {
  @Inject
  CheddarCheese cheese;

  public void startPizzaApp() {
      DaggerCheeseComponent.Builder componentBuilder = DaggerCheeseComponent.builder(); // get the component builder
      componentBuilder = componentBuilder.cheddarModule(new Milk()); // provide arguments to the module
      DaggerCheeseComponent component = componentBuilder.build(); // build the component
      component.inject(this); // inject members
  }
}

{% endhighlight %}

We already know how to provide dependencies to the object graph and have Dagger inject dependencies that we need. With this knowledge, we can already build a basic app using Dagger as a dependency injection library. In the next part of the series, we are going to learn how to setup our project with the Dagger library and also create a practical Android example to demonstrate what we've learned so far.

### References
Dagger 2 JavaDoc - [http://google.github.io/dagger/api/2.0/](http://google.github.io/dagger/api/2.0/)
