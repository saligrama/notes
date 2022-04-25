# Authentication and Session Management

## Pre-history: HTTP auth

* HTTP request: `GET /index.html`
* HTTP response contains: `WWW-Authenticate: Basic realm="Password Required"`
* Browser sends hashed password on all subsequent HTTP requests
* Caveats:
    - User cannot log out other than by closing browser
        - What if user has multiple accounts?
    - Site cannot customize password dialog
    - Confusing dialog to users
    - Easily spoofed

## Modern session management

### Login

* When visiting website, site creates anonymous session and hands session ID back in a cookie
* User will POST with username and password, website checks credentials and elevates token to logged-in

### Logout

* Delete session token from client
* Mark session token as expired on server

### Authenticating users

* First idea: plaintext passwords (terrible)
    - Store password and check match against user input
    - Don't trust anything that provides you your password
* Second idea: store password hash (bad)
    - Store `SHA-1(pw)` and check match against `SHA-1(input)`
    - Weak against attacker who has hashed common passwords
* Third idea: store salted hash (better)
    - Store `(r, SHA-1(pw + r))` and check against `SHA-1(input + r)`
    - Prevents attackers from precomputing password hashes
    - Need to make sure to choose a hash function that is expensive to compute
    - In practice: use `bcrypt`, `scrypt`, or `pbkdf2` when building an application

## Phishing attacks

* Attacker sends a fraudulent message that tricks user into revealing sensitive data (e.g. login, credit card)
* Almost all take place over the web - difficult to know if you're in the right place as a user
* SMS-based 2-factor auth does little good; mostly protect against stolen credentials

Partial panacea: U2F and physical security keys

* Browser malware cannot steal user credentials
* U2F should not enable tracking across sites