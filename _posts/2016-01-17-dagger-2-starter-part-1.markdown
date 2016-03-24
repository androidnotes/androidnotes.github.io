---
layout: post
title:  "Dagger 2 Starter - Part 1 - Declaring Dependencies"
date:   2016-01-17 00:00:00
categories: [starters, libraries]
description: "Dagger 2 is a dependency injection framework for Java. Learn about the basics of Dagger 2 by reading this multi-part starter."
---

## Dagger 2 Starter Series
[Part 0 - Dependency Injection Starter](http://androidnotes.github.io/starters/patterns/2016/01/16/dependency-injection-starter.html)   
Part 1 - Declaring Dependencies   
[Part 2 - Requesting Dependencies](http://androidnotes.github.io/starters/libraries/2016/01/23/dagger-2-starter-part-2.html)   
Part 3 - Scoping   

_____________________________________________

[Dagger 2](http://google.github.io/dagger/) is a dependency injection framework for Java. It is Google's improved version of [Dagger](http://square.github.io/dagger/) by Square. If you are not familiar with dependency injection, you need to take a look at this [Dependency Injection Starter](http://androidnotes.github.io/starters/patterns/2016/01/16/dependency-injection-starter.html) then continue with this article.

## Why use Dagger?
Usually, classes have dependencies and these dependencies have dependencies as well. Like other DI frameworks, Dagger 2 organizes all your dependencies and the dependencies of your dependencies and links them together, forming a dependency graph, a dependency acyclic graph to be specific, or a DAG. This is hard to manage manually. You will end up having tons of boilerplate code. You have to think about how to provide object instances, how to resolve transitive dependencies, how to inject dependencies to anyone who needs them, how to manage object instances or scopes, etc.. Dagger simply lets you define dependencies for the DAG and helps you draw out dependencies from it without worrying about the complications of dependency injection itself hence the name DAGger.

## Declaring Dependencies
Dagger doesn't have superpowers to predict the future and supply dependencies that you have requested from it right off the bat. It has to know what objects to include in the graph and how to construct those objects. There are basically two ways to help Dagger with this:

###1. By Defining Modules and Provider methods
You simply have to create a *module* - a plain Java class with an `@Module` annotation. Inside it, you have to write methods annotated with `@Provides` and return the types that you want to be added to the DAG.

{% highlight java %}
@Module
public class DoughModule { // module class
   @Provides
   public Dough provideDough() { // provider method
      return new Dough();
   }
}
{% endhighlight %}

If a type depends on some other type in order to be instantiated (in other words, it has a transitive dependency), you are allowed to declare provider method parameters to be able to construct and configure the object.

{% highlight java %}
@Module
public class DoughModule {
   @Provides
   public Dough provideDough() { // Dough provider method is required to satisfy the KneadedDough dependency
      return new Dough();
   }

   @Provides
   public KneadedDough provideKneadedDough(Dough dough){ // Dough is passed as a parameter
     return new KneadedDough(dough); // KneadedDough needs a pre-made Dough to be constructed
   }
}
{% endhighlight %}

As mentioned earlier, Dagger is the one who will manage the satisfaction of all dependencies and their respective transitive dependencies for you. You only have to make sure that those transitive dependencies are going to be included in the DAG by creating provider methods that return the transitive dependencies' types or by following step #2.

It is also best to group modules and provider methods in a logical manner since they are really designed to be like that. You can define modules to include other modules to maintain reusability and modularity.

{% highlight java %}
@Module
public class CondimentsModule {  //Module that provides condiments
  @Provides
  public Salt provideSalt() {
    return new Salt();
  }
}


@Module {
  includes = (
    CondimentsModule.class // Include the Condiments module to the Dough module since the SaltedDough provider method below needs Salt from CondimentsModule
  )
}
public class DoughModule {
  @Provides
  public Dough provideDough() {
     return new Dough();
  }

  @Provides
  public KneadedDough provideKneadedDough(Dough dough){
    return new KneadedDough(dough);
  }

  @Provides
  public SaltedDough provideSaltedDough(KneadedDough kneadedDough, Salt salt){ // new SaltedDough provider method
    return new SaltedDought(kneadedDough, salt);
  }
}
{% endhighlight %}

If a provider method's type relies on objects that cannot be supplied by other provider methods in its enclosing module or any included module, those objects can be supplied by exposing setter methods or constructor parameters, storing them as fields, and accessing them in provider methods. This is useful if you do not have control over the instantiation of certain objects such as those that are made by the system (like Android's Context) or other third-party classes (like some of your favorite SDK's objects).

{% highlight java %}
@Module
public class DoughModule {

   private Tray tray; // let us assume that this is from a third-party source

   public DoughModule(Tray tray){ // we can set the value of the field in the constructor
     this.tray = tray;
   }

   //..

   @Provides
   public DoughOnTray provideDoughOnTray(SaltedDough saltedDough){
     return new DoughOnTray(tray, saltedDough); // a Tray instance is required by DoughOnTray
   }

   public void setTray(Tray tray){ // or we can use a setter method
     this.tray = tray;
   }
}
{% endhighlight %}

###2. By Annotating Class Constructors with `@Inject`
The second way to satisfy dependencies is by using the `@Inject` annotation. It has another purpose, its main purpose actually, which you will learn about later. For now, you just have to know that when a class constructor is annotated with `@Inject`, its type is automatically added to the DAG. There is therefore, no need to write a provider method in a module that returns the type.

{% highlight java %}
public class TomatoSauce {
  @Inject
  public TomatoSauce() {
    // whatever you do to initialize a tomato sauce
  }
}
{% endhighlight %}

## To be continued
So far, you already know how to declare types to be included in the DAG generation. Our efforts will be useless if we are not going to use those! In the next part, we will learn about how to tell Dagger to get dependencies from the DAG a.k.a the injection part in dependency injection.

### References
Dagger 2 Official Documentation - [http://google.github.io/dagger/](http://google.github.io/dagger/)
   Fragmented Podcast Episode 21 : Diving Deep with Dagger [http://fragmentedpodcast.com/episodes/021/](http://fragmentedpodcast.com/episodes/021/)
