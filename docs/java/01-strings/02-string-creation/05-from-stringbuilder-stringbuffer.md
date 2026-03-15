---
title: "From StringBuilder/StringBuffer"
description: "Learn how to convert mutable string builders to immutable strings in Java."
---

# StringBuilder/StringBuffer to String

Converting mutable string builders to immutable strings is the final step after dynamically building string content.

## Method 1: Using `toString()` Method (Recommended)

### **From StringBuilder**

```java title="Java"
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" ");
sb.append("World");

// Convert to String
String str = sb.toString();
System.out.println(str);  // "Hello World"
```

### **From StringBuffer**

```java title="Java"
StringBuffer sbf = new StringBuffer();
sbf.append("Java");
sbf.append(" ");
sbf.append("Programming");

// Convert to String
String str = sbf.toString();
System.out.println(str);  // "Java Programming"
```

---

### Method 2: Using String Constructor (Not Recommended)

```java title="Java"
StringBuilder sb = new StringBuilder("Hello World");

// Using constructor
String str1 = new String(sb);

// Using toString() - preferred
String str2 = sb.toString();

// Both produce same result
System.out.println(str1.equals(str2));  // true
```

**Why `toString()` is preferred**:

- More idiomatic and conventional
- Clearer intent
- Direct method on the object
- No unnecessary object creation

---

## StringBuilder vs StringBuffer

### **StringBuilder (Faster, Non-Thread-Safe)**

```java title="Java"
// Single-threaded scenario
public String buildMessage(List<String> parts) {
    StringBuilder sb = new StringBuilder();
    
    for (String part : parts) {
        sb.append(part).append(" ");
    }
    
    return sb.toString().trim();
}
```

### **StringBuffer (Slower, Thread-Safe)**

```java title="Java"
// Multi-threaded scenario
public class SharedStringBuilder {
    private StringBuffer buffer = new StringBuffer();
    
    // Thread-safe append
    public synchronized void appendMessage(String message) {
        buffer.append(message).append("\n");
    }
    
    // Thread-safe retrieval
    public synchronized String getAllMessages() {
        return buffer.toString();
    }
}
```

**Performance Difference**:

- **StringBuilder**: Faster (no synchronization overhead)
- **StringBuffer**: ~10-30% slower (synchronized methods)

**Decision**:

- Use **StringBuilder** for single-threaded operations (99% of cases)
- Use **StringBuffer** only when multiple threads modify the same instance

---

## Practical Examples

### **Example 1: Building CSV String**

```java title="Java"
public String buildCSV(List<String[]> rows) {
    StringBuilder sb = new StringBuilder();
    
    for (String[] row : rows) {
        sb.append(String.join(",", row));
        sb.append("\n");
    }
    
    return sb.toString();
}

// Usage
List<String[]> data = List.of(
    new String[]{"Name", "Age", "City"},
    new String[]{"John", "30", "NY"},
    new String[]{"Jane", "25", "LA"}
);

String csv = buildCSV(data);
System.out.println(csv);
// Output:
// Name,Age,City
// John,30,NY
// Jane,25,LA
```

### **Example 2: Building JSON String**

```java title="Java"
public String buildJSON(Map<String, String> data) {
    StringBuilder json = new StringBuilder();
    json.append("{\n");
    
    int count = 0;
    for (Map.Entry<String, String> entry : data.entrySet()) {
        json.append("  \"").append(entry.getKey()).append("\": ");
        json.append("\"").append(entry.getValue()).append("\"");
        
        if (++count < data.size()) {
            json.append(",");
        }
        json.append("\n");
    }
    
    json.append("}");
    return json.toString();
}

// Usage
Map<String, String> user = Map.of(
    "name", "John",
    "age", "30",
    "city", "New York"
);

String json = buildJSON(user);
System.out.println(json);
// Output:
// {
//   "name": "John",
//   "age": "30",
//   "city": "New York"
// }
```

### **Example 3: Building HTML**

```java title="Java"
public String buildTable(List<Map<String, String>> data) {
    StringBuilder html = new StringBuilder();
    
    html.append("<table>\n");
    
    // Header
    if (!data.isEmpty()) {
        html.append("  <tr>\n");
        for (String key : data.get(0).keySet()) {
            html.append("    <th>").append(key).append("</th>\n");
        }
        html.append("  </tr>\n");
    }
    
    // Rows
    for (Map<String, String> row : data) {
        html.append("  <tr>\n");
        for (String value : row.values()) {
            html.append("    <td>").append(value).append("</td>\n");
        }
        html.append("  </tr>\n");
    }
    
    html.append("</table>");
    return html.toString();
}
```

### **Example 4: String Concatenation in Loops**

```java title="Java"
// ✗ BAD: Creates many intermediate String objects
public String inefficientConcat(List<String> items) {
    String result = "";
    for (String item : items) {
        result += item + ", ";  // Creates new String each iteration
    }
    return result;
}

// ✓ GOOD: Uses StringBuilder efficiently
public String efficientConcat(List<String> items) {
    StringBuilder sb = new StringBuilder();
    for (String item : items) {
        sb.append(item).append(", ");
    }
    
    // Remove trailing comma and space
    if (sb.length() > 2) {
        sb.setLength(sb.length() - 2);
    }
    
    return sb.toString();
}

// Benchmark (10,000 items):
// inefficientConcat: ~2000ms
// efficientConcat: ~2ms (1000x faster!)
```

---

## Best Practices

 ✅ **DO:**

```java title="Java"
// 1. Use toString() method
StringBuilder sb = new StringBuilder("Hello");
String str = sb.toString();

// 2. Choose StringBuilder for single-threaded operations
StringBuilder sb = new StringBuilder();
// Build string...
String result = sb.toString();

// 3. Reuse StringBuilder when building multiple strings
StringBuilder sb = new StringBuilder(100);  // Initial capacity
for (Item item : items) {
    sb.setLength(0);  // Clear builder
    sb.append(item.getName()).append(": ").append(item.getValue());
    processString(sb.toString());
}

// 4. Set initial capacity for known size
int estimatedSize = items.size() * 50;
StringBuilder sb = new StringBuilder(estimatedSize);
```

 ❌ **DON'T:**

```java
// 1. Don't use String constructor
StringBuilder sb = new StringBuilder("Hello");
String str = new String(sb);  // Unnecessary

// 2. Don't use StringBuffer unless needed for thread safety
StringBuffer sbf = new StringBuffer();  // Slower than StringBuilder

// 3. Don't build strings with + in loops
String result = "";
for (String s : list) {
    result += s;  // Creates new String each time
}

// 4. Don't forget to use toString()
StringBuilder sb = new StringBuilder("Hello");
System.out.println(sb);  // Prints StringBuilder@hashcode, not content
```

---
