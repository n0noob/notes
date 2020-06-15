# Modern Java In Action Key-Takeaways

## Chapter 1: Fundamentals

### Java 8, 9, 10 and 11: what's happpening?

#### Programming concepts that drove design of java 8
* **Stream processing**:
  * A stream is a sequence of data items that are conceptually produced one at a time.
  * Stream can perform many operations on each item parallelly leveraging multiple processor cores.
* **Passing code to methods with behaviour parameterization**:
  * This concept is formally called *behavior parameterization*.
  * Streams API is built on the idea of passing code to parameterize the behavior of its operations.
* **Parallelism and shared mutable data**:
  * This concept states that the behaviour provided *must be safe to execute concurrently* on different pieces of input.
  * Function being passed as parameter must not access shared mutable data to do its job.
  * Although, this rule can be overridden using *synchronized* keyword, it will force code to execute sequentially.

**NOTE** : Functional programming paradigm contains:
1. No shared mutable data
2. Ability to pass functions

#### Functions in Java
* Values are *first class citizens* of any programming language. Datastructures, classes and methods are *second class citizens*.
* Java 8 has made functions *first class citizens* just like Smalltalk and JavaScript.

* **Methods and lambdas as first-class citizens**:
  * Java 8 introduced *method reference operator (::)*. It implies "use this method as value". Example:
  ```
  File[] hiddenFiles = new File(".").listFiles(File::isHidden);
  ```
  * Lambdas were introduced so as to make passing functions convenient for the programmer.

#### Streams
* Collections is mostly about storing and accessing data, whereas Streams is mostly about describing computations on data.

#### Java modules
* Java 9 provides a module system that provide you with syntax to define modules containing collections of packages—and keep much better control over visibility and namespaces.

#### default Methods
* Adding a new method to an interface means all concrete classes must provide an implementation for it. Language designers have no control over existing implementations of Collection, so you have a dilemma: How can you evolve published interfaces without disrupting existing implementations?

* The Java 8 solution is to break the last link: an interface can now contain method signatures for which an implementing class doesn’t provide an implementation.

#### Optional
* Java 8 introduced the Optional<T> class that, if used consistently, can help you avoid null-pointer exceptions. It’s a container object that may or may not contain a value. Optional<T> includes methods to explicitly deal with the case where a value is absent, and as a result you can avoid null-pointer exceptions.
* It uses the type system to allow you to indicate when a variable is anticipated to potentially have a missing value. Consistently using Optional values creates a clear distinction between a missing value that’s planned for and a value that’s absent only because of a bug in your algorithm or a problem in your data.
* When a value is present, the Optional class wraps it. Conversely, the absence of a value is modeled with an empty optional returned by the method Optional.empty. This static factory method returns a special singleton instance of the Optional class.
* **Creating optional objects**:
  * Empty optional: Returns an empty optional instance
  ```
  Optional<Car> optCar = Optional.empty();
  ```
  * Optional from a non-null value: Returns a optional of object passed. Raises a NullPointerException if null object is passed.
  ```
  Optional<Car> optCar = Optional.of(car);
  ```
  * Optional from nullable value: Returns a optional of object passed.
  ```
  Optional<Car> optCar = Optional.ofNullable(car);
  ```
* **Extracting and transforming values from Optionals with map**:

  * Code that previously used to look like:
  ```
  String name = null;
  if(insurance != null) {
    name = insurance.getName();
  }
  ```
  Can now be transformed to:
  ```
  Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
  Optional<String> name = optInsurance.map(Insurance::getName);
  ```
  This way we can apply map in the same manner as we do in the case of stream of data. If the optional holds a value, the function reference passed will be applied on the same or else an empty value will be returned.

  * Chaining optional objects with flatMap:
  
