# Reactive Programming

## Introduction
* Reactive Streams was introduced in Java 9 in the form of _Flow API_. The Flow API enabled Java developers to use Reactive programming right from JDK without need for any external libraries. This is based on Observer-Subscriber Pattern.

* *Reactive Streams* is a standard for asynchronous stream processing with non-blocking back pressure.

* There are some other third party libraries that support reactive programming that include RxJava, Akka and Spring Reactor etc.

## Implications of Reactive Programming
* Increased performance – due to the possibility to handle huge volumes of data in a quick and stable way.
* Improved UX – due to the possibility to keep the application more responsive to its user.
* Simplified modifications and updates – due to more readable and easier to predict code.

## When Reactive Programming should be used?
* Reactive programming is best suited for application with high load and applications having enormous users. Some of the examples are _Social networks, Chat applications, Games, Audio and Video applications_.
* It also works great when used as back-end code that serves a highly interactive UI elements.
* Some domains where this can be useful are AI and machine learning and Real-time data streaming.

## Start Reactive Programming in Java
There are a number of ways to start reactive programming in Java till date. Following are some of the popular ways to start reactive programming in java:

* **Flow API**: Java 9 has made an attempt to bring *built-in reactivity* to java developers yet it has not been widely used till date. This was brought in the form of *Flow APIs*.
* **RxJava 3**: This is one of the third library that facilitates reactive programming in java. Initial version of RxJava has been there since Java 6 and was released in 2009. RxJava is a Java VM implementation of ReactiveX library which is used for composing asynchronous and event based programs by using observable sequences.
* **Project Reactor**: This one is Reactive-Streams compliant, as it is the more recent library. Its initial version was released in 2013. It represents the reactive API implementation for Spring 5. Reactor also supports non-blocking inter-process communication with the reactor-netty project. Suited for Microservices Architecture, Reactor Netty offers backpressure-ready network engines for HTTP (including Websockets), TCP, and UDP.
* **Akka**: This library was introduced in 2009. Akka along with Vertex are often implemented as reactive microservices which means the combination of microservices architecture and reactive system principles making distributed systems more flexible and reliable as well as increasing the performance.

## Flow API
### Main Interfaces
* A typical _Flow_ comprises of Publisher, Processor and Subscriber.
* A **Publisher**  is an entity in that publishes an event that can be processed further by the application. Every Publisher has one method – subscribe() which _Subscriber_ uses to subscribe to the event published by the _Publisher_.
* A **Subscriber** is an entity in the application that subscribes to an event.  Typically this is the end of the flow because it does not send the event further. This has four methods that need to be overridden – onSubscribe(), onNext(), onError(), and onComplete().
* A **Processor** is to be implemented if the message/event is to be transformed before reaching the subscriber who has subscribed to that event.