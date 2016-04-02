---
layout: post
title:  "Dagger 2 Starter - Part 3 - Scoping"
date:   2016-03-20 00:00:00
categories: [starters, libraries]
description: "Remember variable scoping? It is the mechanism/rule that controls access to variables. Let's say we have declared a variable inside a method. That variable can only be accessed in that method. Once control exited the method, the variable will be gone and inaccessible. This very behavior is desirable for dependencies as well and Dagger 2 supports it."
---

## Dagger 2 Starter Series

[Part 0 - Dependency Injection Starter](http://androidnotes.github.io/starters/patterns/2016/01/16/dependency-injection-starter.html)   
[Part 1 - Declaring Dependencies](http://androidnotes.github.io/starters/libraries/2016/01/17/dagger-2-starter-part-1.html)   
[Part 2 - Requesting Dependencies](http://androidnotes.github.io/starters/libraries/2016/01/23/dagger-2-starter-part-2.html)   
Part 3 - Scoping   

_____________________________________________

Remember variable scoping? It is the mechanism/rule that controls access to variables.
Let's say we have declared a variable inside a method. That variable can only be accessed in that method. Once control exited the method, the variable will be gone and inaccessible. This very behavior is desirable for dependencies as well and Dagger 2 supports it.

## Why Scope?

Dagger creates new instances of objects every time they are injected when they are unscoped. If we want to work on a single instance of a dependency, we have to scope it first. Next, we want instances of our dependencies to be accessible only to certain parts of our application. For example, it makes sense that we only want a helper class designed for a particular fragment to be accessible only in the fragment it was defined for. In this regard, scoping helps in preventing concurrency and state issues.

## The 2 Dagger 2 Scopes

### 1. The Singleton Scope

Singletons are objects that only have single instances throughout the life of the application. It's the only scope that Dagger 2 supports right off the bat as it was part of the JSR330 standard. For now, remember that we need the `@Singleton` annotation to apply this scope to our  dependencies.

### 2. Custom Scopes

Let's say we want a dependency to be available only for a certain activity or a certain fragment or a certain whatever. Dagger 2 gives us the option to validate the dependency's custom scope by also using annotations. This time, there is a need to declare a custom annotation.

{% highlight java %}
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface ActivityScope {

}
{% endhighlight %}

Remember that custom scopes are also singletons. They also only have one instances but their life is not always tied to the life of the application and can instead be customized to work with smaller lifecycles (i.e. tie it with the lifecycle of an Activity, a Fragment, or a user login session).

Custom scope annotations also help with readability. Custom scope annotations can be strategically named to convey the intended scope of a dependency at a glance.

## How to Scope

### 1. Annotate dependency sources with the desired scope

If we want a certain dependency supplied by provider methods to be scoped, mark it with the desired scope's annotation.

{% highlight java %}
@Module
public class DoughModule {
   @Provides
   @ActivityScope // Relates the dependency returned by this method with this scope
   public Dough provideDough() { // provider method
      return new Dough();
   }
}
{% endhighlight %}

Since classes with constructors marked with `@Inject` are also added to the dependency graph, we can scope them as well by adding the scope annotation on the class name declaration.

{% highlight java %}
@ActivityScope // Relates this class with this scope
public class TomatoSauce {
  @Inject
  public TomatoSauce() {
    // whatever you do to initialize a tomato sauce
  }
}
{% endhighlight %}

### 2. Annotate components with the scope

Remember that through components, we can inject dependencies to class fields. Other than that, they play a big role in scoping dependencies. By annotating the component with a scope annotation, Dagger can verify if the scope of all the dependencies included in the component matches with the component's scope at compile-time. Compile-time errors can be thrown for any mismatch with providers unless they are unscoped.

{% highlight java %}
@ActivityScope // this scope annotation must match with the scope annotations of the component members
@Component {
  modules = {
    MozarellaModule.class
  }
}
public interface CheeseComponent {
  //..
}
{% endhighlight %}

Dagger may stop the compilation and warn you if it finds any one of these scenarios:

1. A component has no scope but at least one of its provider methods/classes has a scope.
2. A component has a scope but it differs from at least one of its provider method/s scopes.
3. A component depends on another component (a subcomponent) that has a conflicting scope.

After Dagger verifies all scope declarations in components and providers, it is up to the developer to manage the dependencies' lifecycles. The life of the instance of all dependencies included in a component depends on the life of the component instance itself. For instance, in Android, a component that has an `@ActivityScope` must live only as long as the activity lives. The logical thing to do is to build the component in `onCreate` and remove any reference to it in `onDestroy`.

## TL;DR

- An unscoped dependency will always return a new instance.
- A scoped dependency can either be annotated with `@Singleton` which means there's only one instance of it throughout the life of the application or a custom annotation which has to be declared manually.
- Dependencies can be scope-annotated on their provider methods or on their class definitions.
- Scope annotations are used to check for conflicts between declared scopes in components and in providers during compile-time.
- The life of a custom-scoped dependency depends on the life of instance of the component where it belongs.


## BONUS: Setting Up The Dagger 2 Stack
To use Dagger 2 in Android Studio, do the following:

1.) In your project's `build.gradle` file, add the [Android APT Gradle Plugin](http://code.neenbedankt.com/gradle-android-apt-plugin/)  by [Hugo Visser](https://plus.google.com/+HugoVisser). It separates the Dagger compiler from the Dagger library and adds Dagger-generated code to the source sets so that Android Studio will recognize it.   {% highlight java %}
buildscript {
    dependencies {
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
}
{% endhighlight %}
2.) In your application's `build.gradle` file, apply the Android APT Plugin then include the Dagger 2 library and compiler.
{% highlight java %}
apply plugin: 'com.neenbedankt.android-apt'

dependencies {
  compile 'com.google.dagger:dagger:2.0'
  apt 'com.google.dagger:dagger-compiler:2.0'
}
{% endhighlight %}
3.) The `@Generated` annotation used in Dagger generated code is included in Java 7. Since Android uses Java 6, we can have this annotation by including the javax.annotation dependency in the application's `build.gradle` as well.
{% highlight java %}
apply plugin: 'com.neenbedankt.android-apt'

dependencies {
  compile 'com.google.dagger:dagger:2.0'
  apt 'com.google.dagger:dagger-compiler:2.0'
  compile 'javax.annotation:jsr250-api:1.0'
}
{% endhighlight %}

### References
Dagger 2 JavaDoc - [http://google.github.io/dagger/api/2.0/](http://google.github.io/dagger/api/2.0/)   
Setting Up Dagger 2 for Android Studio - [http://stackoverflow.com/questions/28661693/dagger2-dependency-gradle](http://stackoverflow.com/questions/28661693/dagger2-dependency-gradle)   
