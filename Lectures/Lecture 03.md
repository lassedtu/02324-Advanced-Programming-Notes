> Part 2 of 2, see [[Lecture 02]]

One major principle of good software design:
- Use exceptions to signal invalid states, not ambiguous return values

# Exceptions in Java

## Stack Example

A naive implementation of `pop()`:

```java
public E pop() {
	if (size > 0) {
		size--;
		return array[size];
	} else {
		return null;
	}
}
```

**Problem:** If the stack allows `null` values, we cannot distinguish between popping a real `null` and popping from an empty stack. This motivates using exceptions for error states.

## What Are Exceptions?

- Represent unexpected behavior or errors
- Interrupt normal control flow
- Propagate up the call stack until caught
- If uncaught, program terminates

## Throwing Exceptions

```java
public E rock() throws IllegalStateException {
	if (size < 1)
		throw new IllegalStateException("Can't pop empty Stack");
	return array[--size];
}
```

- `throw` creates and throws an exception
- `throws` declares that a method may throw an exception
- Code after `throw` is not executed

## Catching Exceptions

```java
try {
	System.out.println("Rock: " + rock());
} catch (IllegalStateException e) {
	System.out.println("Heeeelp! " + e.getMessage());
}
```

- Catch only what you can handle
- Let others propagate
- Lower layers throw, higher layers handle

## The `finally` Block

```java
try {
	...
} catch (...) {
	...
} finally {
	...
}
```

- `finally` always runs (cleanup, resources)

## Multiple Catch Blocks

```java
try {
	...
} catch (IllegalStateException e) {
	...
} catch (NullPointerException e) {
	...
}
```

- Only the matching catch executes

## Example: Selective Catching

```java
public void concert() {
	try {
		System.err.println("[0:ok] Rock is " + rock());
	} catch (IllegalStateException e) {
		System.err.println("[1:nok] Non-critical error: " + e.getMessage());
	} finally {
		System.err.println("[FINALLY] Finally!");
	}
	System.err.println("[2:ok] ¡Muchas gracias!");
}
```

- Only `IllegalStateException` is handled
- Other exceptions propagate

## Example: Re-throwing

```java
catch (NullPointerException e) {
	System.err.println("[2:HARD] Can't fix \"" + e.getMessage() + "\"");
	throw e;
}
```

- Log or cleanup before propagating
- Can wrap in custom exceptions

## Exception Hierarchy

```
Throwable
 ├── Error
 └── Exception
	   ├── RuntimeException
	   └── Other checked exceptions
```

### Errors
- `OutOfMemoryError`, `StackOverflowError` (not recoverable)

### Unchecked Exceptions (RuntimeException)
- `NullPointerException`, `IllegalArgumentException`, `IndexOutOfBoundsException`
- Do not need to be declared
- Represent programming errors

### Checked Exceptions
- `IOException`, `SQLException`, `FileNotFoundException`
- Must be declared with `throws`
- Compiler enforces handling

## Checked vs Unchecked

> If a client can reasonably recover, use a checked exception.
> If not, use an unchecked exception.

## Creating Your Own Exceptions

```java
public class MyException extends Exception {
	public MyException(String msg) {
		super(msg);
	}
	public MyException(String msg, Exception e) {
		super(msg, e);
	}
}
```

- Extend `Exception` for checked
- Extend `RuntimeException` for unchecked

# Design Principles

- Do not return `null` for error states
- Use exceptions to signal invalid states
- Catch only what you can handle
- Let the rest propagate
- No raw exception dumps in the console
- Separate core logic from UI/handling logic