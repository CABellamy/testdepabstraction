---

# Test Dependency Abstraction

## Chris Bellamy

---

### Test dependencies 

Tests (detail) depend on abstraction 

Automated tests should describe a case/event and/or assert an outcome

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

The form changes IDs in the HTML code

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
    $driver->get("https://mywebsite.com");
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
    $this->driver->get('https://mywebsite.com');
}

```
+++

Move the test dependency (URL) away from the test

Use the same test for multiple environments

---

Old way

```
    $usernameField = driver->findElement(WebDriverBy::id('Username'));
    $usernameField->sendKeys('username');
```

+++

Page object pattern

```
function testLogin()
{
    navigateToHomepage();
    $account = [
        "username"=>"test",
        "password"=>"password"
    ];
    $homepage = new Homepage();
    $homepage->login($account);
}
```

+++
```
class HomePage {
    const USERNAME_FIELD_SELECTOR = 'input#username.text-input';

    const PASSWORD_FIELD_SELECTOR = 'input#password.text-input';

    const SUBMIT_BUTTON_SELECTOR = 'input[type=submit]';
...
```
+++
```
...
    function login(account) {
        $usernameField = driver->findElement(
            WebDriverBy::CssSelector(static::USERNAME_FIELD_SELECTOR)
        );
        $usernameField->sendKeys($account['username']);

        $passwordField = driver->findElement(
            WebDriverBy::CssSelector(static::PASSWORD_FIELD_SELECTOR)
        );
        $passwordField->sendKeys($account['password']);

        $submitButton = driver->findElement(
            WebDriverBy::CssSelector(static::SUBMIT_BUTTON_SELECTOR)
        );
        $submitButton->click();
    }
}
```
@[1-5](find username field and enter username) 
@[6-10](find password field and enter password) 
@[12-16](find submit button and click)
---
Moved the test concerns away from the test fixture

Page changes. Update once 

Test code doesn't have to change

---

Dependency inversion principle

Tell don't ask

Easier to maintain and read

---

Thanks

