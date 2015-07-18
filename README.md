# EID Runtime Exceptions and Utilities
This small library holds a set of Exceptions that implements idea of fast, reusable, error codes that can be simple thrown fast in case of unpredictable and unrecoverable application failure.

## Caution

This classes shouldn't be used in any public API or library. It is designed to be used for in-house development of end user applications which will report Bugs in standarized error pages or post them to issue tracker.

## Requirements

* JDK >= 1.6

## Maven

```xml
<dependency>
    <groupId>pl.wavesoftware</groupId>
    <artifactId>eid-exceptions</artifactId>
    <version>0.1.0</version>
</dependency>
```

### `EidPreconditions` class

#### General use

Static convenience methods that help a method or constructor check whether it was invoked correctly (whether its preconditions have been met). These methods generally accept a `boolean` expression which is expected to be `true` (or in the case of `checkNotNull`, an object reference which is expected to be non-null). When `false` (or `null`) is passed instead, the `EidPreconditions` method throws an unchecked exception, which helps the calling method communicate to its caller that that caller has made a mistake.

Each method accepts a EID string or Eid object, which is designed to ease of use and provide strict ID for given exception usage. This approach speed up development of large application and helps support teams by giving both static and random ID for each possible unpredicted bug.

This approach is best to use with tools and plugins like [EidGenerator for Netbeans IDE](http://plugins.netbeans.org/plugin/53137/exception-id-eid-generator)

Example:

```java
/**
 * Returns the positive square root of the given value.
 * 
 * @param value value to be square rooted
 * @return the square root of input value
 * @throws EidIllegalArgumentException if the value is negative
 */
public static double sqrt(double value) {
  EidPreconditions.checkArgument(value >= 0.0, "20150718:012333");
  // calculate the square root
}

void exampleBadCaller() {
  // will throw EidIllegalArgumentException with "20150718:012333" as ID
  double d = sqrt(-1.0);
}
```
 
In this example, `checkArgument` throws an `EidIllegalArgumentException` to indicate that `exampleBadCaller` made an error in its call to sqrt. Exception, when it will be printed will contain user given EID and also randomly generated ID. Those fields can be displayed to end user on error page on posted directly to issue tracker.

Example:

```java
// Main application class for ex.: http servlet
try {
    performRequest(request, response);
} catch (EidRuntimeException ex) {
    issuesTracker.putIssue(ex);
    throw ex;
}
```
 
#### Functional try to execute blocks
 
Using functional blocks to handle operations, that are intended to operate properly, simplify the code and makes it more readable. It's also good way to deal with untested, uncovered `catch` blocks. It's easy and gives developers nice way of dealing with countless operations that supose to work as intended.

Example:

```java
InputStream is = EidPreconditions.tryToExecute(new RiskyCode<InputStream>() {
    @Override
    public InputStream execute() throws IOException {
        return this.getClass().getClassLoader()
            .getResourceAsStream("project.properties");
    }
}, "20150718:121521");
```
 
### Releases

- 0.1.0 (idea imported from Guava Library and COI code)