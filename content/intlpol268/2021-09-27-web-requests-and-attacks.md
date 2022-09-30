# Web requests

## GET requests
Useful for simple requests: retrieving a resource for a server; example: search

* GET's are idempotent: same request is the same every time; should not change server state
* GET fields are limited to the URI

## Single Sign On
Allows user to log into multiple websites with only one login system

Advantages:

* Single security implementation; only one login account needs to be maintained
* Single point of failure
* Allows security team at SSO provider to shut down suspicious login requests on other sites
* Can force 2FA on multiple sites if SSO provider enforces it

## POST requests
For changing server state; contains URI but also can have much more fields that are not contained in the URI

## Cookies
Contained in browser sessions; information to remember user identity and can be used for later authentication

Who can see a cookie?

* Same-origin policy: tries (but fails) to prevent websites from messing with each other

# Web Attacks

## Asking the app to do what you want

* Insert proxy between server and client; change fields in request
    - Only works when field validation is client-side

## Using the web app to get code running in someone else's browser

Cross-site scripting (XSS)

* Inject script into server via web request
* Server saves script in database (stored XSS)
* Server serves script to another user when database request is made
    - User's browser does not know not to trust malicious payload and executes it

## Injecting commands to be run directly by the web app

SQL injection

* Attacker inserts malicious SQL into form field
* When SQL is performed on the form field, the entry confuses server into running it

Example:

```sql
SELECT * from Users where name=''or 1=1;' and password='pass1234';
```