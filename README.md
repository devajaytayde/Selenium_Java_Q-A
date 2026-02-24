
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
