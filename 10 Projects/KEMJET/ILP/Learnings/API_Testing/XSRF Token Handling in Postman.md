---

title: XSRF Token Handling in Postman  
tags:
- api 
- postman 
- web-security
- csrf
- debugging  
type: technical-note
---

# XSRF Token Handling in Postman

## 1. What is an XSRF Token?

An **XSRF token** (Cross-Site Request Forgery token) is a security token used by web applications to help protect against CSRF attacks.

When an application uses CSRF protection, the server may issue a token through a cookie or another response mechanism. A subsequent request must provide the expected token in the required format.

**Purpose:** Verify that a request satisfies the application's CSRF protection requirements.

> Note: An XSRF token is not the same as an API key, API secret, session cookie, or OAuth access token.

## 2. Why do we need it in Postman?

Some web APIs require a CSRF token when performing operations such as submitting forms or making state-changing requests.

Manually copying the token every time is inconvenient. Postman scripts can automate this process:

1. Send a request to the server.
    
2. Read the response headers.
    
3. Extract the `XSRF-TOKEN` cookie.
    
4. Decode the token if necessary.
    
5. Save it as an environment variable.
    
6. Reuse it in subsequent requests.
    

## 3. Understanding the workflow

```
Initial Request
      |
      v
Server Response
      |
      v
Set-Cookie: XSRF-TOKEN=abc123
      |
      v
Extract XSRF Token
      |
      v
Decode Token if Required
      |
      v
Save as {{xsrf_token}}
      |
      v
Use Token in the Next Request
```

## 4. Step 1 — Extract the token from response headers

```
if (!xsrfToken) {
    const headers = res.headers.all();

    for (const header of headers) {
        if (
            header.key.toLowerCase() === 'set-cookie' &&
            header.value.includes('XSRF-TOKEN')
        ) {
            const match = header.value.match(
                /XSRF-TOKEN=([^;]+)/
            );

            if (match) {
                xsrfToken = match[1];

                console.log(
                    'XSRF-TOKEN extracted from Set-Cookie header'
                );
            }
        }
    }
}
```

### How it works

- `if (!xsrfToken)` checks whether the token is missing.
    
- `res.headers.all()` retrieves response headers using the API available in this Postman scripting context.
    
- The `for` loop examines each header.
    
- `header.key.toLowerCase()` makes the header-name comparison case-insensitive.
    
- `header.value.includes('XSRF-TOKEN')` checks whether the cookie contains the token name.
    
- The regular expression extracts the cookie value.
    
- `match[1]` contains the captured token value.
    

### Regular expression explained

```
XSRF-TOKEN=([^;]+)
```

|Pattern|Meaning|
|---|---|
|`XSRF-TOKEN=`|Find the cookie name|
|`()`|Capture a matching value|
|`[^;]+`|Match one or more characters other than `;`|

Example:

```
Set-Cookie: XSRF-TOKEN=abc123xyz; Path=/; Secure
```

Extracted value:

```
abc123xyz
```

**Why stop at the semicolon?**

Cookie attributes such as `Path`, `Secure`, and `HttpOnly` follow the cookie value. The semicolon separates the cookie value from those attributes.

## 5. Step 2 — Decode and save the token

```
if (xsrfToken) {
    const decodedToken = decodeURIComponent(xsrfToken);

    pm.environment.set('xsrf_token', decodedToken);

    console.log(
        'CSRF token stored in environment successfully'
    );
} else {
    console.warn(
        'XSRF-TOKEN not found. Response status: ' + res.status()
    );

    console.log(
        'Response headers received:',
        JSON.stringify(
            res.headers.all().map(function(h) {
                return h.key;
            })
        )
    );
}
```

### Important functions

#### `decodeURIComponent()`

Converts percent-encoded characters into their corresponding characters.

Example:

```
decodeURIComponent('abc%2F123');
// Result: abc/123
```

Only decode when the server's expected token format requires it.

#### `pm.environment.set()`

Stores a value in the currently selected Postman environment.

```
pm.environment.set('xsrf_token', decodedToken);
```

This creates or updates the variable:

```
xsrf_token = decodedToken
```

You can reference this variable in later requests using:

```
{{xsrf_token}}
```

**Why use an environment variable?**

It allows multiple requests to reuse the same token without manually copying its value.

`pm.variables.set()` has different scope and precedence behavior, so an environment variable is useful when you specifically want to persist the value in the selected environment.

### Why log only header names?

```
res.headers.all().map(function(h) {
    return h.key;
})
```

This helps troubleshoot missing cookies without deliberately printing their values, which may contain sensitive information.

## 6. Step 3 — Use the token in the next request

If the server expects an `X-XSRF-TOKEN` header:

|Header|Value|
|---|---|
|`X-XSRF-TOKEN`|`{{xsrf_token}}`|

Postman resolves the variable when sending the request.

The required header name is application-specific. Confirm the API documentation to determine whether the server expects `X-XSRF-TOKEN`, another header, or only a cookie.

Some applications require both:

- An XSRF token header
    
- A session cookie
    

Saving the token in an environment variable does not automatically send the associated session cookie.

## 7. Common troubleshooting cases

|Problem|Possible reason|What to check|
|---|---|---|
|Token not found|No matching cookie was returned|Inspect response header names and cookie settings|
|Token is empty|Extraction pattern did not match|Inspect the cookie format|
|`401 Unauthorized`|Authentication failed|Check credentials and access token|
|`403 Forbidden`|Authorization or CSRF validation failed|Check permissions, token, and session cookie|
|Token rejected|Incorrect encoding or stale token|Check whether decoding is required and refresh the token|
|Variable is unresolved|Wrong environment selected or variable missing|Check the active environment and `xsrf_token` value|

An HTTP status code alone does not prove that a CSRF token is the cause.

## 8. Important distinction: XSRF token vs API credentials

These values serve different purposes.

|Item|Purpose|
|---|---|
|API key|Identifies the API consumer or application|
|API secret|Authenticates the application, depending on the API scheme|
|Access token|Grants access according to its scope and expiry|
|Session cookie|Identifies a session in cookie-based authentication|
|XSRF token|Helps validate requests against CSRF attacks|

### Connection to my Sandbox.co.in debugging

My earlier authentication error reported:

```
Missing required request parameters: [x-api-secret, x-api-key]
```

That error means the authentication request was missing the required API credentials.

**An XSRF token does not replace an API key or API secret.**

I must first fix the missing authentication headers. I should only implement XSRF handling if the particular API or web application requires it.

## 9. Security best practices

- Never share real API keys, API secrets, access tokens, or session cookies in notes or screenshots.
    
- Avoid committing secret-bearing Postman environment files to Git.
    
- Do not log the actual XSRF token unnecessarily.
    
- Use the correct environment for development, testing, or production.
    
- Refresh tokens when required by the server.
    
- Confirm the server's official authentication and CSRF documentation before implementing this workflow.
    

## 10. What I learned

- I understand what an XSRF token is.
    
- I can identify a `Set-Cookie` response header.
    
- I understand how the regular expression extracts the token.
    
- I understand when `decodeURIComponent()` is needed.
    
- I can save a value using `pm.environment.set()`.
    
- I can reuse a variable using `{{xsrf_token}}`.
    
- I can distinguish an XSRF token from API credentials.
    
- I can troubleshoot missing tokens without exposing their values.
    

## Related Notes

- [[Postman Environment Variables]]
    
- [[HTTP Request and Response Headers]]
    
- [[Cookies and Sessions]]
    
- [[API Authentication]]
    
- [[OAuth 2.0 Access Tokens]]
    
- [[CSRF and XSRF Protection]]
    
- [[Regular Expressions in JavaScript]]