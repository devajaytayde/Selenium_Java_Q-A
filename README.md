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

## Key Takeaways

- **ImplicitWait**: Simple but applies globally to all elements
- **ExplicitWait**: More flexible, condition-based, recommended approach
- **FluentWait**: Most flexible with custom polling and exception handling
- **StaleElementException**: Handle with WebDriverWait, try-catch, or Page Factory
- **Pass by Value vs Reference**: Primitives use pass by value; objects use pass by reference
