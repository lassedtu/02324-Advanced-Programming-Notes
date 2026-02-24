This lecture contrasts two big viewpoints on programming: telling the computer how to do something (imperative) versus telling it what you want (declarative). We also revisit common paradigms (procedural, OO, functional), and connect them to concrete Java examples like lambdas, functional interfaces, and streams.

- Imperative vs declarative: how vs what
- Imperative: low-level, procedural, object-oriented
- Declarative: domain-specific (SQL), functional
- Lambda expressions, closures, currying, and streams

![[L7-prg-levels.png | center | 600]]

## Imperative vs declarative

**Imperative:** You describe the steps and state changes. You control the order of operations.

**Declarative:** You describe the goal. The system decides the steps.

Think of it like this:
- Imperative: a recipe with numbered steps.
- Declarative: a request to a chef ("give me a vegetarian lasagna").

### Example: split a stack by a pivot

Goal: return a clone where elements `< pivot` end up at the bottom and elements `>= pivot` end up on top, while keeping the original order within each group.

Imperative (explicit steps):

```java
public static <T extends Integer>
Stack<T> splitImperative(Stack<T> stack, T pivot) {
    Stack<T> lower = new Stack<>();
    Stack<T> upper = new Stack<>();
    Stack<T> result = new Stack<>();
    result.addAll(stack);
    while (!result.empty()) {
        T num = result.pop();
        if (Integer.compare(num, pivot) < 0) {
            lower.push(num);
        } else {
            upper.push(num);
        }
    }
    while (!lower.empty()) {
        result.push(lower.pop());
    }
    while (!upper.empty()) {
        result.push(upper.pop());
    }
    return result;
}
```

Declarative (describe the result):

```java
public static <T extends Integer>
Stack<T> splitDeclarative(Stack<T> stack, T pivot) {
    Stack<T> result = new Stack<>();
    result.addAll(Stream.concat(
        stack.stream().filter(e -> e < pivot),
        stack.stream().filter(e -> e >= pivot)
    ).toList());
    return result;
}
```

Note: `T extends Integer` allows us to safely use `Integer.compare` (LSP still holds).

### Example: sum 1..10 (Rust)

Imperative version shows explicit looping and state change:

```rust
let mut sum = 0;
for i in 1..11 {
    sum += i;
}
println!("{sum}");
```

Declarative version focuses on the result of a fold:

```rust
println!("{}", (1..11).fold(0, |a, b| a + b));
```

## Imperative programming (how?)

### Low-level and procedural

**Low-level programming** is close to hardware. Each instruction is specific to an architecture. This gives maximum control, but also maximum responsibility.

Assembly (compute area 3 x 7):

```asm
mv fp, sp
li t0, 3
li t1, 7
mv t2, t0
mv s1, t1
mul t2, t2, s1
mv s1, t2
addi sp, sp, -8
sw a7, 0(sp)
sw a0, 4(sp)
mv a0, s1
li a7, 1
ecall
```

Machine code for the same program (binary instructions executed by the CPU):

```text
00000000001000000000010000110011
00000000001000000000001010010011
00000000011100000000001100010011
00000000010100000000001110110011
00000000011000000000010010110011
00000010100100111000001110110011
00000000011100000000010010110011
11111111100000010000000100010011
00000001000100010010000000100011
00000000101000010010001000100011
00000000100100000000010100110011
00000000000100000000100010010011
00000000000000000000000001110011
```

**Procedural programming** introduces named procedures (functions) and abstracts some low-level details. It still relies on shared mutable state and explicit control flow.

```c
int foo() { return 42; }

char* bar(int N) {
  char result[N];
  for (int i = 0; i < N; i += foo())
    result[i] = i;
  return result;
}

void baz(char* str, ...) {}
```

```c
#include <stdio.h>
#define LEN (1u<<4)
char buf[LEN] = {'\0'};

int readout(char* fname) {
  FILE* f = fopen(fname, "r");
  if (0 == f) return -1;
  return fread(buf, LEN, sizeof(buf[0]), f);
}
```

Note: procedural code is flexible, but that flexibility puts pressure on good style and discipline.

### Object-oriented

Object-oriented programming organizes code around **objects** with encapsulated state and behavior.

Key ideas:
- **Encapsulation:** hide state behind methods.
- **Inheritance:** model "is-a" relationships.
- **Abstraction:** expose only what matters.
- **Polymorphism:** same call, different behavior depending on type.

Polymorphism types:
- **Overloading (syntactic):** same method name, different parameter list.
- **Overriding (semantic):** subclass replaces inherited behavior.

Method resolution in Java prefers the most specific class implementation, and then considers interface defaults:

![[L7-poly-ex.png | center | 600]]

## Declarative programming (what?)

### Domain-specific: SQL

SQL states *what* you want, not *how* to store or retrieve it. The database engine decides indexes, join strategy, and storage details.

```sql
CREATE TABLE Persons (
  LastName varchar(255),
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255)
);

INSERT INTO Persons (PersonID, LastName, FirstName, Address, City)
VALUES (1, 'Winston', 'Churchil', '123 Main St.', 'Anytown');

CREATE TABLE Debtors (
  LastName varchar(255),
  FirstName varchar(255)
);

SELECT Address, City
FROM Persons
INNER JOIN Debtors
ON LastName, FirstName;
```

### Functional

Functional style focuses on expressions, transformations, and functions as values.

Lambda syntax in Java:

```java
(parameter-list) -> { statements }
```

```java
(int x) -> { return x * x; }
(int x, int y) -> { return x + y; }
(int x, int y) -> x + y
```

Functional interface example (a single abstract method):

```java
interface GetOne<E> {
    E get(E[] array);
}

GetOne<Integer> first = a -> a[0];
GetOne<Integer> last = a -> a[a.length - 1];
```

Common functional interfaces (java.util.function):

| Interface | Explanation |
| --- | --- |
| `Function<T, R>` | Takes one value of type `T` and returns a value of type `R`. Use when you map one thing into another. |
| `Consumer<T>` | Takes one value of type `T` and returns nothing. Use for side effects (printing, mutation). |
| `BiFunction<T, U, R>` | Takes two values (`T`, `U`) and returns a value `R`. Use when a result depends on two inputs. |
| `BiConsumer<T, U>` | Takes two values and returns nothing. Use for side effects that need two inputs. |
| `BinaryOperator<T>` | A special case of `BiFunction` where both inputs and the output are the same type `T`. Useful for combining values. |
| `BiPredicate<T, U>` | Takes two values and returns a boolean. Use for yes/no checks on pairs. |
| `Predicate<T>` | Takes one value and returns a boolean. Use for filtering. |
| `Supplier<T>` | Takes no input and produces a value `T`. Use for factories or deferred computation. |

Consumer example (mutating a screen of pixels):

```java
Consumer<Pixel[][]> lighten = (Pixel[][] scr) -> {
    for (int i = 0; i < scr.length; i++) {
        for (int j = 0; j < scr[i].length; j++) {
            Integer[] values = scr[i][j].getValues();
            for (int k = 0; k < 3; k++) {
                values[k] = Math.min(values[k] + 11, Pixel.MAX_VALUE);
            }
        }
    }
};

lighten.accept(screen);
```

Function example (build a string from an array):

```java
Function<Integer[], String> glue = a -> {
    String result = "";
    for (int i = 0; i < a.length; i++) {
        result += a[i];
    }
    return result;
};
```

Closures: lambdas can capture variables outside their parameter list.

```java
Integer[] array = {0, 1, 2, 3};
Supplier<String> black = () -> {
    String result = "";
    for (int i = 0; i < array.length; i++) {
        result += array[i] + ",";
    }
    return result;
};
```

Functions as arguments (higher-order functions):

```java
BiConsumer<Pixel[][], Consumer<Pixel>> shader = (scr, op) -> {
    for (int i = 0; i < scr.length; i++) {
        for (int j = 0; j < scr[i].length; j++) {
            op.accept(scr[i][j]);
        }
    }
};
```

Returning a function (currying):

```java
Function<Integer[], Function<String, String>> curry =
    a -> gl -> {
        String result = "";
        for (int i = 0; i < a.length; i++) {
            result += a[i] + gl;
        }
        return result;
    };
```

Streams: map/reduce express transformations and aggregation without manual loops.

```java
List<Integer> listArray = new ArrayList<>(Arrays.asList(array));

System.out.println(
    listArray.stream().reduce((i1, i2) -> i1 + i2)
);

System.out.println(
    listArray.stream()
        .map(Object::toString)
        .reduce(String::concat)
        .orElseGet(() -> "")
);
```
