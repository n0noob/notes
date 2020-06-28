# Modern Java In Action Key-Takeaways

## Chapter 1: Fundamentals

### Java 8, 9, 10 and 11: what's happening?

#### Programming concepts that drove design of java 8
* **Stream processing**:
  * A stream is a sequence of data items that are conceptually produced one at a time.
  * Stream can perform many operations on each item parallelly leveraging multiple processor cores.
* **Passing code to methods with behavior parameterization**:
  * This concept is formally called *behavior parameterization*.
  * Streams API is built on the idea of passing code to parameterize the behavior of its operations.
* **Parallelism and shared mutable data**:
  * This concept states that the behavior provided *must be safe to execute concurrently* on different pieces of input.
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

---
---
## Behavior parameterization
---
### Introduction
* *Behavior parameterization* is a software development pattern that lets you handle frequent requirement changes. For instance, you could pass the block of code as an argument to another method that will execute it later.
* In other words, it is the ability to tell a method to take multiple behaviors (or strategies) as parameters and use them internally to accomplish different behaviors. This is what is defined in Strategy Design Pattern where you use interface to represent the entity but underlying logic or strategy can be implemented by various classes.
* *java.util.function* package defines various interfaces whose *strategy* we can implement and pass through the code.
* Behavior parameterization can be achieved by various ways:
  * **Passing classes** (containing the code to be passed)
  * **Passing Anonymous** classes (implementing only the required method)
  * **Passing Lambdas**

---
---
## Optional
---
### Introduction
* Java 8 introduced the Optional<T> class that, if used consistently, can help you avoid null-pointer exceptions. It’s a container object that may or may not contain a value. Optional<T> includes methods to explicitly deal with the case where a value is absent, and as a result you can avoid null-pointer exceptions.
* It uses the type system to allow you to indicate when a variable is anticipated to potentially have a missing value. Consistently using Optional values creates a clear distinction between a missing value that’s planned for and a value that’s absent only because of a bug in your algorithm or a problem in your data.
* When a value is present, the Optional class wraps it. Conversely, the absence of a value is modeled with an empty optional returned by the method Optional.empty. This static factory method returns a special singleton instance of the Optional class.

### Creating optional objects
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

### Extracting and transforming values from Optionals with map

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


### Chaining optional objects with flatMap

  * One thing to note is that map returns an Optional<T> where T is the object which is returned by the function inside map. So, `flatmap` should be used instead of `map` alone so as to flatten Optional<Optional<T>>, if found anywhere. Example:
  ```
  public String getCarInsuranceName(Optional<Person> person) {
    return person.flatMap(Person::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("Unknown");
  }
  ```

  * **NOTE**: Optional class wasn't intended for use as a field type, it doesn't implement Serializable interface. Alternatively, if you need to have a serializable domain model, we suggest that you at least provide a method allowing access to any possibly missing value as an optional, as in the following example:
  ```
  public class Person {
      private Car car;
      public Optional<Car> getCarAsOptional() {
          return Optional.ofNullable(car);
      }
  }
  ```

### Manipulating a stream of optionals
  * The Optional’s stream() method, introduced in Java 9, allows you to convert an Optional with a value to a Stream containing only that value or an empty Optional to an equally empty Stream.
  * This technique can be particularly convenient in a common case: when you have a Stream of Optional and need to transform it into another Stream containing only the values present in the nonempty Optional of the original Stream.
  * Example:
  ```
    Stream<Optional<String>> stream;
    Set<String> result = stream.filter(Optional::isPresent)
                                .map(Optional::get)
                                .collect(toSet());
  ```

### Methods for unwrapping the optionals
  * **get()** : Simplest but also the least safe of these methods. It returns the wrapped element if it exists and throws NoSuchElementException otherwise. For this reason, using this method is almost always a bad idea unless you’re sure that the optional contains a value.
  * **orElse()** : It allows you to provide a default value when the optional doesn’t contain a value.
  * **orElseGet(Supplier<? extends T> other)** : It is the lazy counterpart of the orElse method, because the supplier is invoked only if the optional contains no value. You should use this method when the default value is time-consuming to create (to gain efficiency) or you want the supplier to be invoked only if the optional is empty.
  * **or(Supplier<? extends Optional<? extends T>> supplier)** : Lazily provides a different Optional when the original one is empty.
  * **orElseThrow(Supplier<? extends X> exceptionSupplier)** : It is similar to the get method in that it throws an exception when the optional is empty, but it allows you to choose the type of exception that you want to throw.
  * **ifPresent(Consumer<? super T> consumer)** : Lets you execute the action given as argument if a value is present; otherwise, no action is taken.

### Combining two optionals
  * **Problem**: Suppose you are supposed to find appropriate insurance for a given person (which may or may not be present) and given car (which may or may not be present)
  * **Trivial solution**:
  ```
  public Optional<Insurance> nullSafeFindCheapestInsurance(
                              Optional<Person> person, Optional<Car> car) {
      if (person.isPresent() && car.isPresent()) {
        return Optional.of(findCheapestInsurance(person.get(), car.get()));
      } else {
        return Optional.empty();
      }
  }
  ```
  * **Modern approach**:
  ```
  public Optional<Insurance> nullSafeFindCheapestInsurance(
                              Optional<Person> person, Optional<Car> car) {
      return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
  }
  ```
### Rejecting certain values with filter
  * **Problem** : Check on some property of an object.
  * **Trivial Solution** :
  ```
  Insurance insurance = ...;
    if(insurance != null && "CambridgeInsurance".equals(insurance.getName())){
      System.out.println("ok");
  }
  ```
  * **Modern approach** :
  ```
  Optional<Insurance> optInsurance = ...;
  optInsurance.filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
                .ifPresent(x -> System.out.println("ok"));
  ```

### Practical Examples
  * **Wrapping a potentially null value in an optional**:
    One can either use a traditional null check with an possible null value or can wrap it with Optional.ofNullable() like this:
  ```
  Optional<Object> value = Optional.ofNullable(map.get("key"));
  ```

  * **Exceptions vs Optional**:
    Throwing an exception is another common alternative in the Java API to returning a null when a value can’t be provided. A typical example is the conversion of String into an int provided by the Integer.parseInt(String) static method. In this case, if the String doesn’t contain a parseable integer, this method throws a NumberFormat-Exception. Once again, the net effect is that the code signals an invalid argument if a String doesn’t represent an integer, the only difference being that this time, you have to check it with a try/catch block instead of using an if condition to control whether a value isn’t null.
    Example:
  ```
  public static Optional<Integer> stringToInt(String s) {
      try {
          return Optional.of(Integer.parseInt(s));
      } catch (NumberFormatException e) {
          return Optional.empty();
      }
  }
  ```
---
---
## The Java Module System
---
### Introduction
  * *Java Module System* was released in Java 9. This feature was developed within project *Jigsaw*, and its development took almost a decade.
  * Since year 2000, java has been using module system named OSGi(Open Services Gateway initiative) which wasn't formally a part of Java platform. Framework examples : Apache Felix and Equinox (which Eclipse uses)

### The driving force
  * **Separation of concerns**:
    * *Separation of concerns (SoC)* is a principle that promotes decomposing a computer program into distinct features. Separate modules are formed that contain cohesive groups of code that have little overlap.
    * Java 9 modules give you finer-grained control of which classes can see which other classes and allow this control to be checked at compile time.
  * **Information Hiding**:
    * This principle encourages hiding implementation details. By hiding implementation details, you can reduce the chances that a local change will require cascading changes in other parts of your program.
    * We control the visibility of methods, fields and classes using visibility modifiers: public, protected, package-level and private. However, their granularity isn’t fine enough in many cases, and you could be obliged to declare a method public even if you didn’t intend to make it accessible for end users.

### Why Java Module System was designed?
  * **Modularity limitations**:
    * Before Java 9, there were three levels to group code : classes, packages and JARs.
    * At class level we had access modifiers and encapsulation to control access but very little encapsulation at the package and JARs level.
    * *Limited Visibility control*:
      * If you want classes and interfaces from one package to be visible to another package, you have to declare them as public. As a consequence, these classes and interfaces are accessible to everyone else as well.
      * Worse, this situation is bad from a security point of view because you potentially increase the attack surface as more code is exposed to the risk of tampering.
    * *Classpath*:
      * The class path has no notion of versioning for the same class. You cannot predict what will happen if different versions of the same library with same class is present in the classpath.
      * The class path doesn’t support explicit dependencies; all the classes inside different JARs are merged into one bag of classes on the class path. In other words, the class path doesn’t let you declare explicitly that one JAR depends on a set of classes contained inside another JAR.
  * **Monolithic JDK**:
    * JDK has grown and increased considerably in size. Many technologies were added and later deprecated. It doesn't matter if you are using those technologies, they will come shipped along with JDK. This situation becomes more problematic in applications which are meant to run on mobile devices or cloud.
  * **Comparision OSGi**:
    * When running inside an OSGi framework, a single bundle can be remotely installed, started, stopped, updated, and uninstalled without a reboot.
    * The possibility of hot-swapping different subparts of your application without the need to restart it probably is the main advantage of OSGi over Jigsaw.

### Java modules: The Big Picture
  * Java 9 provides a new unit of Java program structure: the *module*. A module is introduced with a new keyword module, followed by its name and its body. Such a module descriptor lives in a special file: *module-info.java*, which is compiled to *module-info.class*.
  * The body of a module descriptor consists of clauses, of which the two most important are requires and exports. The former clause specifies what other modules your modules need to run, and exports specifies everything that your module wants to be visible for other modules to use.
  * A module descriptor describes and encapsulates one or more packages (and typically lives in the same folder as these packages), but in simple use cases, it exports (makes visible) only one of these packages.
  * It is helpful to think of the exports and requires parts of a module as being respectively like the lugs (or tabs) and holes of a *jigsaw puzzle* (which is perhaps where the working name Project Jigsaw originated).
### Working with Modules
  * See this example of how to place module-info.java while using Java Module System.
    Example:
  ```
  |─ expenses.application
    |─ module-info.java
    |─ com
      |─ example
        |─ expenses
          |─ application
            |─ ExpensesApplication.java

  |─ expenses.readers
    |─ module-info.java
    |─ com
      |─ example
        |─ expenses
          |─ readers
            |─ Reader.java
          |─ file
            |─ FileReader.java
          |─ http
            |─ HttpReader.java
  ```
  * Modules can be exported and imported in the following manner:
  ```
  module expenses.readers {
    requires java.base;

    exports com.example.expenses.readers;
    exports com.example.expenses.readers.file;
    exports com.example.expenses.readers.http;
  }
  ```
