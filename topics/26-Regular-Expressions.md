# Regular Expressions in Java

## Table of Contents
1. [Introduction to Regex](#introduction-to-regex)
2. [Pattern and Matcher Classes](#pattern-and-matcher-classes)
3. [Basic Regex Syntax](#basic-regex-syntax)
4. [Character Classes](#character-classes)
5. [Quantifiers](#quantifiers)
6. [Anchors and Boundaries](#anchors-and-boundaries)
7. [Groups and Capturing](#groups-and-capturing)
8. [Lookahead and Lookbehind](#lookahead-and-lookbehind)
9. [Common Use Cases](#common-use-cases)
10. [Best Practices and Performance](#best-practices-and-performance)

---

## Introduction to Regex

**Regular Expressions** (regex) are sequences of characters that define search patterns, primarily used for pattern matching and text manipulation.

### Java Regex Classes
```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;
import java.util.regex.PatternSyntaxException;
```

### Basic Usage
```java
// Method 1: Using String methods
String text = "Hello123";
boolean matches = text.matches("Hello\\d+");  // true

// Method 2: Using Pattern and Matcher
Pattern pattern = Pattern.compile("Hello\\d+");
Matcher matcher = pattern.matcher(text);
boolean found = matcher.matches();  // true
```

---

## Pattern and Matcher Classes

### Pattern Class

The `Pattern` class represents a compiled regex pattern.

```java
// Compile a pattern
Pattern pattern = Pattern.compile("\\d+");

// Compile with flags
Pattern caseInsensitive = Pattern.compile("hello", Pattern.CASE_INSENSITIVE);
Pattern multiline = Pattern.compile("^start", Pattern.MULTILINE);

// Multiple flags
Pattern combined = Pattern.compile(
    "hello", 
    Pattern.CASE_INSENSITIVE | Pattern.MULTILINE
);
```

### Common Pattern Flags
```java
Pattern.CASE_INSENSITIVE  // (?i) - Case insensitive matching
Pattern.MULTILINE         // (?m) - ^ and $ match line boundaries
Pattern.DOTALL            // (?s) - Dot matches newline
Pattern.UNICODE_CASE      // (?u) - Unicode-aware case folding
Pattern.COMMENTS          // (?x) - Allow whitespace and comments
```

### Matcher Class

The `Matcher` class performs match operations on a character sequence.

```java
String text = "Java 17, Python 3.11, JavaScript ES2023";
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher(text);

// Find all matches
while (matcher.find()) {
    System.out.println("Found: " + matcher.group());
    System.out.println("Start: " + matcher.start());
    System.out.println("End: " + matcher.end());
}
// Output:
// Found: 17 (Start: 5, End: 7)
// Found: 3 (Start: 17, End: 18)
// Found: 11 (Start: 19, End: 21)
// Found: 2023 (Start: 36, End: 40)
```

### Matcher Methods

```java
String text = "The price is $99.99";
Pattern pattern = Pattern.compile("\\$(\\d+\\.\\d+)");
Matcher matcher = pattern.matcher(text);

// matches() - entire string must match
boolean fullMatch = matcher.matches();  // false

// find() - find next match
matcher.reset();
if (matcher.find()) {
    String match = matcher.group();      // "$99.99"
    String price = matcher.group(1);     // "99.99"
}

// lookingAt() - match at beginning
boolean startsWithPattern = matcher.lookingAt();  // false

// replaceAll() - replace all matches
String replaced = matcher.replaceAll("\\$XX.XX");
// "The price is $XX.XX"

// replaceFirst() - replace first match only
String replacedFirst = matcher.replaceFirst("\\$XX.XX");
```

---

## Basic Regex Syntax

### Literal Characters
```java
"hello".matches("hello")           // true - exact match
"Hello".matches("hello")           // false - case sensitive
"Hello".matches("(?i)hello")       // true - case insensitive
```

### Metacharacters

Special characters that need escaping with `\\`:
```java
. ^ $ * + ? { } [ ] \ | ( )
```

```java
"example.com".matches("example\\.com")     // true - escaped dot
"example.com".matches("example.com")       // false - dot matches any char
"a.c".matches("a.c")                       // true - 'b' matches dot
```

### Escape Sequences
```java
\\t   // Tab
\\n   // Newline
\\r   // Carriage return
\\f   // Form feed
\\    // Backslash
```

---

## Character Classes

### Predefined Character Classes

```java
\\d   // Digit: [0-9]
\\D   // Non-digit: [^0-9]
\\w   // Word character: [a-zA-Z0-9_]
\\W   // Non-word character: [^\\w]
\\s   // Whitespace: [ \\t\\n\\r\\f]
\\S   // Non-whitespace: [^\\s]
.     // Any character (except newline by default)
```

### Examples
```java
"123".matches("\\d+")              // true
"abc123".matches("\\w+")           // true
"hello world".matches("\\w+\\s\\w+")  // true
```

### Custom Character Classes

```java
[abc]         // a, b, or c
[^abc]        // Any character except a, b, or c
[a-z]         // Any lowercase letter
[A-Z]         // Any uppercase letter
[a-zA-Z]      // Any letter
[0-9]         // Any digit
[a-zA-Z0-9]   // Any alphanumeric character
```

### Examples
```java
"a".matches("[abc]")               // true
"d".matches("[abc]")               // false
"5".matches("[0-9]")               // true
"B".matches("[A-Za-z]")            // true
```

### Combining Character Classes
```java
[a-z&&[def]]     // Intersection: d, e, or f
[a-z&&[^bc]]     // Subtraction: a-z except b and c
[a-z&&[^m-p]]    // a-l and q-z
```

---

## Quantifiers

### Greedy Quantifiers
```java
*      // 0 or more times
+      // 1 or more times
?      // 0 or 1 time
{n}    // Exactly n times
{n,}   // At least n times
{n,m}  // Between n and m times
```

### Examples
```java
"aaa".matches("a*")                // true
"aaa".matches("a+")                // true
"".matches("a*")                   // true
"".matches("a+")                   // false
"a".matches("a?")                  // true
"".matches("a?")                   // true

"123".matches("\\d{3}")            // true - exactly 3 digits
"12".matches("\\d{3}")             // false
"1234".matches("\\d{2,4}")         // true - 2 to 4 digits
"12345".matches("\\d{2,}")         // true - at least 2 digits
```

### Reluctant (Lazy) Quantifiers
```java
*?     // 0 or more, lazy
+?     // 1 or more, lazy
??     // 0 or 1, lazy
{n,}?  // At least n, lazy
{n,m}? // Between n and m, lazy
```

### Greedy vs Lazy
```java
String text = "<div>First</div><div>Second</div>";
Pattern greedy = Pattern.compile("<div>.*</div>");
Pattern lazy = Pattern.compile("<div>.*?</div>");

Matcher greedyMatcher = greedy.matcher(text);
if (greedyMatcher.find()) {
    System.out.println(greedyMatcher.group());
    // Output: <div>First</div><div>Second</div>
}

Matcher lazyMatcher = lazy.matcher(text);
while (lazyMatcher.find()) {
    System.out.println(lazyMatcher.group());
    // Output (first): <div>First</div>
    // Output (second): <div>Second</div>
}
```

### Possessive Quantifiers
```java
*+     // 0 or more, possessive
++     // 1 or more, possessive
?+     // 0 or 1, possessive
{n,}+  // At least n, possessive
{n,m}+ // Between n and m, possessive
```

Possessive quantifiers never backtrack, which can improve performance but may prevent matches.

---

## Anchors and Boundaries

### Position Anchors
```java
^      // Start of line
$      // End of line
\\A    // Start of input
\\Z    // End of input (before final line terminator if present)
\\z    // Absolute end of input
```

### Examples
```java
"hello".matches("^hello$")         // true
"hello world".matches("^hello")    // false (doesn't match entire string)

Pattern pattern = Pattern.compile("^hello", Pattern.MULTILINE);
Matcher matcher = pattern.matcher("world\\nhello");
matcher.find();                     // true - finds hello at line start
```

### Word Boundaries
```java
\\b    // Word boundary
\\B    // Non-word boundary
```

### Examples
```java
String text = "The cat scattered his food";
Pattern pattern = Pattern.compile("\\bcat\\b");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println("Found at: " + matcher.start());
}
// Output: Found at: 4 (finds "cat", not the "cat" in "scattered")
```

---

## Groups and Capturing

### Capturing Groups
```java
(pattern)      // Numbered capturing group
(?<name>pattern)  // Named capturing group
(?:pattern)    // Non-capturing group
```

### Numbered Groups
```java
String text = "John Doe, 30 years old";
Pattern pattern = Pattern.compile("(\\w+)\\s(\\w+),\\s(\\d+)");
Matcher matcher = pattern.matcher(text);

if (matcher.find()) {
    System.out.println("Full match: " + matcher.group(0));
    System.out.println("First name: " + matcher.group(1));
    System.out.println("Last name: " + matcher.group(2));
    System.out.println("Age: " + matcher.group(3));
}
// Output:
// Full match: John Doe, 30
// First name: John
// Last name: Doe
// Age: 30
```

### Named Groups
```java
String text = "2024-02-11";
Pattern pattern = Pattern.compile(
    "(?<year>\\d{4})-(?<month>\\d{2})-(?<day>\\d{2})"
);
Matcher matcher = pattern.matcher(text);

if (matcher.find()) {
    System.out.println("Year: " + matcher.group("year"));
    System.out.println("Month: " + matcher.group("month"));
    System.out.println("Day: " + matcher.group("day"));
}
// Output:
// Year: 2024
// Month: 02
// Day: 11
```

### Non-Capturing Groups
```java
// Use (?:) when you need grouping but don't need to capture
String text = "color: red";
Pattern withCapture = Pattern.compile("(color|colour): (\\w+)");
Pattern withoutCapture = Pattern.compile("(?:color|colour): (\\w+)");

Matcher matcher = withoutCapture.matcher(text);
if (matcher.find()) {
    System.out.println(matcher.group(1));  // "red"
    // group(1) is the first (and only) capturing group
}
```

### Backreferences
```java
\\1, \\2, etc.  // Reference to numbered group
\\k<name>       // Reference to named group
```

```java
// Find repeated words
String text = "the the cat sat sat on the mat";
Pattern pattern = Pattern.compile("\\b(\\w+)\\s+\\1\\b");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println("Repeated: " + matcher.group(1));
}
// Output:
// Repeated: the
// Repeated: sat

// Check for balanced quotes
"'hello'".matches("(['\"]).*\\1")      // true
"'hello\"".matches("(['\"]).*\\1")     // false
```

---

## Lookahead and Lookbehind

### Positive Lookahead
```java
(?=pattern)    // Positive lookahead
```

```java
// Match "Java" only if followed by "Script"
String text = "Java JavaScript Java";
Pattern pattern = Pattern.compile("Java(?=Script)");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println("Found at: " + matcher.start());
}
// Output: Found at: 5 (only "Java" in "JavaScript")
```

### Negative Lookahead
```java
(?!pattern)    // Negative lookahead
```

```java
// Match "Java" NOT followed by "Script"
String text = "Java JavaScript Java";
Pattern pattern = Pattern.compile("Java(?!Script)");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println("Found at: " + matcher.start());
}
// Output: 
// Found at: 0 (first standalone "Java")
// Found at: 16 (last standalone "Java")
```

### Positive Lookbehind
```java
(?<=pattern)   // Positive lookbehind
```

```java
// Match digits preceded by "$"
String text = "$100 and €200";
Pattern pattern = Pattern.compile("(?<=\\$)\\d+");
Matcher matcher = pattern.matcher(text);

if (matcher.find()) {
    System.out.println("Price: " + matcher.group());
}
// Output: Price: 100
```

### Negative Lookbehind
```java
(?<!pattern)   // Negative lookbehind
```

```java
// Match digits NOT preceded by "$"
String text = "$100 and 200 items";
Pattern pattern = Pattern.compile("(?<!\\$)\\d+");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println("Number: " + matcher.group());
}
// Output: Number: 200
// (Note: also matches "00" from "$100", use word boundaries if needed)
```

### Complex Example
```java
// Password validation: 
// - At least 8 characters
// - Contains at least one digit
// - Contains at least one uppercase letter
// - Contains at least one lowercase letter
String password = "SecurePass123";
boolean isValid = password.matches(
    "^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z]).{8,}$"
);
System.out.println("Valid: " + isValid);  // true
```

---

## Common Use Cases

### Email Validation
```java
public class EmailValidator {
    private static final Pattern EMAIL_PATTERN = Pattern.compile(
        "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$"
    );
    
    public static boolean isValidEmail(String email) {
        if (email == null) return false;
        return EMAIL_PATTERN.matcher(email).matches();
    }
}

// Usage
EmailValidator.isValidEmail("user@example.com");     // true
EmailValidator.isValidEmail("invalid.email");        // false
```

### Phone Number Validation
```java
public class PhoneValidator {
    // US phone format: (123) 456-7890 or 123-456-7890
    private static final Pattern PHONE_PATTERN = Pattern.compile(
        "^(\\(\\d{3}\\)|\\d{3})[- ]?\\d{3}[- ]?\\d{4}$"
    );
    
    public static boolean isValidPhone(String phone) {
        return phone != null && PHONE_PATTERN.matcher(phone).matches();
    }
}

// Usage
PhoneValidator.isValidPhone("(123) 456-7890");  // true
PhoneValidator.isValidPhone("123-456-7890");    // true
PhoneValidator.isValidPhone("1234567890");      // false
```

### URL Extraction
```java
public static List<String> extractUrls(String text) {
    Pattern pattern = Pattern.compile(
        "https?://[\\w-]+(\\.[\\w-]+)+([\\w.,@?^=%&:/~+#-]*[\\w@?^=%&/~+#-])?"
    );
    Matcher matcher = pattern.matcher(text);
    List<String> urls = new ArrayList<>();
    
    while (matcher.find()) {
        urls.add(matcher.group());
    }
    return urls;
}

// Usage
String text = "Visit https://example.com and http://test.org for more info";
List<String> urls = extractUrls(text);
// ["https://example.com", "http://test.org"]
```

### Date Format Validation
```java
public class DateValidator {
    // Format: YYYY-MM-DD
    private static final Pattern DATE_PATTERN = Pattern.compile(
        "^\\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\\d|3[01])$"
    );
    
    public static boolean isValidDate(String date) {
        return date != null && DATE_PATTERN.matcher(date).matches();
    }
}

// Usage
DateValidator.isValidDate("2024-02-11");  // true
DateValidator.isValidDate("2024-13-01");  // false (invalid month)
DateValidator.isValidDate("2024-02-32");  // false (invalid day)
```

### IP Address Validation
```java
public class IPValidator {
    private static final Pattern IP_PATTERN = Pattern.compile(
        "^((25[0-5]|2[0-4]\\d|1\\d{2}|[1-9]?\\d)\\.){3}" +
        "(25[0-5]|2[0-4]\\d|1\\d{2}|[1-9]?\\d)$"
    );
    
    public static boolean isValidIP(String ip) {
        return ip != null && IP_PATTERN.matcher(ip).matches();
    }
}

// Usage
IPValidator.isValidIP("192.168.1.1");    // true
IPValidator.isValidIP("256.1.1.1");      // false
IPValidator.isValidIP("192.168.1");      // false
```

### Text Sanitization
```java
public static String removeHtmlTags(String html) {
    return html.replaceAll("<[^>]*>", "");
}

public static String removeExtraWhitespace(String text) {
    return text.replaceAll("\\s+", " ").trim();
}

public static String extractDigits(String text) {
    return text.replaceAll("\\D", "");
}

// Usage
removeHtmlTags("<p>Hello <b>World</b></p>");  // "Hello World"
removeExtraWhitespace("Hello    World  ");    // "Hello World"
extractDigits("Price: $123.45");              // "12345"
```

### String Splitting
```java
String csv = "apple,banana,cherry";
String[] fruits = csv.split(",");

String sentence = "Java   is    awesome";
String[] words = sentence.split("\\s+");  // ["Java", "is", "awesome"]

String path = "C:\\Users\\John\\Documents";
String[] parts = path.split("\\\\");  // ["C:", "Users", "John", "Documents"]
```

### Find and Replace
```java
String text = "The color is red and the colour is blue";
String replaced = text.replaceAll("colou?r", "shade");
// "The shade is red and the shade is blue"

// Replace with groups
String dates = "2024-02-11 and 2024-12-25";
String formatted = dates.replaceAll(
    "(\\d{4})-(\\d{2})-(\\d{2})", 
    "$3/$2/$1"
);
// "11/02/2024 and 25/12/2024"
```

### Username Validation
```java
public class UsernameValidator {
    // 3-16 characters, alphanumeric and underscore only
    private static final Pattern USERNAME_PATTERN = Pattern.compile(
        "^[a-zA-Z0-9_]{3,16}$"
    );
    
    public static boolean isValidUsername(String username) {
        return username != null && USERNAME_PATTERN.matcher(username).matches();
    }
}
```

### Credit Card Masking
```java
public static String maskCreditCard(String cardNumber) {
    return cardNumber.replaceAll("\\d(?=\\d{4})", "*");
}

// Usage
maskCreditCard("1234567812345678");  // "************5678"
```

---

## Best Practices and Performance

### 1. Compile Patterns Once
```java
// BAD - Compiles pattern every time
public boolean validateEmail(String email) {
    return email.matches("^[\\w.-]+@[\\w.-]+\\.\\w+$");
}

// GOOD - Compile once, reuse
public class EmailValidator {
    private static final Pattern EMAIL_PATTERN = 
        Pattern.compile("^[\\w.-]+@[\\w.-]+\\.\\w+$");
    
    public static boolean validate(String email) {
        return EMAIL_PATTERN.matcher(email).matches();
    }
}
```

### 2. Use Non-Capturing Groups When Possible
```java
// Capturing groups (slower)
Pattern.compile("(red|green|blue)");

// Non-capturing groups (faster)
Pattern.compile("(?:red|green|blue)");
```

### 3. Be Specific with Quantifiers
```java
// BAD - Too greedy, poor performance
Pattern.compile(".*");

// GOOD - More specific
Pattern.compile("\\w{1,50}");
```

### 4. Anchor Your Patterns
```java
// Faster with anchors
Pattern.compile("^\\d{3}-\\d{3}-\\d{4}$");

// Slower without anchors
Pattern.compile("\\d{3}-\\d{3}-\\d{4}");
```

### 5. Use Possessive Quantifiers for Performance
```java
// Backtracking (slower if match fails)
Pattern.compile(".*,");

// No backtracking (faster)
Pattern.compile(".*+,");
```

### 6. Handle PatternSyntaxException
```java
public static Pattern compilePattern(String regex) {
    try {
        return Pattern.compile(regex);
    } catch (PatternSyntaxException e) {
        System.err.println("Invalid regex: " + e.getMessage());
        System.err.println("Error at position: " + e.getIndex());
        return null;
    }
}
```

### 7. Use StringBuilder for Multiple Replacements
```java
// For multiple replacements
public static String replaceMultiple(String text) {
    Matcher matcher = Pattern.compile("\\d+").matcher(text);
    StringBuilder result = new StringBuilder();
    
    while (matcher.find()) {
        matcher.appendReplacement(result, "NUMBER");
    }
    matcher.appendTail(result);
    
    return result.toString();
}
```

### 8. Reset Matcher for Reuse
```java
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher("123 and 456");

matcher.find();  // finds "123"
matcher.reset(); // reset to beginning
matcher.find();  // finds "123" again

// Or reset with new text
matcher.reset("789 and 012");
matcher.find();  // finds "789"
```

### 9. Limit Catastrophic Backtracking
```java
// BAD - Can cause catastrophic backtracking
Pattern.compile("(a+)+b");

// GOOD - Use possessive or atomic groups
Pattern.compile("(a++)+b");
Pattern.compile("(?>a+)+b");
```

### 10. Use String Methods for Simple Cases
```java
// Overkill for simple tasks
text.replaceAll("\\.", ",");

// Better for simple replacement
text.replace(".", ",");
```

---

## Summary

Regular expressions in Java provide powerful pattern matching capabilities through the `Pattern` and `Matcher` classes. Key points:

- **Compile patterns once** for better performance
- **Use appropriate quantifiers** (greedy, lazy, possessive)
- **Leverage capturing groups** for extracting data
- **Apply lookahead/lookbehind** for complex conditions
- **Handle exceptions** properly
- **Optimize patterns** to avoid performance issues

Common applications include validation (email, phone, URLs), text parsing, data extraction, and string manipulation. Understanding regex fundamentals and best practices ensures efficient and maintainable code.

---

## Additional Resources

- Java Pattern Class: [java.util.regex.Pattern](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/regex/Pattern.html)
- Java Matcher Class: [java.util.regex.Matcher](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/regex/Matcher.html)
- Regex Testing Tools: [regex101.com](https://regex101.com/), [regexr.com](https://regexr.com/)

---

**Last Updated:** February 2026
