# Comparator
> Something (e.g. a device or object) that compares (two) values.

``` Java
@FunctionalInterface
public interface Comparator<T> {
	/** Compares its two arguments for order.
	* Returns a negative integer, zero, or a positive
	* integer as the first argument is less than,
	* equal to, or greater than the second.
	...
	* @param o1 the first object to be compared.
	* @param o2 the second object to be compared.
	...
	*/
	int compare(T o1, T o2);
	// ...
}
```

For some comparator `c`:
- `o1 < 02 if c.compare(o1,o2) < 0`
- `o1 > 02 if c.compare(o1,o2) > 0`
- `o1 = 02 if c.compare(o1,o2) = 0`

### Why `Comparator<? super E> c` makes sense

When a method accepts a `Comparator` as a parameter, using `Comparator<? super E>` is often the correct choice.

-   This wildcard means the comparator can compare objects of type `E` or any of its *supertypes*.

**Example:** Consider a `sort` method that takes a comparator:

``` Java
	// ...
	void sort(Comparator<? super E> C) throws UnsupportedOperationException;
	// ...
```

Suppose the argument for `C` is of type `Comparator<B>`, where `E` is a subtype of `B`.
- The `compare` method of `C` would have the signature `int compare(B o1, B o2)`.
- Since `E` is a subtype of `B`, we can successfully call this `compare` method with elements of type `E`. This is precisely the purpose of a comparator: to consume objects and compare them.

### What is `<? extends E>` good for?

The `<? extends E>` wildcard is useful when a generic type is designed to *produce* values that are `E` or a subtype of `E`.

-   It indicates that the concrete type replacing the wildcard `?` is `E` or a subtype of `E`.
-   **Example:** Consider a generic type `Example<? extends E>`, where `?` (or the concrete type `A` it stands for) is the return type of a method, such as `A someMethod(int n)`.
    -   We can safely use the result of a call to `someMethod` because the returned value (of type `A`) is guaranteed to be a subtype of `E`. This means our implementation knows how to handle it as an `E` (or more specific).

# Comparable

For sorted lists we either need to fix the used order (defined by a comparator) upfront (e.g. as a parameter in the constructor) or sort it when the order changes (which should not happen too often)

Or we could use a datatype that has a built-in order.  Java has a mechanism for that: 

``` Java
public interface Comparable<T> {

/**
* ﻿﻿Compares this object with the specified object for order. Returns a
* ﻿﻿negative integer, zero, or a positive integer as this object is less
* ﻿﻿than, equal to, or greater than the specified object.
*
...
*/
	public int compareTo(T o);
｝
```

> This is like the Comparator (except that it is called on the object itself implementing the interface directly and not on a separate Comparator). But the same conditions apply (must constitute an order)!

## Example (Assignment 3)

``` Java
public class Person implements Comparable {
	final public String name;
	final public double weight;
	
	Person(@NotNull String name, @NotNull double weight) {
		if (name == null || weight <= 0) {
			throw new IllegalArgumentException("...");
		}
		this.name = name;
		this.weight = weight;
	}
	
	// ...
	
	@Override
	public int compareTo(@NotNull Person o) {
		if (o == null) {
			throw new IllegalArgumentException("...");
		}
		int result = this.name.compareTo(o.name);
		if (result != 0) {
			return result;
		}
		
		return (int) Math.signum(this.weight-o.weight);
	}
	
	// ...
```

This `Person` class demonstrates how to implement the `Comparable` interface, allowing `Person` objects to be naturally ordered.

-   **Fields:**
    -   `final public String name;`: Stores the person's name, which is immutable after construction.
    -   `final public double weight;`: Stores the person's weight, also immutable after construction.

-   **Constructor:**
    -   `Person(@NotNull String name, @NotNull double weight)`: Initializes a `Person` object.
    -   It includes basic validation to ensure `name` is not null and `weight` is positive, throwing an `IllegalArgumentException` if these conditions are not met.

-   **`compareTo` Method:**
    -   `@Override public int compareTo(@NotNull Person o)`: This method is required by the `Comparable` interface and defines the natural ordering for `Person` objects.
    -   **Null Check:** It first checks if the `Person` object `o` being compared against is null, throwing an `IllegalArgumentException` if it is.
    -   **Primary Sort Key (Name):**
        -   `int result = this.name.compareTo(o.name);`: It first compares the names of the two `Person` objects. `String.compareTo()` provides a lexicographical comparison.
        -   `if (result != 0) { return result; }`: If the names are different (`result` is not zero), that difference determines the order, and the method returns `result` immediately.
    -   **Secondary Sort Key (Weight):**
        -   `return (int) Math.signum(this.weight-o.weight);`: If the names are identical (`result` was 0), the comparison falls back to the `weight`.
        -   `Math.signum()` returns -1.0, 0.0, or 1.0 depending on the sign of the difference. Casting it to `int` gives -1, 0, or 1, which is the expected return value for `compareTo`. This ensures that if names are the same, the person with the smaller weight comes first.

> The following two methods must be implemented in order to respect the contract of objects with respect to equals(), hashCode() and compareTo() methods, however these can be created automatically by IntelliJ

``` Java
	// ...
	
	@Override
	public boolean equals(Object o) {
	if (this == o) return true;
	
	if (o == null || getClass() != o.getClass())
		return false;
	
	Person person = (Person) o;
	return Double.compare(weight, person.weight) == 0 && 
		Objects.equals(name, person.name);
	}
	
	@Override
	public int hashCode() {
		return Objects.hash(name, weight);
	}
	
	// ...
```

# Sorted List

> A sorted list maintains the order of all its elements at all times
> The needed order is fixed on creation (we do it by requiring a type extending Comparable)
> Each operation that adds an element must find the position where the element fits in (linear search or even better binary search)
> Some operations on lists do not make sense anymore on sorted lists, these operation throw an exception!

``` Java 
public interface SortedList<E extends Comparable<E>> extends List<E> {
 
	@Override
	default E set(int pos, @NotNull E e) 
		throws UnsupportedOperationException { 
		throw new UnsupportedOperationException
			("Operation set(int pos, E e) not allowed on SortedLists"); 
	}
	
	@Override
	default boolean add(int pos, @NotNull E e) 
		throws UnsupportedOperationException { 
		throw new UnsupportedOperationException("..."); 
	}
	
	@Override
	default void sort(@NotNull Comparator c) 
	$throws UnsupportedOperationException {
		throw new UnsupportedOperationException("..."); 
	}
	
}
```