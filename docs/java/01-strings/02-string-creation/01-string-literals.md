---
title: String Literals
description: Learn about the basic syntax and characteristics of string literals in Java.
---

# String Literals

#### Definition
A **String literal** is a sequence of characters enclosed in double quotes. It's the most common, efficient, and recommended way to create strings in Java.

#### Basic Syntax

```java title="Java"
String variableName = "string content";
```

#### Simple Examples

```java title="Java"
String greeting = "Hello, World!";
String name = "John Doe";
String empty = "";                    // Empty string
String singleChar = "A";              // Single character (still a String, not char)
String withNumbers = "Java17";
String withSpaces = "Hello World";
String specialChars = "user@email.com";
```

---

## Characteristics of String Literals

### 1. Stored in String Pool

String literals are automatically stored in a special memory region called the **String Pool** (also known as *String Constant Pool* or *String Intern Pool*).

```java title="Java"
String s1 = "Hello";  // Created in String pool
String s2 = "Hello";  // Reuses the same object from pool
String s3 = "Hello";  // Also reuses the same object

// All three reference the SAME object
System.out.println(s1 == s2);  // true (same reference)
System.out.println(s2 == s3);  // true (same reference)
System.out.println(s1 == s3);  // true (same reference)

// Only ONE "Hello" object exists in memory
```

**Memory Advantage**: Multiple references to the same literal share a single object, significantly reducing memory consumption.

**Visual Representation**:
``` mermaid
%%{init: {'themeVariables': { 'fontSize': '12px' }} }%%

flowchart LR

subgraph Stack_Memory["Stack Memory"]
    A["s1"]
    B["s2"]
    C["s3"]
end

subgraph Heap_Memory["Heap Memory"]
    subgraph String_Pool["String Pool"]
        D["Hello"]
    end
end

A --> D
B --> D
C --> D
```

### 2. Compile-Time Creation

String literals are created at compile time and embedded directly in the bytecode (.class file).

```java title="java"
public class Demo {
    String message = "Welcome";  // "Welcome" is in the .class file
    
    public void greet() {
        String greeting = "Hello";  // "Hello" is also in .class file
    }
}
```

**Implication**: Literal strings exist before runtime, making them immediately available when the class is loaded.

### 3. Immutable and Safely Shared

Since literals are shared through the pool, they must be immutable to prevent one reference from affecting others.

```java title="Java"
String s1 = "Java";
String s2 = "Java";  // Points to same object as s1

s1 = "Python";  // Creates new reference, doesn't modify the original object

System.out.println(s1);  // "Python" (new reference)
System.out.println(s2);  // "Java" (unchanged, still points to original)
```

### 4. Memory Efficient

String pooling provides significant memory savings by reusing existing objects.

```java title="Java"
// Scenario 1: Using literals (efficient)
String str1 = "Java";
String str2 = "Java";
String str3 = "Java";
// Memory: 1 object, 3 references

// Scenario 2: Using new (inefficient)
String obj1 = new String("Java");
String obj2 = new String("Java");
String obj3 = new String("Java");
// Memory: 4 objects (1 in pool + 3 in heap), 3 references
```

---

## Types of String Literals

### Simple String Literals
```java title="Java"
String simple = "Hello";
String withNumbers = "Java123";
String withSpaces = "Hello World";
String punctuation = "Hello, World!";
```

### Empty String Literal
```java title="Java"
String empty = "";

System.out.println(empty.length());   // 0
System.out.println(empty.isEmpty());  // true
System.out.println(empty.isBlank());  // true (Java 11+)

// Empty string is NOT null
System.out.println(empty == null);    // false
```

#### Important Distinction
```java title="Java"
String empty = "";       // Empty string - object exists, length is 0
String nullStr = null;   // Null reference - no object exists

// empty.length();       // ✓ Works - returns 0
// nullStr.length();     // ✗ NullPointerException!
```

### Multi-line Strings (Traditional Approach)
```java title="Java"
// Before Java 15 - concatenation approach
String multiLine = "Line 1\n" +
                   "Line 2\n" +
                   "Line 3";

String paragraph = "This is a long paragraph that " +
                   "spans multiple lines for " +
                   "better code readability.";

System.out.println(multiLine);
// Output:
// Line 1
// Line 2
// Line 3
```

### Text Blocks (Java 15+)

Text blocks provide a cleaner way to write multi-line strings.

```java title="Java"
// Text block syntax (Java 15+)
String textBlock = """
    This is a text block
    that spans multiple lines
    and preserves formatting
    automatically
    """;

// Practical examples
String json = """
    {
        "name": "John",
        "age": 30,
        "city": "New York"
    }
    """;

String html = """
    <html>
        <body>
            <h1>Hello World</h1>
        </body>
    </html>
    """;

String sql = """
    SELECT id, name, email
    FROM users
    WHERE active = true
    ORDER BY name
    """;
```

#### Benefits of Text Blocks

- No need for escape sequences (`\n`, `\"`)
- Automatic indentation management
- Better readability for multi-line content
- Ideal for JSON, XML, HTML, SQL, etc.

---


## Escape Sequences in String Literals

Special characters require escape sequences to be represented in string literals.

### Common Escape Sequences Table

| Escape Sequence | Description       | Example          | Output                           |
| --------------- | ----------------- | ---------------- | -------------------------------- |
| `\n`            | Newline           | `"Line1\nLine2"` | Line1<br>Line2                   |
| `\t`            | Tab               | `"Col1\tCol2"`   | Col1&nbsp;&nbsp;&nbsp;&nbsp;Col2 |
| `\r`            | Carriage return   | `"Text\rOver"`   | Overwrites                       |
| `\\`            | Backslash         | `"C:\\Users"`    | C:\Users                         |
| `\"`            | Double quote      | `"Say \"Hi\""`   | Say "Hi"                         |
| `\'`            | Single quote      | `"It\'s fine"`   | It's fine                        |
| `\b`            | Backspace         | `"Tex\bt"`       | Tet                              |
| `\f`            | Form feed         | `"Page1\fPage2"` | Page break                       |
| `\uXXXX`        | Unicode character | `"\u0041"`       | A                                |

### Practical Examples

```java title="Java"
// File paths (Windows)
String windowsPath = "C:\\Users\\John\\Documents\\file.txt";
System.out.println(windowsPath);  // C:\Users\John\Documents\file.txt

// Quoted text
String quote = "She said, \"Java is awesome!\"";
System.out.println(quote);  // She said, "Java is awesome!"

// Multi-line text
String multiLine = "First Line\nSecond Line\nThird Line";
System.out.println(multiLine);
// Output:
// First Line
// Second Line
// Third Line

// Tabulated data
String table = "Name\tAge\tCity\nJohn\t30\tNY\nJane\t25\tLA";
System.out.println(table);
// Output:
// Name    Age    City
// John    30     NY
// Jane    25     LA

// Unicode characters
String unicode = "Hello \u4E16\u754C";  // Hello 世界
System.out.println(unicode);  // Hello 世界

String symbols = "\u2665 \u2663 \u2666 \u2660";  // ♥ ♣ ♦ ♠
System.out.println(symbols);  // ♥ ♣ ♦ ♠

// Emoji using Unicode
String emoji = "Smile: \uD83D\uDE00";  // 😀
System.out.println(emoji);  // Smile: 😀
```

### Single Quote vs Double Quote
```java title="Java"
// Single quotes don't need escaping in strings
String apostrophe = "It's a beautiful day";  // ✓ No escape needed
String contractions = "don't, can't, won't";  // ✓ Fine

// But you can escape if you want
String escaped = "It\'s still valid";  // ✓ Also works

// Double quotes MUST be escaped
String quoted = "He said \"Hello\"";  // ✓ Escape required
// String invalid = "He said "Hello"";  // ✗ Compilation error
```

---

## Compile-Time String Concatenation

The Java compiler automatically concatenates string literals at compile time, creating a single string.

### Basic Concatenation
```java title="Java"
// This code:
String message = "Hello" + " " + "World";

// Is compiled to:
String message = "Hello World";  // Single literal in bytecode

// Another example:
String longText = "This is a very long string " +
                  "split across multiple lines " +
                  "for better code readability";
// Compiled as: "This is a very long string split across multiple lines for better code readability"
```

### Literal-Only Concatenation

**Important**: Compile-time concatenation only works with literals, not variables or expressions.

```java title="Java"
// COMPILE-TIME concatenation (optimized)
String s1 = "Hello" + " " + "World";
// Compiler converts to: String s1 = "Hello World";

String s2 = "Java " + "Programming";
// Compiler converts to: String s2 = "Java Programming";

// RUNTIME concatenation (NOT optimized at compile time)
String part1 = "Hello";
String part2 = "World";
String combined = part1 + " " + part2;
// This uses StringBuilder at runtime (Java 9+) or new String objects (Java 8-)
```

### Constant Folding with Final Variables

Constant Folding in Java is a compiler optimization technique where the compiler evaluates constant expressions at compile time instead of computing them during program execution. This optimization can improve performance and reduce memory usage. When using `final` variables, the compiler can perform constant folding at compile time.

```java title="Java"
// Constant folding with final
final String GREETING = "Hello";
final String TARGET = "World";
String message = GREETING + " " + TARGET;
// Compiler can optimize this to: "Hello World"

// Without final - runtime concatenation
String greeting = "Hello";
String target = "World";
String message2 = greeting + " " + target;
// Uses StringBuilder at runtime (cannot be optimized at compile time)
```

#### Verification Example
```java title="Java"
final String CONST1 = "Hello";
final String CONST2 = "Hello";
String literal = "Hello";

String s1 = CONST1 + " World";
String s2 = CONST2 + " World";
String s3 = literal + " World";

System.out.println(s1 == s2);  // true (both optimized to "Hello World" literal)
System.out.println(s1 == s3);  // false (s3 created at runtime)
```

---

## Best Practices for String Literals

### 1. Prefer Literals Over `new String()`
 
```java title="Java"
// ✓ GOOD - uses String pool
String s1 = "Hello";

// ✗ BAD - creates unnecessary object in heap
String s2 = new String("Hello");
```

**Why**: Literals are memory-efficient and leverage the String Pool. Using `new` creates an extra object in the heap, wasting memory.

### 2. Use String Constants for Repeated Values

```java title="Java"
// ✗ BAD - string literals repeated multiple times
public class UserService {
    public void validateUser(String status) {
        if (status.equals("ACTIVE")) {
            // process active user
        }
    }
    
    public void checkPermission(String userStatus) {
        if (userStatus.equals("ACTIVE")) {
            // grant access
        }
    }
    
    public void logStatus(String status) {
        if (status.equals("ACTIVE")) {
            // log active status
        }
    }
}

// ✓ GOOD - define as constant, use consistently
public class UserService {
    private static final String STATUS_ACTIVE = "ACTIVE";
    private static final String STATUS_INACTIVE = "INACTIVE";
    private static final String STATUS_PENDING = "PENDING";
    
    public void validateUser(String status) {
        if (STATUS_ACTIVE.equals(status)) {
            // process active user
        }
    }
    
    public void checkPermission(String userStatus) {
        if (STATUS_ACTIVE.equals(userStatus)) {
            // grant access
        }
    }
    
    public void logStatus(String status) {
        if (STATUS_ACTIVE.equals(status)) {
            // log active status
        }
    }
}
```

#### Benefits

- Single point of definition (easier to maintain)
- Prevents typos
- Enables IDE autocomplete
- Better code documentation
  
### 3. Null-Safe String Comparisons

```java title="Java"
String input = getUserInput();  // Might return null

// ✗ RISKY - throws NullPointerException if input is null
if (input.equals("SUBMIT")) {
    processSubmission();
}

// ✓ SAFE - literal first prevents NPE
if ("SUBMIT".equals(input)) {
    processSubmission();
}

// Alternative safe approaches:
if (input != null && input.equals("SUBMIT")) {
    processSubmission();
}

// Java 7+: Objects.equals()
if (Objects.equals(input, "SUBMIT")) {
    processSubmission();
}
```

**Explanation**: When the literal is on the left side of `equals()`, even if `input` is null, the method returns `false` instead of throwing an exception.

### 4. Use Text Blocks for Multi-line Content (Java 15+)

```java title="Java"
// ✗ BAD - hard to read and maintain
String json = "{\n" +
              "  \"name\": \"John\",\n" +
              "  \"age\": 30,\n" +
              "  \"email\": \"john@example.com\"\n" +
              "}";

// ✓ GOOD - clean and readable
String json = """
    {
      "name": "John",
      "age": 30,
      "email": "john@example.com"
    }
    """;
```

### 5. Externalize Large or Changeable Strings

```java title="Java"
// ✗ BAD - hardcoded messages in code
public void sendEmail(String recipient) {
    String message = "Dear Customer, Thank you for your purchase...";
    emailService.send(recipient, message);
}

// ✓ GOOD - externalize to properties file
// messages.properties:
// email.purchase.message=Dear Customer, Thank you for your purchase...

public void sendEmail(String recipient) {
    String message = messageSource.getMessage("email.purchase.message");
    emailService.send(recipient, message);
}
```

---

## Common Patterns and Idioms

### Pattern 1: String Pool Reuse

```java title="Java"
String s1 = "Java";
String s2 = "Java";
String s3 = "Ja" + "va";  // Compile-time concatenation

// All reference the same pooled object
System.out.println(s1 == s2);  // true
System.out.println(s1 == s3);  // true
```

### Pattern 2: Constant Definition

```java title="Java"
public class Constants {
    // HTTP Status Codes
    public static final String HTTP_OK = "200";
    public static final String HTTP_NOT_FOUND = "404";
    public static final String HTTP_SERVER_ERROR = "500";
    
    // User Roles
    public static final String ROLE_ADMIN = "ADMIN";
    public static final String ROLE_USER = "USER";
    public static final String ROLE_GUEST = "GUEST";
    
    // Date Formats
    public static final String DATE_FORMAT_ISO = "yyyy-MM-dd";
    public static final String DATE_FORMAT_US = "MM/dd/yyyy";
}
```

### Pattern 3: Breaking Long Strings

```java title="Java"
// Multi-line string for readability
String errorMessage = "An unexpected error occurred while processing your request. " +
                      "Please try again later or contact support if the problem persists. " +
                      "Error code: 500";

// Or with text block (Java 15+)
String errorMessage = """
    An unexpected error occurred while processing your request.
    Please try again later or contact support if the problem persists.
    Error code: 500
    """;
```

---