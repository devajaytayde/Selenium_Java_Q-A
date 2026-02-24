# Selenium Java Q&A

## Table of Contents
- [Wait Strategies](#wait-strategies)
- [StaleElementException](#staleelementexception)
- [Pass By Value & Pass By Reference](#pass-by-value--pass-by-reference)

---

## Wait Strategies

### 1. ImplicitWait

Waits implicitly for a specified time before throwing NoSuchElementException.

```java
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
```

---

### 2. ExplicitWait (WebDriverWait)

Waits for a specific condition to occur before proceeding.

```java
package waitExample;

import java.util.concurrent.TimeUnit;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;

public class WaitTest {
    private WebDriver driver;
    private String baseUrl;
    private WebElement element;

    @BeforeMethod
    public void setUp() throws Exception {
        driver = new FirefoxDriver();
        baseUrl = "http://www.google.com";
        driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
    }

    @Test
    public void testUntitled() throws Exception {
        driver.get(baseUrl);
        element = driver.findElement(By.id("lst-ib"));
        element.sendKeys("Selenium WebDriver Interview questions");
        element.sendKeys(Keys.RETURN);
        List<WebElement> list = driver.findElements(By.className("_Rm"));
        System.out.println(list.size());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }
}
```

---

### 3. FluentWait

A more flexible wait that allows custom polling intervals and exception handling.

```java
// Declare and initialise a fluent wait
FluentWait wait = new FluentWait(driver);

// Specify the timeout of the wait
wait.withTimeout(5000, TimeUnit.MILLISECONDS);

// Specify polling time
wait.pollingEvery(250, TimeUnit.MILLISECONDS);

// Specify what exceptions to ignore
wait.ignoring(NoSuchElementException.class);

// Specify the condition to wait on
wait.until(ExpectedConditions.alertIsPresent());
```

---

### 4. Script Wait

Sets a timeout for JavaScript execution.

```java
driver.manage().timeouts().setScriptTimeout(100, SECONDS);
```

---

### 5. Page Load Wait

Sets a timeout for page load completion.

```java
driver.manage().timeouts().pageLoadTimeout(100, SECONDS);
```

---

### 6. Thread Sleep

Pauses execution for a specified duration (generally not recommended for test automation).

```java
Thread.sleep(1000);
```

---

## StaleElementException

Occurs when an element reference is no longer valid in the DOM. Here are multiple solutions:

### Solution 1: Use WebDriverWait with presenceOfElementLocated

```java
public class ExplicitWait {
    public static void main(String[] args) {
        // Initialize WebDriver
        WebDriver driver = new ChromeDriver();

        // Open the webpage
        driver.get("https://google.com");

        // Create an explicit wait
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        // Use the explicit wait to handle StaleElementException
        try {
            // Wait until the element is present on the page
            WebElement ele = wait.until(
                ExpectedConditions.presenceOfElementLocated(By.cssSelector("textarea[name='q']"))
            );

            // Perform the action on the element
            ele.sendKeys("Selenium");
            System.out.println("Search text entered successfully.");

        } catch (org.openqa.selenium.StaleElementReferenceException e) {
            // Element became stale, handle accordingly
            System.out.println("StaleElementException occurred. Refreshing the page.");

            // Refresh the page
            // driver.navigate().refresh();

            // Re-locate the element after page refresh
            WebElement refreshedEle = driver.findElement(By.cssSelector("textarea[name='q']"));
            refreshedEle.sendKeys("Selenium");
            System.out.println("Search text entered successfully after page refresh.");
        }

        // Close the browser
        driver.quit();
    }
}
```

#### Method 2: Using refreshed() ExpectedCondition

```java
WebElement ele = wait.until(
    ExpectedConditions.refreshed(
        ExpectedConditions.presenceOfElementLocated(By.cssSelector("textarea[name='q']"))
    )
);
```

---

### Solution 2: Use Try-Catch Block

Re-locate the element after handling the exception.

```java
WebElement element = driver.findElement(By.id("web-element-locator"));

try {
    element.click();
} catch (StaleElementReferenceException e) {
    // Refresh the page
    driver.navigate().refresh();

    // Try to locate the element again
    element = driver.findElement(By.id("web-element-locator"));
    element.click();
}
```

---

### Solution 3: Use Page Object Model with Page Factory

Page Factory initializes page elements lazily (only when accessed). If elements become stale, they are automatically reinitialized on the next access.

```java
// Elements are initialized lazily and refreshed automatically
```

---

### Solution 4: Refresh the Web Page

```java
driver.navigate().refresh();
```

---

## Pass By Value & Pass By Reference

### Pass By Value

Does not change the scope of original variables. Changes made inside the method are local.

```java
public class Tester {
    public static void main(String[] args) {
        int a = 30;
        int b = 45;
        System.out.println("Before swapping, a = " + a + " and b = " + b);
        
        // Invoke the swap method
        swapFunction(a, b);
        
        System.out.println("\n**Now, Before and After swapping values will be same here**:");
        System.out.println("After swapping, a = " + a + " and b is " + b);
    }

    public static void swapFunction(int a, int b) {
        System.out.println("Before swapping(Inside), a = " + a + " b = " + b);
        
        // Swap a with b
        int c = a;
        a = b;
        b = c;
        
        System.out.println("After swapping(Inside), a = " + a + " b = " + b);
    }
}
```

**Output:**
```
Before swapping, a = 30 and b = 45
Before swapping(Inside), a = 30 b = 45
After swapping(Inside), a = 45 b = 30
**Now, Before and After swapping values will be same here**:
After swapping, a = 30 and b is 45
```

---

### Pass By Reference

Passing a reference (memory address) of an object by value to a method. Changes affect the original object.

```java
public class JavaTester {
    public static void main(String[] args) {
        IntWrapper a = new IntWrapper(30);
        IntWrapper b = new IntWrapper(45);
        System.out.println("Before swapping, a = " + a.a + " and b = " + b.a);
        
        // Invoke the swap method
        swapFunction(a, b);
        
        System.out.println("\n**Now, Before and After swapping values will be different here**:");
        System.out.println("After swapping, a = " + a.a + " and b is " + b.a);
    }

    public static void swapFunction(IntWrapper a, IntWrapper b) {
        System.out.println("Before swapping(Inside), a = " + a.a + " b = " + b.a);
        
        // Swap a with b
        IntWrapper c = new IntWrapper(a.a);
        a.a = b.a;
        b.a = c.a;
        
        System.out.println("After swapping(Inside), a = " + a.a + " b = " + b.a);
    }
}

class IntWrapper {
    public int a;
    
    public IntWrapper(int a) {
        this.a = a;
    }
}
```

**Output:**
```
Before swapping, a = 30 and b = 45
Before swapping(Inside), a = 30 b = 45
After swapping(Inside), a = 45 b = 30
**Now, Before and After swapping values will be different here**:
After swapping, a = 45 and b is 30
```

---

**Q. Can a Constructor Be Static in Java?**
```
A. No, as Static keyword applies to Class not for Object level

---

**Inheritance and Constructor Chaining: **
```
In Java, constructors are not inherited by subclasses. However, when a subclass is instantiated, the superclass constructor is implicitly called through constructor chaining. If a constructor were static, it would not be accessible to subclasses, violating the principles of inheritance and object initialization.

---
  


// Online Java Compiler
// Use this editor to write, compile and run your Java code online

class Main {
    static int  classvar = 300;
    
    static 
    {
        System.out.println("Static variable" + classvar);
        classvar = 200;
         System.out.println("Static variable" + classvar);
    }
    
    public static void main(String[] args) {
        System.out.println("Static variable" + classvar);
        
    }
    
}

## Main method can be overloaded but it will only execute Strings[] arg  one
class Main extends Parent {
    
    int mainvar = 100;
    Main()
    {
         System.out.println("Sub Class Constructor");
    }
    
    public static   void  main(String[] args) {
       
        Main mn = new Main();
    }
    
    public static   void  main(String args) {
       
            System.out.println("Non main will not exeute");
    }
}



## Contructor can not be Abstract, because, constructor can not be overridden 


## Constructor can not be Final, because, constructors are not inherited in java


## Runtime Polymorphism/ Dynamic Method Dispatch/Method Overriding
In Java, method overriding is used when a subclass needs to provide its own specific implementation of a method that is already defined in its parent (super) class.

Here’s why we override methods:

**1. To Achieve Runtime Polymorphism**
Overriding allows Java to decide at runtime which method implementation to call, based on the actual object type, not the reference type.
This enables dynamic method dispatch.

**2. To Change or Extend Behavior**
Sometimes the default behavior in the parent class is not suitable for the subclass.
By overriding, the subclass can modify or enhance the method’s functionality.

**3. To Implement Specific Behavior for a Subclass**
Different subclasses can have different implementations of the same method, while sharing the same method signature.

Note: Override keyword is used specificaly to ensure the method is correctly overridden, catching errors like typos.
Example:
Java

Copy code
class Animal {
    void sound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    @Override
    void sound() {
        System.out.println("Cat meows");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal a1 = new Dog(); // Runtime decides Dog's sound()
        Animal a2 = new Cat(); // Runtime decides Cat's sound()

        a1.sound(); // Output: Dog barks
        a2.sound(); // Output: Cat meows
    }
}
Key Points About Overriding
Method name, parameters, and return type must be exactly the same as in the parent class.
The overridden method cannot have a more restrictive access modifier.
Only inherited methods can be overridden (not static, final, or private methods).
Supports runtime polymorphism.


**Why We Can't Override Static Methods in Java**
```
Static methods are resolved at compile time, not runtime.
 it is not considered overriding but method hiding.
class Parent {
   public static void display() {
       System.out.println("Static method in Parent");
   }
}
class Child extends Parent {
   public static void display() {
       System.out.println("Static method in Child");
   }
}
public class Test {
   public static void main(String[] args) {
       Parent obj = new Child();
       obj.display(); // Outputs: Static method in Parent
   }
}

---

---
---

## Serenity Screenplay Pattern with Selenium
emphasizes roles, tasks, and outcomes
aligned with real-world user interactions
report with production like details /steps passed failed






## Key Takeaways

- **ImplicitWait**: Simple but applies globally to all elements
- **ExplicitWait**: More flexible, condition-based, recommended approach
- **FluentWait**: Most flexible with custom polling and exception handling
- **StaleElementException**: Handle with WebDriverWait, try-catch, or Page Factory
- **Pass by Value vs Reference**: Primitives use pass by value; objects use pass by reference
