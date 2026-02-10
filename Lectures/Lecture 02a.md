> Part 1 of 2, see [[Lecture 02b]]

One major principle of good software design: 
- Avoid having duplicate code or very similar code at different places in your software project
> Reuse code by calling it, not copying it!

Problem with our Stack example (as is):
- If we also want to have a stack for other types of elements, we would either need to copy the code and change it for every specific type or implement it for the general class Object (instead of Integer)
# Generics

Here is an interface fora stack that has proper implementation for any type of element.

``` Java
public interface Stack<E> {
	void clear();
	
	E pop();
	
	E top();
	
	void push(E value);
	
	int size();
	
	default boolean isEmpty() {
		return size == 0;
	}
}
```

`<E>` is what's known as a type parameter. It works as a variable that contains a type, you can think of the places where `E` is used to be replaced with the type chosen later on.

- Instead of E, we could use any Java identifier for a generic type parameter
- By convention, a type parameter name is a single capital letter
	- `T` is often used for type, followed by `U`, `V`, etc.
- We will see later, that a class (and also a method) can have more than one type parameter, and that we can restrict the type that are used as the concrete type argument (bounded types, wildcards)

## Generic Stack Implementation

``` Java
public class LinkedListStack<E> implements Stack<E< {
	private StackElement<E> top = null;
	private int size = 0;
	
	public E pop() {
		if (top != null) {
			StackElement<E> element = top;
			top = element.next;
			size--;
			return element.value;
		}
		return null;
	}
	
	public void push(E value) {
		StackElement<E> newElement = new StackElement<>(value, top);
		size++;
		top = newElement;
	}
	[...]
}
```

`<>` is called the "diamond operator", this is shorthand for `<E>` (or any other identifier) and java infers it automatically, acting as though it's there.

In the definition of a generic class (with type parameter `E`), we can use the name `E` for different purposes:
- Declare a name `E` for the type parameter
- Use this name `E` as a type in the declaration of this class
	- Attribute of that type `E`
	- Variables of that type `E`
	- Parameters of that type `E`
	- Type argument in other generic types
- Create new objects of another generic type with `E` as its parameter with identifier for a generic type parameter.

> We cannot use the name `E` of this type for creating a new element of the type `E`!
> This is because we don't know what kind of constructors the provided class will have, also `E` might be an abstract class or interface which can never be instantiated.
> *Note, It is also not possible to create arrays of type `E` but there is a workaround*
> 	*(You should use `ArrayList` rather than `Array`)*

For using a generic type in variable declarations and for creating instances, we can use the type or class with concrete type arguments:

``` Java
private Stack<String> stack = new ArrayStack<>();
```

### Raw Use of The Generic Type

It is possible to use a generic type without providing a type argument:

``` Java 
private Stack stack = new ArrayStack();
```

This is called a raw use of the generic type (or raw type). Then this generic type will be using the type `Object` as type argument.

> **But don't do this!**
> Using raw types is considered bad style (but occurs in legacy code from before Java 5)

#### The `final` keyword
``` Java
private class StackElement<EE> {
	final private EE value;
	final private StackElement<EE> next;
	
	private StackElement(EE value, StackElement next) {
		this.value = value;
		this.next = next;
	}
}
```

> The `final` keyword was included in the slides, `final` denotes that the variable is "final" and can never be changed again after the declaration, like a constant in other languages.

# Bounded Types

Up to now, we can use any type as concrete parameter for generic types.
- **Good**: generic types work for every concrete type argument
- **Bad**: generic type cannot use any (specific) operation on objects of type `E`

Some generic data types make only sense for types `E` that provide some specific operations.

``` Java
[...]
public interface Comparable<T> {
/**
* ...
* @param o the object to be compared.
* @return a negative integer, zero, or a positive integer as this object  
* is less than, equal to, or greater than the specified object
*
* @throws NullPointerException if the specified object is null
* @throws ClassCastException if the specified object's type prevents it
* from being compared to this object.
**/
	public int compareTo(T o);
}
```

When we define a generic type, we can require that the type argument (for a parameter type) has certain methods. This is done by requiring that the type parameter extends another type.

#### Example
``` Java
public interface SortedList<E extends Comparable<E>> extends List<E> {
	[...]
}
```

> In this example, it is required that the type provided for `E` has a method `compareTo(E o)`
> Or more precisely that type `E` extends the type `Comparable<E>`
> 
> `<E extends Comparable<E>>` is called a bounded type. It requires that the type provided for `E` implements/extends interface `Comparable` (`E` is bounded or restricted).
> 
> In `SortedList` and its implementations all variables (or expressions) of type `E` have the operation `compareTo`) as of the provided type `E`.
> 
> *Note: An interface can extend an existing interface. It is debatable whether SortedList should extend the interface List (SortedList breaks the contract of List). This will be discussed at a later point in more detail.*

We can use bounded types only in the declaration of the type parameter (and nowhere else in the generic type).

> Continued in [[Lecture 02b]]