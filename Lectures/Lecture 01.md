# Recapitulation

### Example 1
``` Java
class Pos {
	int x;
	int y;
}
class PosName {
	String name;
	Pos pos;
	public PosName(String name, Pos pos) {
		this.name = name;
		this.pos = pos;
	}
}
```

![[Example_1.png | center]]

``` Java
class Test {
	public static void main(String[] args) {
		Pos pos1 = new Pos();
		pos1.x = 1;
		pos1.y = 2;
		
		Pos pos2 = pos1;
		pos2.x = 3;
		pos2.y = 4;
	}
}
```

> Which values would the following expressions evaluate to when the program reaches this point? `pos1.x, pos2.x, pos1.y` og `pos2.y` And why?
#### My Answer

- `pos1.x = pos2.x = 3`
- `pos1.y = pos2.y = 4`

`pos1` does not contain the actual object of the class `Pos`, it contains a reference to where this object is located in memory, when `pos2` is set to be equal to `pos1` `pos2` becomes a pointer to the object `pos1`.

#### Professors Answer
**Stack**: Stores data in Last-In-First-Out (LIFO) order, like stacking plates. Used for method calls, local variables, and object references. Each method call creates a stack "frame" containing its variables and parameters. When the method completes, the frame is automatically removed. This structured organization makes the stack fast and efficient.

**Heap**: A flexible memory pool for objects that can be created and destroyed in any order. Objects persist here beyond individual method calls until Java's garbage collector determines they're no longer referenced and reclaims the memory. More flexible than the stack, but slower and requires active management.

- **`Pos pos1 = new Pos();`**

Java executes this in two steps:

1. `new Pos()` allocates memory in the **heap** for a new `Pos` object. This object contains instance variables `x` and `y`, which are initialized to their default values (likely `0` for integers).
2. `pos1` is created as a reference variable in the **stack**. It stores the memory address of the `Pos` object in the heap—think of it as an arrow pointing from the stack to the heap.

At this point: One object exists in the heap, and `pos1` in the stack points to it.

- **`pos1.x = 1;` and `pos1.y = 2;`**

Java follows the reference stored in `pos1` to locate the object in the heap, then modifies that object's fields directly. The object in the heap now has `x = 1` and `y = 2`.

- **`Pos pos2 = pos1;`**

Java creates a new reference variable `pos2` in the stack, but it doesn't create a new `Pos` object. Instead, `pos2` receives a _copy of the reference_ that's stored in `pos1`. Now both `pos1` and `pos2` contain the same memory address—they both point to the _exact same object_ in the heap.

Think of it like two people holding directions to the same house. They each have their own piece of paper (separate references in the stack), but both papers point to the same address (the same object in the heap).

- **`pos2.x = 3;` and `pos2.y = 4;`**

Java follows the reference in `pos2` to the object in the heap and modifies its fields. Since `pos2` points to the same object that `pos1` points to, these changes affect that shared object.

At the end of execution:

- **Stack**: Contains two reference variables, `pos1` and `pos2`, both storing the same memory address
- **Heap**: Contains exactly _one_ `Pos` object with `x = 3` and `y = 4`

---
## Example 2

``` Java
PosName posName1 = new PosName("position 1", pos1);
PosName posName2 = new PosName("position 2", pos2);

Pos[] posArray = new Pos[10]; 
for (int i = 0; i < posArray.length; i++) {
	posArray[i].x = i;
	posArray[i].y = i;
}

posArray[1] = pos1; 
posArray[2] = pos2;
```

**Line 1:** `PosName posName1 = new PosName("position 1", pos1);`
- **Stack:** Variable `posName1` stores a reference to the new PosName object
- **Heap:**
    - New `PosName` object created
    - String `"position 1"` created (or reused from string pool)
    - The `PosName.pos` field stores a reference to the existing `pos1` object

**Line 2:** `PosName posName2 = new PosName("position 2", pos2);`
- **Stack:** Variable `posName2` stores a reference to the new PosName object
- **Heap:**
    - New `PosName` object created
    - String `"position 2"` created (or reused from string pool)
    - The `PosName.pos` field stores a reference to the existing `pos2` object

**Line 3:** `Pos[] posArray = new Pos[10];`
- **Stack:** Variable `posArray` stores a reference to the array
- **Heap:**
    - Array object of length 10 created
    - All 10 elements initialized to `null` (default value for object references)

**Lines 4-7:** The `for` loop attempts to access `posArray[i].x` and `posArray[i].y`

**This code will crash with a `NullPointerException`** on the first iteration (i=0) because:

- `posArray[0]` is `null`
- You're trying to access `.x` on a null reference
- The loop never completes

The array was created, but the individual `Pos` objects were never instantiated. You'd need something like `posArray[i] = new Pos();` inside the loop before accessing the fields.

> If we added `posArray[i] = new Pos();` in the for loop the could would run as follows instead:

**Lines 4-7:** Each iteration creates a new `Pos` object

**Iteration 0:**

- **Heap:** New `Pos` object created with `x=0, y=0`
- `posArray[0]` now references this new object

**Iteration 1:**

- **Heap:** New `Pos` object created with `x=1, y=1`
- `posArray[1]` now references this new object

...and so on through iteration 9

**After the loop completes:**

- **Heap:** 10 new `Pos` objects exist (in addition to `pos1` and `pos2`)
- All array slots contain references to these objects

##### Array Reassignments - The Key Part

**Line 8:** `posArray[1] = pos1;`

- `posArray[1]` now points to `pos1` instead
- **The `Pos` object that was created with `x=1, y=1` is now orphaned** (no references to it)
- It becomes eligible for garbage collection

**Line 9:** `posArray[2] = pos2;`

- `posArray[2]` now points to `pos2` instead
- **The `Pos` object that was created with `x=2, y=2` is now orphaned**
- It also becomes eligible for garbage collection

##### Final State

- **8 objects** from the loop remain referenced: indices 0, 3, 4, 5, 6, 7, 8, 9
- **2 objects** created in the loop (at indices 1 and 2) are garbage
- `posArray[1]` and `posArray[2]` now reference the same objects as `pos1` and `pos2` respectively

---

# Interfaces

In Java there is no multiple inheritance: Each class can (directly) extend one class only, 
but each class can implement multiple interfaces. Multiple inheritance can (to some extent) be mimicked by a class implementing multiple interfaces.

Interfaces and their implementation provided means for good design of software systems:

- **Abstraction**: Focus on essential operations
- Hiding implementation details

### Example: Interface `Stack`
``` Java
public interface Stack {
	
	void clear();
	
	Integer pop();
	
	Integer top();
	
	void push(Integer value);
	
	int size();
	
	default boolean isEmpty() {
		return size() == 0;
	}
}
```

A stack has 5 operations:
- **clear()**: Removes all elements from the stack, making it empty.
- **pop()**: Removes and returns the top element of the stack. If the stack is empty, it may throw an exception or return null.
- **top()**: Returns (but does not remove) the top element of the stack. If the stack is empty, it may throw an exception or return null.
- **push(Integer value)**: Adds a new element (value) to the top of the stack.
- **size()**: Returns the number of elements currently in the stack.

The `default` keyword:
Used in interfaces to provide a method implementation. This allows interfaces to have concrete methods with a body, so implementing classes can use or override them.

### Implementation

``` Java
public class LinkedListStack implements Stack {
	private StackElement top = null;
	
	private int size = 0;
	
	public void clear() {
		top = null;
		size = 0;
	}
	
	public Integer pop() {
		if (top!=null) {
			StackElement element = top;
			top = element.next;
			size--;
			return element.value;
		}
		
		return null;
	}
```

- **Fields**:
  - `top`: Points to the top StackElement in the stack (or null if empty).
  - `size`: Tracks the number of elements in the stack.

- **clear()**: Sets `top` to null and `size` to 0, removing all elements from the stack.

- **pop()**: 
  - If the stack is not empty, it removes the top element:
    - Stores the current top in a temporary variable.
    - Updates `top` to the next element.
    - Decrements `size`.
    - Returns the value of the removed element.
  - If the stack is empty, returns null.

This implementation uses a (singly) linked list structure, where each StackElement points to the next, allowing efficient push and pop operations at the top of the stack.

### How the rest of the methods work

``` Java
public Integer top() {
	if (top != null) {
		return top.value;
	}
	
	return null;
}

public void push(Integer value) {
	StackElement newElement = new Stackelement(value, top);
	size++;
	top = newElement;
}

public int size() {
	return size;
}
```

``` Java
private class StackElement {
	private Integer value;
	
	private StackElement next;
	
	private StackElement(Integer value, StackElement next) {
		this.value = value;
		this.next = next;
	}
}
```

# ``
---
