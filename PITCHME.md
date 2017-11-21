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
public function testLogin() {
    $driver = new Webdriver();
    $driver->get("https://github.com");
    $signInLink = driver->findElement(By->linkText("Sign In"));
    $signInLink.click();
}
```

# Why is this bad?

+++

Test code is tightly coupled

The link text changes to 'log in'?

The form changes IDs?

What if there's an AB test for log on?

+++

The test breaks
 
---

# What can we do?

---

# Refactor

```
public function testLogin() {
    $driver = new Webdriver();
    $driver->get("https://github.com");
```
+++
# becomes
```
public function testLogin(Portal $portal) {
    $portal->navigateToHomepage();
}
```

```
//some config code to set up environment during initialisation of tests

var homepage = config.currentEnv.homePage();

function navigateToHomepage() {
    driver.open(homepage);
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
function testLogin(){
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
