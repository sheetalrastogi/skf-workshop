# skf-workshop
Demo PHP Application with security vulnerabilities, can be used to teach SKF in a workshop

Requirements for this web-application:
- Webserver
  - Tested with Apache2
- PHP5
- Mysql
  - PHP Code uses the Mysql PDO driver
  - user: root
  - password: root
- Database test

Remark:
Do not run this outside a secured LAB environment!

#-------------------

To **monitor JavaScript errors in the browser console using Chrome DevTools Protocol with Selenium Java**, you can use the DevTools Log domain and filter for error-level log entries.

Below is a concise example:

```java name=MonitorJSErrorsInConsoleExample.java
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.log.Log;
import org.openqa.selenium.devtools.v124.log.model.Entry;

import java.util.Optional;

public class MonitorJSErrorsInConsoleExample {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable console log monitoring
        devTools.send(Log.enable());

        // Listen for console log entries and filter for JavaScript errors
        devTools.addListener(Log.entryAdded(), (Entry entry) -> {
            if ("error".equalsIgnoreCase(entry.getLevel().toString())) {
                System.out.println("JavaScript Error: " + entry.getText());
                if (entry.getUrl().isPresent()) {
                    System.out.println("  at: " + entry.getUrl().get() + " line: " + entry.getLineNumber().get());
                }
            }
        });

        driver.get("https://www.example.com"); // Use a page that may trigger JS errors

        // Wait so JS errors can be captured
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}
```

**How this works:**
- Enables Chrome DevTools Log domain.
- Listens for log entries with level `"error"`, which are JavaScript errors.
- Prints error message, and if available, its location (URL and line number).

**Tip:**  
You can collect these errors into a list for reporting, or further filter by `entry.getSource()` for errors specifically from `javascript`.


#1. click on an element using Chrome Dev Protocol
----------------------------------------------------


Selenium itself interacts with elements using standard WebDriver APIs like `findElement().click()`. However, if you want to **click an element using Chrome DevTools Protocol** (CDP) in Selenium Java, you'll need to use the DevTools "Input" domain to dispatch a mouse event at specific coordinates—since CDP doesn't provide a direct "click this element" command.

**Typical steps:**
1. Find the element using Selenium.
2. Get its location and size.
3. Use CDP to send a mouse click event at the element's coordinates.

---

### **Example: Click an Element via CDP**

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.Point;
import org.openqa.selenium.Dimension;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.input.Input;

import java.util.Optional;

public class CDPClickExample {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        driver.get("https://www.example.com");

        // Find the element using Selenium
        WebElement element = driver.findElement(By.id("myButton"));

        // Get element location and size
        Point location = element.getLocation();
        Dimension size = element.getSize();

        // Calculate the element's center (relative to the page)
        int centerX = location.getX() + size.getWidth() / 2;
        int centerY = location.getY() + size.getHeight() / 2;

        // Send mouse pressed and released events via CDP
        devTools.send(Input.dispatchMouseEvent(
            Input.DispatchMouseEventType.MOUSE_PRESSED,
            (double) centerX,
            (double) centerY,
            Optional.of(0), // Left button
            Optional.of(1), // Click count
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty()
        ));

        devTools.send(Input.dispatchMouseEvent(
            Input.DispatchMouseEventType.MOUSE_RELEASED,
            (double) centerX,
            (double) centerY,
            Optional.of(0), // Left button
            Optional.of(1), // Click count
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty(),
            Optional.empty()
        ));

        // Wait to observe effect
        try { Thread.sleep(2000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}
```

---

**Notes:**
- Replace `"myButton"` with your element's actual selector.
- This approach clicks at the element’s center. For off-screen/scrolled elements, ensure the element is visible first.
- This is useful when normal Selenium `.click()` fails due to overlays or JavaScript restrictions.




#2.  
To **monitor JavaScript errors in the browser console using Chrome DevTools Protocol with Selenium Java**, you can use the DevTools Log domain and filter for error-level log entries.

Below is a concise example:

```java name=MonitorJSErrorsInConsoleExample.java
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.log.Log;
import org.openqa.selenium.devtools.v124.log.model.Entry;

import java.util.Optional;

public class MonitorJSErrorsInConsoleExample {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable console log monitoring
        devTools.send(Log.enable());

        // Listen for console log entries and filter for JavaScript errors
        devTools.addListener(Log.entryAdded(), (Entry entry) -> {
            if ("error".equalsIgnoreCase(entry.getLevel().toString())) {
                System.out.println("JavaScript Error: " + entry.getText());
                if (entry.getUrl().isPresent()) {
                    System.out.println("  at: " + entry.getUrl().get() + " line: " + entry.getLineNumber().get());
                }
            }
        });

        driver.get("https://www.example.com"); // Use a page that may trigger JS errors

        // Wait so JS errors can be captured
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}
```

**How this works:**
- Enables Chrome DevTools Log domain.
- Listens for log entries with level `"error"`, which are JavaScript errors.
- Prints error message, and if available, its location (URL and line number).

**Tip:**  
You can collect these errors into a list for reporting, or further filter by `entry.getSource()` for errors specifically from `javascript`.






