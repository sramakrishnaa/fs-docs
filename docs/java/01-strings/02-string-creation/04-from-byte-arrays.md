---
title: "From Byte Arrays"
description: "Learn how to convert byte arrays to strings in Java."
---

# Byte Array to String

Converting byte arrays to strings is essential for I/O operations, network communication, and working with binary data. Character encoding is critical in these conversions.

--- 

## Why Character Encoding Matters

Bytes represent raw binary data. To convert them to text, you must specify how bytes map to characters. Different encodings use different rules:

```java title="Java"
byte[] bytes = {-61, -87};  // Two bytes

// Different encodings, different results:
String utf8 = new String(bytes, StandardCharsets.UTF_8);
System.out.println(utf8);  // "é" (single character in UTF-8)

String iso = new String(bytes, StandardCharsets.ISO_8859_1);
System.out.println(iso);  // "Ã©" (two characters in ISO-8859-1)

// Always specify encoding explicitly!
```

---

## Method 1: Basic Byte Array Constructor (Default Charset)

⚠️ **Not Recommended**: Uses platform default charset (system-dependent)

```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111};  // "Hello" in ASCII/UTF-8
String str = new String(bytes);
System.out.println(str);  // "Hello"

// Problem: Default charset varies by system
// Windows might use Windows-1252
// Linux might use UTF-8
// This can cause bugs when moving code between systems!
```

---

## Method 2: Byte Array with Charset (Recommended)

✅ **Recommended Approach**: Always specify charset explicitly

```java title="Java"
import java.nio.charset.StandardCharsets;

byte[] bytes = {72, 101, 108, 108, 111};

// UTF-8 encoding (most common)
String utf8 = new String(bytes, StandardCharsets.UTF_8);
System.out.println(utf8);  // "Hello"

// US-ASCII encoding
String ascii = new String(bytes, StandardCharsets.US_ASCII);
System.out.println(ascii);  // "Hello"

// ISO-8859-1 (Latin-1) encoding
String latin1 = new String(bytes, StandardCharsets.ISO_8859_1);
System.out.println(latin1);  // "Hello"

// UTF-16 encoding
String utf16 = new String(bytes, StandardCharsets.UTF_16);
System.out.println(utf16);  // Different result (UTF-16 uses 2 bytes per char)
```

**Available StandardCharsets**:

- `StandardCharsets.UTF_8` - UTF-8 (recommended for most cases)
- `StandardCharsets.UTF_16` - UTF-16
- `StandardCharsets.UTF_16BE` - UTF-16 Big Endian
- `StandardCharsets.UTF_16LE` - UTF-16 Little Endian
- `StandardCharsets.US_ASCII` - US ASCII (7-bit)
- `StandardCharsets.ISO_8859_1` - ISO Latin-1 (8-bit)

**Using Charset Name (Older Approach)**

```java title="Java"
import java.io.UnsupportedEncodingException;

byte[] bytes = {72, 101, 108, 108, 111};

try {
    // Requires exception handling
    String str = new String(bytes, "UTF-8");
    System.out.println(str);  // "Hello"
} catch (UnsupportedEncodingException e) {
    e.printStackTrace();
}
```

**Why StandardCharsets is better**:

- No exception handling needed
- Compile-time safety (typos caught by compiler)
- Better performance (no charset lookup)
- Clearer intent

---

## Method 3: Partial Byte Array Conversion

**With Charset**
```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111, 87, 111, 114, 108, 100};
//              H   e   l   l   o   W   o   r   l   d

// Constructor: String(byte[] bytes, int offset, int length, Charset charset)

String str1 = new String(bytes, 0, 5, StandardCharsets.UTF_8);
System.out.println(str1);  // "Hello"

String str2 = new String(bytes, 5, 5, StandardCharsets.UTF_8);
System.out.println(str2);  // "World"

String str3 = new String(bytes, 2, 3, StandardCharsets.UTF_8);
System.out.println(str3);  // "llo"
```

**Without Charset (Not Recommended)**

```java title="Java"
byte[] bytes = {72, 101, 108, 108, 111, 87, 111, 114, 108, 100};

// Uses default charset (platform-dependent)
String str = new String(bytes, 0, 5);
System.out.println(str);  // "Hello" (might differ on other systems)
```

---

## Understanding Character Encodings

**ASCII Encoding (7-bit)**

```java title="Java"
// ASCII values for "Hello"
byte[] ascii = {72, 101, 108, 108, 111};
String str = new String(ascii, StandardCharsets.US_ASCII);
System.out.println(str);  // "Hello"

// ASCII only supports 0-127
byte[] extended = {72, -61, -87};  // H, é in UTF-8
String asciiStr = new String(extended, StandardCharsets.US_ASCII);
System.out.println(asciiStr);  // "H??" (non-ASCII bytes become ?)
```

**UTF-8 Encoding (Variable-length)**

UTF-8 uses 1-4 bytes per character:

- ASCII characters (U+0000 to U+007F): 1 byte
- Latin, Greek, Cyrillic, etc. (U+0080 to U+07FF): 2 bytes
- Most other characters including CJK: 3 bytes
- Rare characters, emoji: 4 bytes

```java title="Java"
// Single-byte characters (ASCII)
byte[] ascii = {72, 101, 108, 108, 111};  // "Hello"
String s1 = new String(ascii, StandardCharsets.UTF_8);
System.out.println(s1);  // "Hello" (5 bytes → 5 characters)

// Multi-byte characters
byte[] utf8 = {72, 101, 108, 108, 111, 32,  // "Hello "
               -28, -72, -106, -25, -107, -116};  // "世界" (2 Chinese chars, 6 bytes)
String s2 = new String(utf8, StandardCharsets.UTF_8);
System.out.println(s2);  // "Hello 世界" (12 bytes → 8 characters)

// Emoji (4 bytes per emoji in UTF-8)
byte[] emoji = {-16, -97, -104, -128};  // 😀 (4 bytes)
String s3 = new String(emoji, StandardCharsets.UTF_8);
System.out.println(s3);  // "😀" (4 bytes → 1 character)
```

**UTF-16 Encoding (Fixed 2 or 4 bytes)**

```java title="Java"
// UTF-16 uses 2 bytes for most characters
byte[] utf16 = {0, 72, 0, 105};  // "Hi" in UTF-16 BE
String s1 = new String(utf16, StandardCharsets.UTF_16BE);
System.out.println(s1);  // "Hi"

// UTF-16 with BOM (Byte Order Mark)
byte[] utf16BOM = {-2, -1, 0, 72, 0, 105};  // BOM + "Hi"
String s2 = new String(utf16BOM, StandardCharsets.UTF_16);
System.out.println(s2);  // "Hi" (BOM is recognized and skipped)
```

---

## Practical Use Cases

### Use Case 1: Reading File Content

```java title="Java"
import java.nio.file.*;
import java.nio.charset.StandardCharsets;

public class FileReader {
    public String readFile(String filePath) throws IOException {
        // Read all bytes from file
        byte[] bytes = Files.readAllBytes(Paths.get(filePath));
        
        // Convert to String with UTF-8 encoding
        return new String(bytes, StandardCharsets.UTF_8);
    }
    
    public String readFilePartial(String filePath, int maxBytes) throws IOException {
        byte[] bytes = Files.readAllBytes(Paths.get(filePath));
        
        // Read only first maxBytes
        int length = Math.min(maxBytes, bytes.length);
        return new String(bytes, 0, length, StandardCharsets.UTF_8);
    }
}
```

### Use Case 2: Network Communication

```java title="Java"
import java.net.*;
import java.io.*;
import java.nio.charset.StandardCharsets;

public class NetworkReceiver {
    public String receiveMessage(Socket socket) throws IOException {
        InputStream in = socket.getInputStream();
        
        // Read bytes from network
        byte[] buffer = new byte[1024];
        int bytesRead = in.read(buffer);
        
        // Convert to String
        return new String(buffer, 0, bytesRead, StandardCharsets.UTF_8);
    }
}
```

### Use Case 3: Decoding Base64

```java title="Java"
import java.util.Base64;
import java.nio.charset.StandardCharsets;

public class Base64Decoder {
    public String decodeBase64(String encoded) {
        // Decode Base64 to bytes
        byte[] decodedBytes = Base64.getDecoder().decode(encoded);
        
        // Convert bytes to String
        return new String(decodedBytes, StandardCharsets.UTF_8);
    }
}

// Usage
Base64Decoder decoder = new Base64Decoder();
String encoded = "SGVsbG8gV29ybGQ=";  // "Hello World" in Base64
String decoded = decoder.decodeBase64(encoded);
System.out.println(decoded);  // "Hello World"
```

### Use Case 4: HTTP Response Parsing

```java title="Java"
import java.nio.charset.StandardCharsets;

public class HttpResponseParser {
    public String parseResponse(byte[] responseBytes, String charset) {
        // Parse charset from Content-Type header
        Charset charsetObj = switch (charset.toLowerCase()) {
            case "utf-8" -> StandardCharsets.UTF_8;
            case "iso-8859-1" -> StandardCharsets.ISO_8859_1;
            default -> StandardCharsets.UTF_8;  // Default fallback
        };
        
        return new String(responseBytes, charsetObj);
    }
    
    public String extractBody(byte[] response) {
        // Find header-body separator (\r\n\r\n)
        int headerEnd = findHeaderEnd(response);
        
        if (headerEnd != -1) {
            int bodyStart = headerEnd + 4;  // Skip \r\n\r\n
            int bodyLength = response.length - bodyStart;
            return new String(response, bodyStart, bodyLength, StandardCharsets.UTF_8);
        }
        
        return "";
    }
    
    private int findHeaderEnd(byte[] response) {
        // Implementation to find \r\n\r\n
        for (int i = 0; i < response.length - 3; i++) {
            if (response[i] == '\r' && response[i+1] == '\n' &&
                response[i+2] == '\r' && response[i+3] == '\n') {
                return i;
            }
        }
        return -1;
    }
}
```

---

## Encoding Mismatch Issues

**Problem: Wrong Encoding**

```java title="Java"
// Original text: "Café"
byte[] utf8Bytes = "Café".getBytes(StandardCharsets.UTF_8);
// utf8Bytes = [67, 97, 102, -61, -87]  (5 bytes)

// ✗ WRONG: Decode with different encoding
String wrong = new String(utf8Bytes, StandardCharsets.ISO_8859_1);
System.out.println(wrong);  // "CafÃ©" (corrupted)

// ✓ CORRECT: Use same encoding for decode as encode
String correct = new String(utf8Bytes, StandardCharsets.UTF_8);
System.out.println(correct);  // "Café" (correct)
```

**Rule**: Always use the same encoding for decoding that was used for encoding!

**Problem: Platform-Dependent Code**

```java title="Java"
// ✗ BAD: Platform-dependent
String text = "Hello";
byte[] bytes = text.getBytes();  // Uses default charset
// Sends bytes over network...
// On receiving side:
String received = new String(bytes);  // Uses default charset

// Problem: If sender and receiver have different default charsets,
// the received string will be corrupted!

// ✓ GOOD: Explicitly specify encoding
String text = "Hello";
byte[] bytes = text.getBytes(StandardCharsets.UTF_8);
// Sends bytes over network...
// On receiving side:
String received = new String(bytes, StandardCharsets.UTF_8);
// Works correctly regardless of platform!
```

---

## Handling Invalid Bytes

```java title="Java"
// Invalid UTF-8 sequence
byte[] invalidUtf8 = {72, 101, 108, 108, 111, (byte)0xFF, (byte)0xFE};

// Java replaces invalid bytes with replacement character (�)
String str = new String(invalidUtf8, StandardCharsets.UTF_8);
System.out.println(str);  // "Hello��"

// Check for replacement character
if (str.contains("\uFFFD")) {
    System.out.println("String contains invalid characters!");
}
```

---

## Performance Considerations

```java title="Java"
// Scenario 1: Large byte arrays
byte[] largeData = new byte[1_000_000];

// Efficient: Direct conversion
String str = new String(largeData, StandardCharsets.UTF_8);

// Inefficient: Don't do unnecessary intermediate steps
// String str = new String(largeData, "UTF-8");  // Requires exception handling

// Scenario 2: Repeated conversions
List<byte[]> byteArrays = getMultipleByteArrays();
StringBuilder sb = new StringBuilder();

for (byte[] bytes : byteArrays) {
    sb.append(new String(bytes, StandardCharsets.UTF_8));
}
String result = sb.toString();
```

---

## Best Practices

✅ **DO:**

```java title="Java"
// 1. Always specify charset explicitly
byte[] bytes = getData();
String str = new String(bytes, StandardCharsets.UTF_8);

// 2. Use StandardCharsets (no exception handling needed)
String str = new String(bytes, StandardCharsets.UTF_8);

// 3. Match encode and decode charsets
byte[] encoded = text.getBytes(StandardCharsets.UTF_8);
String decoded = new String(encoded, StandardCharsets.UTF_8);

// 4. Validate byte array before conversion
if (bytes != null && bytes.length > 0) {
    String str = new String(bytes, StandardCharsets.UTF_8);
}

// 5. Use partial conversion when needed
String header = new String(bytes, 0, headerLength, StandardCharsets.UTF_8);
```

❌ **DON'T:**

```java title="Java"
// 1. Don't rely on default charset
byte[] bytes = getData();
String str = new String(bytes);  // Platform-dependent!

// 2. Don't use charset name strings
try {
    String str = new String(bytes, "UTF-8");  // Requires exception handling
} catch (UnsupportedEncodingException e) { }

// 3. Don't mismatch encodings
byte[] utf8 = text.getBytes(StandardCharsets.UTF_8);
String str = new String(utf8, StandardCharsets.ISO_8859_1);  // Corrupted!

// 4. Don't forget null checks
byte[] bytes = null;
// String str = new String(bytes, StandardCharsets.UTF_8);  // NullPointerException!
```

---
