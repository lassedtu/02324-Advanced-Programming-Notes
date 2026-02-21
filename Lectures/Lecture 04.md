# Lists and Collections

One major principle of good software design:
- Use well-designed collections and data structures from established libraries

## Motivation and Overview

Lists and Collections are one of the most important built-in (generic) data types in Java. After understanding the fundamentals (Part I), you should use these built-in data types of Java wherever possible.

For now, you will implement your own slightly simplified versions of Collections (Stack and List) to:
- Provide understanding of API design
- Understand some of the inner workings of collections, and the use of interfaces, generics and exceptions in them
- Practice the steps of implementing algorithms:
  1. Conceptual idea
  2. Algorithm (idea)
  3. Program (in Java)

## 5.1. List: A Java Definition

### Definition

A list is, according to Merriam-Webster: _"a simple series of words or numerals (such as the names of persons or objects)"_

**Characteristics:**
- Contains multiple values or names (references to things)
- Things have a position on the list (and the order typically matters)
- In some types of list, the same thing can occur multiple times
- Have a certain number of things on them
- Some lists are sorted (see 5.2)

### List Interface

Here is the Java interface definition for a List:

``` Java
public interface List<E> {
	void clear();
	int size();
	default boolean isEmpty() { return size() == 0; }
	@NotNull E get(int pos) throws IndexOutOfBoundsException;
	@NotNull E set(int pos, @NotNull E e) throws IndexOutOfBoundsException;
	boolean add(@NotNull E e);
	boolean add(int pos, @NotNull E e) throws IndexOutOfBoundsException;
	@NotNull E remove(int pos) throws IndexOutOfBoundsException;
	boolean remove(E e);
	int indexOf(E e);
	default boolean contains(E e) { return indexOf(e) >= 0; }
	void sort(Comparator<? super E> c) throws UnsupportedOperationException;
}
```

**Key Methods:**
- **`clear()`**: Removes all elements from the list.
- **`size()`**: Returns the number of elements in the list.
- **`isEmpty()`**: A default method that returns true if the list is empty.
- **`get(int pos)`**: Retrieves the element at the specified position, throwing `IndexOutOfBoundsException` if the position is invalid.
- **`set(int pos, E e)`**: Replaces the element at position `pos` with element `e`.
- **`add(E e)`**: Adds element `e` to the end of the list.
- **`add(int pos, E e)`**: Inserts element `e` at position `pos`, shifting other elements as needed.
- **`remove(int pos)`**: Removes and returns the element at position `pos`.
- **`remove(E e)`**: Removes the first occurrence of element `e`.
- **`indexOf(E e)`**: Returns the position of the first occurrence of `e`, or -1 if not found.
- **`contains(E e)`**: A default method that returns true if the element is in the list.
- **`sort(Comparator<? super E> c)`**: Sorts the list using the provided comparator.

### Implementation Ideas for ArrayList

For an `ArrayList` implementation (Assignment 3a):

**Array-Based Storage:**
- Elements are stored in an array of a fixed size
- When the array is full and more elements are needed, the array must be extended (by creating a new larger array and copying elements)
- This is similar to how `ArrayStack` works in Assignments 1 & 2

**Insertion and Deletion:**
- When an element is added at a position or removed, all elements after it need to be moved
- Suggestion: implement two private helper methods:
  - `private void shiftElementsUpFrom(int pos)` - moves elements down (for removal)
  - `private void shiftElementsDownTo(int pos)` - moves elements up (for insertion)

**Searching:**
- The `indexOf()` method can be implemented by linear search
- Iterate through all positions in the array until you either find an equal element or reach the end of the list
- Remember: the passed element might be null (handle that case)

## 5.2. Searching and Sorting

Searching and sorting are one of the most common operations on lists. Here, we discuss some ways of implementing the methods `indexOf()` (Assignment 3a) and `sort()` (Assignment 3b). We also use these to show how to go from a conceptual idea to an algorithm to a program.

### Linear Search

The `indexOf()` method searches for an element in a list:

``` Java
int indexOf(String e) {
	for (int i = 0; i < size(); i++) {
		if (e.equals(get(i))) {
			return i;
		}
	}
	return -1;
}
```

This method iterates through the list from position 0 to the end. If it finds an element equal to `e`, it returns the position. If no element is found, it returns -1.

### Sorting: Bubble Sort

There are many different algorithms for sorting lists or arrays. One of the simplest (but not fastest) is **Bubble Sort**.

**Algorithmic Idea:**
While there are two neighbouring elements in the array that are out of order:
- Swap the numbers at these positions

**Conceptual Approach:**

We assume we have:
- `int[] values = ...` (the array to sort)
- `int size = ...` (the current size of the list)

To systematically find neighbouring numbers that are out of order, we need to:
1. Find pairs of adjacent elements that are in the wrong order
2. Swap them
3. Be sure that there are no such pairs anymore

### Bubble Sort Implementation (Version 1)

``` Java
boolean swapped;
do {
	swapped = false;
	for (int i = 0; i + 1 < size; i++) {
		if (values[i] > values[i+1]) {
			int v = values[i];
			values[i] = values[i+1];
			values[i+1] = v;
			swapped = true;
		}
	}
} while (swapped);
```

**How It Works:**
- The outer `do-while` loop repeats until no swaps are made in a complete pass
- The inner `for` loop compares each adjacent pair of elements
- If two adjacent elements are out of order, they are swapped and `swapped` is set to `true`
- When a complete pass happens with no swaps, the array is sorted

### Bubble Sort Observations

**Key Insight:**
- In the first iteration, the **largest** number "bubbles" to the last position of the array
- In the next iteration, the **next largest** number moves to the second-to-last position
- And so on...

This means we do not need to check the last element in subsequent iterations. We can optimize by reducing the range of the inner loop.

### Bubble Sort Optimized (Version 2)

``` Java
boolean swapped;
int j = size;

do {
	swapped = false;
	for (int i = 0; i + 1 < j; i++) {
		if (values[i] > values[i+1]) {
			int v = values[i];
			values[i] = values[i+1];
			values[i+1] = v;
			swapped = true;
		}
	}
	j--;
} while (swapped);
```

The optimized version introduces `j` to track where the sorted section of the array begins. After each pass, we decrement `j` because we know the last `j` elements are already in their final sorted positions.

## 5.3. Annotations in Java

### What Are Annotations?

Java annotations give additional information (_meta data_) about the code. They do not directly affect the execution of the code, but they provide information for:
- The compiler
- Build-time tools
- Runtime tools
- Development tools (like IDEs)

Annotations are applied to Java constructs such as:
- Classes
- Methods
- Attributes (fields)
- Variables
- Parameters
- Packages

### Built-in Annotations

Some Java annotations are built-in, meaning they are part of the Java language itself:

**`@Override`**
- Indicates that a method overrides a method from a superclass or implements a method from an interface
- Helps catch errors if the method signature doesn't match

**`@Deprecated`**
- Marks a class, method, or field as outdated
- The compiler will warn if the deprecated element is used
- Used when you want to discourage use of a particular API

**`@FunctionalInterface`**
- Marks an interface as a functional interface (exactly one abstract method)
- Helps prevent accidental addition of extra methods

**`@SuppressWarnings`**
- Tells the compiler to suppress specific warnings
- Useful when you intentionally do something that would normally cause a warning

### Imported Annotations

Some annotations come from external libraries and need to be imported. Examples include:

**`@NotNull`**
- Indicates that a parameter or return value cannot be null
- Often used for documentation and static analysis
- Part of the JSR-305 or similar annotation libraries

**`@Test`**
- Marks a method as a unit test (from JUnit)
- The testing framework recognizes and runs these methods

**`@Before`**
- Marks a method to run before each test (from JUnit)
- Used for test setup

**`@After`**
- Marks a method to run after each test (from JUnit)
- Used for test cleanup

### User-Defined Annotations

You can also define your own annotations:

``` Java
@interface MyAnnotation {
	String value();
	int count() default 0;
}
```

You can then apply your custom annotation to classes, methods, etc.:

``` Java
@MyAnnotation(value = "example", count = 5)
public class MyClass {
	// ...
}
```

## JavaDoc Tags

In JavaDoc comments, there are special tags that provide documentation information. These tags are sometimes called "annotations" but should not be confused with Java annotations.

**Example:**

``` Java
/**
 * A sequence of objects (elements) of generic type E, where the
 * position of the object in the sequence matters. Elements can be
 * inserted at the end of the list or at a specific position in
 * the list; elements can also be replaced or removed. But, elements
 * of the list cannot be null.
 * 
 * Note that there is a built-in interface in Java for that purpose:
 * {@link java.util.List} along with many different implementations.
 *
 * @param <E> the type of the list's elements
 * @author Ekkart Kindler, ekki@dtu.dk
 */
public interface List<E> {
	// ...
}
```

**Common JavaDoc Tags:**
- **`@param <type> description`**: Documents a type parameter or method parameter
- **`@return description`**: Documents the return value of a method
- **`@throws ExceptionType description`**: Documents an exception that may be thrown
- **`@author name`**: Documents the author of a class or method
- **`@deprecated description`**: Documents that a method or class is deprecated
- **`{@link reference}`**: Creates a link to another class, method, or package
- **`@see reference`**: References another class, method, or external resource

JavaDoc tags are used by the `javadoc` tool to generate HTML documentation. They help other developers understand your API better.
