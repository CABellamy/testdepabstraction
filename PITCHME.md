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
public function testLoginSuccess() 
{
    //open page
    $driver = new Webdriver();
    $driver->get('https://mywebsite.com');

    //click Sign In Link
    $signInLink = driver->findElement(WebDriverBy::linkText('Sign In'));
    $signInLink.click();

    //find username field and enter username
    $usernameField = driver->findElement(WebDriverBy::id('Username'));
    $usernameField->sendKeys('username');

    //find password field and enter password
    $passwordField = driver->findElement(WebDriverBy::id('Password'));
    $passwordField->sendKeys('password');

    //find submit button and click
    $submitButton = driver->findElement(WebDriverBy::id('Submit'));
    $submitButton->click();

    //assert that success text is displayed
    $successText = driver->findElements(WebDriverBy::id('SuccessText'));
    AssertNotEmpty($successText);
}
```

# Why is this bad?

+++

Test code is tightly coupled to execution

+++

If link text changes to 'log in'

The form changes IDs

What if there's an AB test for log on

+++

The test breaks
 
---

# What can we do?

---

# Refactor

```
public function testLogin() 
{
    $driver = new Webdriver();
    $driver->get("https://github.com");
```
+++
# becomes
```
public function testLogin(Portal $portal) 
{
    $portal->navigateToHomepage();
}
```

```
//some config code to set up environment during initialisation of tests

$homepage = config->currentEnv->homePage();

public function navigateToHomepage() 
{
    driver.open($homepage);
}

```
+++

Move the test dependency (URL) away from the test

Use the same test for multiple environments

---

Old way

```
    driver.type("name=login", "username");
    driver.type("name=password", "password");
    driver.click("name=commit");
```

+++

Page object pattern

```
class HomePage {

}

function testLogin()
{
    navigateToHomepage();
    login(account: {
        username: 'test',
        password: 'password'
    });
}
```
```
function login(account) {
    driver.findElement(By.linkText("Sign In"));
    driver.type
}
