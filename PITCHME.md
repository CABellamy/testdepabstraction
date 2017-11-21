---

# Test Dependency Abstraction

## Chris Bellamy

---

### Testing dependencies 

Tests (detail) depend on abstraction 

Tests should describe a case/event 

---

### Brittle tests

```
func testLogin() {
    var driver = new Webdriver();
    driver.open("https://github.com");
    var signInLink = driver.findElement(By.linkText("Sign In"));
    element.click();
    driver.waitForPageToload("1000");
    driver.type("name=login", "username");
    driver.type("name=password", "password");
    driver.click("name=commit");
}
```

Why is this bad?

+++

#### The link text changes to 'log in'?

#### The form changes IDs?

#### What if there's an AB test for log on?

+++

Broken tests

+++

---

# What can we do?

---

# Refactor

```
func testLogin() {
    var driver = new Webdriver();
    driver.open("https://github.com");
```
+++

func testLogin() {
    navigateToHomepage();
}

+++

```
//some config switching code to set up environment during initialisation

var homepage = config.currentEnv.homePage

func navigateToHomepage(driver) {
    driver.open(homepage)
}

```

+++

---

