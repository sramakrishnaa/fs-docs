---
title: Using `new` Keyword
description: Learn about the different ways to create String objects using the `new` keyword in Java.
---

# Using `new` Keyword

#### Basic Syntax

```java title="Java"
String s = new String("content");
```

---

## How It Works

When you create a String using the `new` keyword:

1. **Creates a new String object** in the heap memory (not in String Pool)
2. **Does NOT reuse** existing strings from the String Pool
3. **Creates a separate object** even if identical content exists in the pool
4. **Can be forced into pool** using the `intern()` method

---

## Literal vs `new` Keyword

```java title="Java"
// Using literal
String s1 = "Hello";          // Stored in String Pool
String s2 = "Hello";          // Reuses object from pool
System.out.println(s1 == s2); // true (same reference)

// Using new keyword
String s3 = new String("Hello");  // New object in heap
String s4 = new String("Hello");  // Another new object in heap
System.out.println(s3 == s4);     // false (different references)

// Comparing literal with new
System.out.println(s1 == s3);     // false (different memory locations)
System.out.println(s1.equals(s3)); // true (same content)
```

---

## Memory Allocation Details

When you write `new String("literal")`, **TWO** objects are potentially created:

1. **String literal** `"literal"` in the String Pool (if not already present)
2. **New String object** in the heap memory

```java title="Java"
String s1 = new String("Java");
// Object 1: "Java" literal in String Pool (if not already there)
// Object 2: New String object in heap that references the pool literal

String s2 = new String("Java");
// Object 1: "Java" literal already exists in pool (reused)
// Object 2: Another new String object in heap

String s3 = new String("Java");
// Object 1: "Java" literal still in pool (reused)
// Object 2: Yet another new String object in heap
```

**Total Objects**: 1 in pool + 3 in heap = 4 objects for 3 variables!

**Visual Representation**

``` mermaid
%%{init: {'themeVariables': { 'fontSize': '12px' }} }%%
flowchart LR
subgraph Stack_Memory["Stack Memory"]
    A["s1"]
    B["s2"]
    C["s3"]
end

subgraph Heap_Memory["Heap Memory"]
    subgraph Heap_Objects["Heap Objects"]
        D["new String(''Java'') - object1"]
        E["new String(''Java'') - object2"]
        F["new String(''Java'') - object3"]
    end
    subgraph String_Pool["String Pool"]
        G["Java"]
    end
end

A --> D
B --> E
C --> F

D -.uses literal.-> G
E -.uses literal.-> G
F -.uses literal.-> G
```

---

## When to Use `new String()`

While generally discouraged, there are rare legitimate use cases when `new String()` is necessary:

### Use Case 1: Explicit New Object Creation (Rare)

```java title="Java"
// When you explicitly need a new object (very rare)
String pooled = "Hello";
String independent = new String(pooled);

System.out.println(pooled == independent);  // false
```

**Note**: This is almost never needed because Strings are immutable. A simple reference is usually sufficient:
```java title="Java"
String pooled = "Hello";
String reference = pooled;  // This is sufficient 99.9% of the time
```

### Use Case 2: Substring Memory Leak Prevention (Legacy)

In older Java versions (before Java 7), `substring()` shared the same character array as the original string. This could cause memory leaks if the original string was very large.

```java title="Java"
// Java 6 and earlier problem:
String huge = readHugeFile();  // 100 MB string
String tiny = huge.substring(0, 10);  // Just 10 characters

// Problem: 'tiny' still held reference to the 100 MB char array!
// The huge array couldn't be garbage collected

// Solution in Java 6:
String tiny = new String(huge.substring(0, 10));  // Creates independent copy
huge = null;  // Now the huge array can be GC'd
```

**Modern Java** (Java 7+): This is no longer an issue. Substring creates a new char/byte array, so defensive copying is unnecessary and the original string can be GC'd.

```java title="Java"
// Java 7+ - no memory leak, 'new String()' not needed
String huge = readHugeFile();
String tiny = huge.substring(0, 10);  // Creates independent String
huge = null;  // huge can be garbage collected
```

### Use Case 3: Testing Reference Equality

```java title="Java"
// For unit testing scenarios where you need guaranteed different objects
@Test
public void testReferenceEquality() {
    String s1 = new String("test");
    String s2 = new String("test");
    
    assertFalse(s1 == s2);  // Guaranteed different objects
    assertTrue(s1.equals(s2));  // But same content
}
```

### Use Case 4: Breaking Pool Reference (Very Rare)

```java title="Java"
// Scenario: Large string in pool that you want to eventually GC
String pooled = "Very large string content...";  // In pool
String copy = new String(pooled);  // Independent copy in heap

// If we later clear the pool reference:
pooled = null;
// The pooled string still exists in the pool and can't be GC'd
// But 'copy' is independent and can be used without keeping pool reference alive
```

**Note**: This is extremely rare and usually indicates a design problem.


---


## Performance Implications

```java title="Java"
// Example 1: Inefficient - creates 1000 new objects
for (int i = 0; i < 1000; i++) {
    String s = new String("Hello");  // 1000 separate objects in heap
}

// Example 2: Efficient - reuses 1 object
for (int i = 0; i < 1000; i++) {
    String s = "Hello";  // All 1000 references point to same pool object
}

// Memory usage:
// Example 1: ~1000 objects (wasteful)
// Example 2: 1 object (optimal)
```

---

## Benchmark Comparison

```java title="Java"
// Inefficient approach
long start = System.nanoTime();
for (int i = 0; i < 100_000; i++) {
    String s = new String("Benchmark");
}
long end = System.nanoTime();
System.out.println("new String(): " + (end - start) / 1_000_000 + " ms");

// Efficient approach
start = System.nanoTime();
for (int i = 0; i < 100_000; i++) {
    String s = "Benchmark";
}
end = System.nanoTime();
System.out.println("Literal: " + (end - start) / 1_000_000 + " ms");

// Typical results:
// new String(): 15-20 ms
// Literal: 1-2 ms (8-10x faster!)
```

---

## Common Mistakes with `new String()`

### Mistake 1: Unnecessary Object Creation

```java title="Java"
// ✗ BAD - creates 2 objects unnecessarily
String name = new String("John");

// ✓ GOOD - uses String pool efficiently
String name = "John";
```

### Mistake 2: Comparing with `==` Instead of `equals()`

```java title="Java"
String s1 = new String("Hello");
String s2 = new String("Hello");

// ✗ WRONG - compares references (always false with new)
if (s1 == s2) {
    System.out.println("Equal");  // Never executes
}

// ✓ CORRECT - compares content
if (s1.equals(s2)) {
    System.out.println("Equal");  // Correctly executes
}
```

### Mistake 3: Believing `new` Is Necessary for Mutability

```java title="Java"
// ✗ WRONG THINKING
String s = new String("Hello");  // "I need 'new' to modify this string"
s = s + " World";  // Modifying the string

// ✓ CORRECT UNDERSTANDING
// Strings are ALWAYS immutable, regardless of how they're created
String s = "Hello";  // Works exactly the same
s = s + " World";    // Creates new String object anyway
```

### Mistake 4: Using `new String()` for Empty Strings

```java title="Java"
// ✗ BAD
String empty = new String();

// ✓ GOOD
String empty = "";
```

---

## The `intern()` Method

You can manually add a String to the pool using `intern()`:

```java
String s1 = new String("Hello");  // Creates object in heap
String s2 = "Hello";              // References pool object

System.out.println(s1 == s2);     // false (different locations)

String s3 = s1.intern();          // Returns pool reference
System.out.println(s2 == s3);     // true (both reference pool)
System.out.println(s1 == s3);     // false (s1 still in heap)
```

### How `intern()` Works

1. Checks if string exists in pool
2. If yes: returns pool reference
3. If no: adds string to pool and returns new pool reference

```java
String heap1 = new String("Unique");
String heap2 = new String("Unique");

System.out.println(heap1 == heap2);  // false

String pool1 = heap1.intern();
String pool2 = heap2.intern();

System.out.println(pool1 == pool2);  // true (same pool reference)
```

---

## When to Use Each Approach

| Method | Use When | Avoid When |
|--------|----------|------------|
| **Literal** | Almost always | Never (always prefer this) |
| **new String()** | Very rare edge cases (testing, legacy code) | Regular string creation |
| **intern()** | Need to add dynamically created strings to pool | String already in pool |

---