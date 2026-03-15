---
title: Constructor Overloads
description: Learn about the different ways to create String objects in Java.
---

# Constructor Overloads

The String class provides multiple constructors for different scenarios. Understanding when to use each is important for writing efficient code.

---

## Complete Constructor Reference

### 1. Empty String Constructor

```java title="Java"
String str = new String();

System.out.println(str);           // "" (empty string)
System.out.println(str.length());  // 0
System.out.println(str.isEmpty()); // true
```

**Use Case**: Rarely used; prefer `""` literal.

```java
// ✗ Unnecessary
String empty = new String();

// ✓ Preferred
String empty = "";
```

---

### 2. Copy Constructor

```java title="Java"
String original = "Hello";
String copy = new String(original);

System.out.println(copy);  // "Hello"
System.out.println(original == copy);  // false (different objects)
System.out.println(original.equals(copy));  // true (same content)
```

**Use Case**: Creating a new object when necessary (rare due to immutability).

**Note**: Usually unnecessary:
```java title="Java"
String original = "Hello";
String reference = original;  // Sufficient in most cases
```

---

### 3. Character Array Constructors

**Full Array**
```java title="Java"
public String(char[] value)
```

```java title="Java"
char[] chars = {'J', 'a', 'v', 'a'};
String str = new String(chars);
System.out.println(str);  // "Java"
```

**Partial Array**
```java title="Java"
public String(char[] value, int offset, int count)
```

```java title="Java"
char[] chars = {'H', 'e', 'l', 'l', 'o', 'W', 'o', 'r', 'l', 'd'};

String str1 = new String(chars, 0, 5);   // "Hello"
String str2 = new String(chars, 5, 5);   // "World"
String str3 = new String(chars, 2, 3);   // "llo"
```

---

### 4. Byte Array Constructors (Default Charset)

⚠️ **Not Recommended**: Uses platform default charset

**Full Array**
```java title="Java"
public String(byte[] bytes)
```

```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111};  // "Hello" in ASCII
String str = new String(bytes);
System.out.println(str);  // "Hello"
```

**Partial Array**
```java title="Java"
public String(byte[] bytes, int offset, int length)
```

```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111, 87, 111, 114, 108, 100};

String str1 = new String(bytes, 0, 5);   // "Hello"
String str2 = new String(bytes, 5, 5);   // "World"
```

---

### **5. Byte Array with Charset (Recommended)**

✅ **Best Practice**: Always specify charset

**Using Charset Object**
```java title="Java"
public String(byte[] bytes, Charset charset)
public String(byte[] bytes, int offset, int length, Charset charset)
```

```java title="Java"
import java.nio.charset.StandardCharsets;

byte[] bytes = {72, 101, 108, 108, 111};

// Full array
String str1 = new String(bytes, StandardCharsets.UTF_8);

// Partial array
String str2 = new String(bytes, 0, 3, StandardCharsets.UTF_8);
System.out.println(str2);  // "Hel"
```

**Using Charset Name**
```java title="Java"
public String(byte[] bytes, String charsetName) throws UnsupportedEncodingException
public String(byte[] bytes, int offset, int length, String charsetName) throws UnsupportedEncodingException
```

```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111};

try {
    String str = new String(bytes, "UTF-8");
    System.out.println(str);  // "Hello"
} catch (UnsupportedEncodingException e) {
    e.printStackTrace();
}
```

**Recommendation**: Use `Charset` object to avoid exception handling.

---

### **6. StringBuilder Constructor**

```java title="Java"
public String(StringBuilder builder)
```

```java title="Java"
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");

String str = new String(sb);
System.out.println(str);  // "Hello World"
```

**Alternative** (preferred):
```java title="Java"
String str = sb.toString();
```

---

### **7. StringBuffer Constructor**

```java title="Java"
public String(StringBuffer buffer)
```

```java title="Java"
StringBuffer sbf = new StringBuffer("Java");
sbf.append(" Programming");

String str = new String(sbf);
System.out.println(str);  // "Java Programming"
```

**Alternative** (preferred):
```java title="Java"
String str = sbf.toString();
```

---

### **8. Code Point Array Constructor (Unicode)**

```java title="Java"
public String(int[] codePoints, int offset, int count)
```

Creates a string from Unicode code points.

```java title="Java"  
// Standard ASCII characters
int[] codePoints = {72, 101, 108, 108, 111};  // "Hello"
String str = new String(codePoints, 0, codePoints.length);
System.out.println(str);  // "Hello"

// Unicode characters
int[] unicode = {0x4E16, 0x754C};  // 世界 (Chinese characters)
String chinese = new String(unicode, 0, unicode.length);
System.out.println(chinese);  // "世界"

// Emoji
int[] emoji = {0x1F600, 0x1F601, 0x1F602};  // 😀😁😂
String emojiStr = new String(emoji, 0, emoji.length);
System.out.println(emojiStr);  // "😀😁😂"
```

**Use Case**: Working with Unicode supplementary characters or code points.

---

## Constructor Comparison Table

| Constructor | Use Case | Throws Exception | Recommendation |
|------------|----------|------------------|----------------|
| `String()` | Empty string | No | Use `""` instead |
| `String(String)` | Copy string | No | Rarely needed |
| `String(char[])` | Char array conversion | No | ✅ Use when needed |
| `String(char[], int, int)` | Partial char array | No | ✅ Use for portions |
| `String(byte[])` | Default charset | No | ❌ Avoid (platform-dependent) |
| `String(byte[], Charset)` | Specified charset | No | ✅ **Recommended** |
| `String(byte[], String)` | Charset by name | Yes | ❌ Use Charset object instead |
| `String(StringBuilder)` | From builder | No | Use `toString()` instead |
| `String(StringBuffer)` | From buffer | No | Use `toString()` instead |
| `String(int[], int, int)` | Unicode code points | No | ✅ For Unicode work |

---

## Deprecated Constructors

**Do Not Use**: These constructors are deprecated

```java title="Java"
// DEPRECATED - Do not use
@Deprecated(since="1.1")
public String(byte[] ascii, int hibyte, int offset, int count)

@Deprecated(since="1.1")
public String(byte[] ascii, int hibyte)
```

**Reason**: These don't properly convert bytes to characters according to platform's default character encoding. Use charset-aware constructors instead.

---

## Advanced Example: Multi-Source String Building

```java
public class MultiSourceStringBuilder {
    public static void main(String[] args) {
        // 1. Start with literal
        String literal = "Greeting: ";
        
        // 2. Add from char array
        char[] nameChars = {'J', 'o', 'h', 'n'};
        String name = new String(nameChars);
        
        // 3. Add from byte array
        byte[] punctuation = {33, 33};  // "!!"
        String exclamation = new String(punctuation, StandardCharsets.UTF_8);
        
        // 4. Add from StringBuilder
        StringBuilder details = new StringBuilder();
        details.append(" Age: ").append(30).append(", City: NY");
        
        // 5. Combine all
        StringBuilder result = new StringBuilder();
        result.append(literal)
              .append(name)
              .append(details.toString())
              .append(exclamation);
        
        String finalString = result.toString();
        System.out.println(finalString);
        // Output: "Greeting: John Age: 30, City: NY!!"
    }
}
```

---


