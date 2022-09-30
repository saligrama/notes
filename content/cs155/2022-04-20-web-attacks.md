# Web Attacks

## Cookie Attack

* Suppose CS155 allows you to login and submit homework at `cs155.stanford.edu`
    - Login with `username` and `password`; `cs155.stanford.edu` responds with `cookies: session=abc`
    - Now access `dabo.stanford.edu/memes`, `dabo.stanford.edu` sets cookie to `cookies: session=def` for `Domain=stanford.edu`
    - Now browser will send both session cookies, and it is up to server to determine which is correct

## Cross-Site Request Forgery

* Type of web exploit where a website transmits unauthorized commands as a user that the web app trusts
    - In CSRF attack, user is tricked into submitting an unintended/unrealized request to website
    - Cookie-based authentication is not sufficient for requests that have any side effect
* Preventing CSRF attacks:
    - Referer validation
    - Secret validation token
    - Custom HTTP header
    - sameSite cookies

### Referer validation

* Referer header contains address of previous web page from which a link to currently requested page was followed
    - Allows servers to identify where people are visiting from
    - `https://bank.com` requesting `https://bank.com` is ok
    - `https://evil.com` requesting `https://bank.com` is not ok
    - No referer requesting `https://bank.com`: up to website
        - Referer is not required by HTTP spec, and can lead to privacy leak (website B knows you visited website A first) so people sometimes want to delete it

### Secret validation token

* `bank.com` includes a secret value in every form that the server can validate
    ```html
    <form action=“https://bank.com/transfer" method="post">
        <!-- Request will fail without the token -->
        <input type="hidden" name="csrf_token" value=“434ec7e838ec3167ef5">
        <input type=“text" name="to">
        <input type=“text" name=“amount”>
        <button type="submit">Transfer!</button>
    </form>
    ```
* Coming up with a secret token that user can access but attacker can't:
    - Send session-specific token as part of the page
    - Attacker cannot access because SOP blocks reading content

### Forcing CORS pre-flight

* Requests that required and passed CORS pre-flight check are safe
    - Typical `GET` and `POST` don't require pre-flight even if XHR
* We can force browser to make pre-flight check and tell server
    - Add custom header to XHR
    - Forces pre-flight
    - Never sent by browser itself when performing normal `GET` or `POST`
* Typically developers use `X-Requested-By` or `X-Requested-With`

### sameSite cookies

* Cookie option that prevents browser from sending cookie along with cross-site requests
* Strict mode: never send cookie in any cross-site browsing context even when following a regular link
* Lax mode: session cookie is allowed when following a regular link but blocks it in CSRF-prone request methods (e.g. POST)

## SQL injection

e.g. login form

```php
$login = $_POST['login'];
$pass = $_POST['password'];
$sql = "SELECT id FROM users WHERE username = '$login' AND password = '$password'";
$rs = $db->executeQuery($sql);

if $rs.count > 0 {
    // success
}
```

* With non-malicious input, this is totally ok!
* Suppose with malicious input:
    - `username: ' or 1=1 --`
    - `password: 123`
    - Then the SQL statement will look like:
        ```sql
        SELECT id FROM users WHERE uid='' or 1=1 -- AND pwd...
        ```
    - This will return every user in the database!
* Alternatively:
    - `username: '; DROP TABLE [users] --`
    - `password: 123`
    - Then the SQL statement will look like:
        ```sql
        SELECT id FROM users WHERE uid=''; DROP TABLE [users] -- AND pwd...
        ```
    - This will delete all user info in the database
* Even worse: Microsoft SQL server lets you run arbitrary shellcode through `xp_shellcmd`
    - `username: '; exec xp_cmdshell 'net user add user pwd' --`
    - `password: 123`
    - Then the SQL statement will look like:
        ```sql
        SELECT id FROM users WHERE uid=''; exec xp_cmdshell 'net user add user pwd' -- AND pwd...
        ```
    - This will add a user account controlled by the attacker to the *system* (not database!)

### Preventing SQL injection

* Never trust user input, particularly when constructing a command
    - Avoid building SQL statements yourself
* There are tools for safely passing user input to databases:
    - Parametrized (i.e. prepared) SQL - allows you to send query and arguments separately to server
        - No need to escape untrusted data
        - Faster because server can cache query plan
    - ORM (Object Relational Mapper) - uses prepared SQL internally
        - e.g. Django

## Cross-Site Scripting (XSS)

* Attack occurs when application takes untrusted data and sends it to a web browser without proper validation or sanitization
    - Command/SQL injection: attacker's malicious code is executed on app server
    - XSS: attacker's malicious code is executed on victim's browser

e.g. search on `https://google.com/search?q=apple`

```html
<html>
    <title>Search results</title>
    <body>
        <h1>Results for <?php echo $_GET["q"] ?></h1>
    </body>
</html>
```

* With non-malicious input: totally ok!
* With malicious input:
    - Search query: `https://google.com/search?q=<script>alert("hello")</script>`
    - Rendered in browser:
        ```html
        <h1>Results for <script>alert("hello")</script></h1>
        ```
    - Can use this to steal cookies (or really do anything within `<script>...</script>`)

### Reflected XSS

* Attack script is reflected back to the user as part of a page from the victim's site
    - e.g. Attackers contacted PayPal users via email and fooled them into accessing a URL on legitimate PayPal website
    - Injected code redirected PayPal visitors to a page warning users their accounts had been compromised
    - Victims were then redirected to a phishing site and prompted to enter sensitive financial data

### Stored XSS
* The attacker stores the malicious code in a resource managed by the web application, such as a database
    - Old forum software has these bugs
    - MySpace tried to filter out script-type tags, but missed one (e.g. can run JS inside of CSS tags)

### Preventing XSS

* Previously: tried to filter out tags that could be possibly part of an attack
    - However: this is very hard: huge amount of different ways to run JS content
* Content Security Policy (CSP):
    - HTTP header that servers can send which declares which dynamic resources (e.g. JS) are allowed
    - e.g. JS: `Content-Security-Policy: script-src 'self'`
        - JS can only be loaded from the same domain as the page
        - No JS will be executed from other domains
        - No inline scripts will be executed
    - Can be very particular about where any type of resources (e.g scripts, fonts, CSS, images) can be loaded from
        - Mozilla default policy: only allow images, scripts, AJAX, form actions, CSS from same origin, and does not allow any other resources to load (and no inline scripts)