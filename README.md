
**1. ImplicitWait**

driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

**2. ExpectedWait**

Package waitExample;
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


**3. FluentWait**

//Declare and initialise a fluent wait
FluentWait wait = new FluentWait(driver);
//Specify the timout of the wait
wait.withTimeout(5000, TimeUnit.MILLISECONDS);
//Sepcify polling time
wait.pollingEvery(250, TimeUnit.MILLISECONDS);
//Specify what exceptions to ignore
wait.ignoring(NoSuchElementException.class)

//This is how we specify the condition to wait on.
//This is what we will explore more in this chapter
wait.until(ExpectedConditions.alertIsPresent());


**4. Script Wait**
driver.manage().timeouts().setScriptTimeout(100,SECONDS);


**5. Page Wait**
driver.manage().timeouts().pageLoadTimeout(100, SECONDS);

**6. Thread Wait**
thread.sleep(1000);



**StaleElementException**


1. Use WebDriverWait

  wait.until(ExpectedConditions.presenceOfElementLocated(Web element locator")))

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

         WebElement ele = wait

                    .until(ExpectedConditions.presenceOfElementLocated(By.cssSelector("textarea[name='q']")));   

 

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


Method 2: Wait until the element is refreshed 

WebElement ele = wait.until(ExpectedConditions.refreshed(ExpectedConditions.presenceOfElementLocated(By.cssSelector("textarea[name='q']"))));


2. Use the try-catch block
  WebElement element = driver.findElement(By.id("web element locator"));

try {  

  element.click();

} catch (StaleElementReferenceException e) {

  // Refresh the page

  driver.navigate().refresh();

 

  // Try to locate the element again

  element = driver.findElement(By.id("web element locator "));

  element.click();

}

4. Use Page Object Model

   Page Factory initializes the page elements lazily which means they are initialized only when they are accessed within page methods.

If the elements become stale, it will automatically reinitialize it whenever it is accessed the next time.



5. Refresh the web page




**Pass By Val & Pass By Reference**


Pass By Value does not change scope of original variables-
public class Tester{
   public static void main(String[] args){
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
      // Swap n1 with n2
      int c = a;
      a = b;
      b = c;
      System.out.println("After swapping(Inside), a = " + a + " b = " + b);
   }
}

Output
 
Before swapping, a = 30 and b = 45
Before swapping(Inside), a = 30 b = 45
After swapping(Inside), a = 45 b = 30
**Now, Before and After swapping values will be same here**:
After swapping, a = 30 and b is 45


**Call by Reference”** means passing a reference (i.e. memory address) of the object by value to a method

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
      // Swap n1 with n2
      IntWrapper c = new IntWrapper(a.a);
      a.a = b.a;
      b.a = c.a;
      System.out.println("After swapping(Inside), a = " + a.a + " b = " + b.a);
   }
}
class IntWrapper {
   public int a;
   public IntWrapper(int a){ this.a = a;}
}



