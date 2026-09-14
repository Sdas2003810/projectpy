# Fetching Internet Resources with `urllib` HOWTO

The `urllib.request` module provides an extensible framework for retrieving URLs (HTTP, HTTPS, FTP). While third-party packages like `httpx` and `requests` are common, mastering `urllib` enables writing zero-dependency scripts that run on any standard Python installation.

---

## Basic Resource Retrieval with `urlopen()`

The simplest retrieval requires just one function:

```python
from urllib.request import urlopen

with urlopen("https://httpbin.org/get") as response:
    status_code = response.status       # e.g., 200
    headers = response.headers          # HTTPMessage mapping headers
    body = response.read().decode("utf-8")

print(f"Status: {status_code}")
print(f"Server header: {headers.get('Server')}")
```

---

## Customizing Requests with Headers

Many web APIs reject requests with missing or generic User-Agent headers. Use `urllib.request.Request` to construct customized HTTP requests:

```python
from urllib.request import Request, urlopen

url = "https://api.github.com/events"

headers = {
    "User-Agent": "ProjectPy-DocEngine/1.0",
    "Accept": "application/vnd.github.v3+json"
}

req = Request(url, headers=headers)

with urlopen(req) as resp:
    data = resp.read().decode("utf-8")
```

---

## Sending Data: Making POST Requests

To send data via HTTP POST:
1. Encode data into query string bytes using `urllib.parse.urlencode()`.
2. Pass the bytes to `Request(url, data=...)`. Python automatically switches the HTTP method from GET to POST when the `data` parameter is supplied.

```python
from urllib.request import Request, urlopen
from urllib.parse import urlencode

url = "https://httpbin.org/post"

form_fields = {
    "username": "souhardya",
    "submission_type": "automated"
}

# Encode form data to URL-encoded byte sequence
post_data = urlencode(form_fields).encode("utf-8")

req = Request(url, data=post_data, method="POST")

with urlopen(req) as resp:
    print(resp.read().decode("utf-8"))
```

---

## Exception Handling: `URLError` & `HTTPError`

Network requests can fail for various reasons: server errors (500), missing pages (404), DNS lookup failures, or connection timeouts.

Handle both `HTTPError` and `URLError`:

```python
from urllib.request import urlopen, Request
from urllib.error import HTTPError, URLError

target_url = "https://httpbin.org/status/404"

try:
    with urlopen(Request(target_url), timeout=5) as response:
        print("Success:", response.status)

except HTTPError as e:
    # Server returned an HTTP error code (4xx or 5xx)
    print(f"HTTP Error Code: {e.code}")
    print(f"Server Reason:   {e.reason}")
    print("Error body:      ", e.read().decode("utf-8"))

except URLError as e:
    # Network level failure (DNS down, no route to host, timeout)
    print(f"Network Failure: {e.reason}")
```

<div class="terminal-output">
HTTP Error Code: 404
Server Reason:   NOT FOUND
</div>

!!! important "`HTTPError` is a subclass of `URLError`"
    Always catch `HTTPError` **before** `URLError` in your `try...except` blocks, because `HTTPError` inherits from `URLError`.

---

## Advanced Architecture: Handlers and Openers

Under the hood, `urlopen()` uses a default `OpenerDirector` instance equipped with handlers for redirects, HTTP, HTTPS, and errors.

To handle cookies, proxies, or authentication, construct a custom opener using `build_opener()`:

### Managing Cookies with `http.cookiejar`

```python
from urllib.request import build_opener, HTTPCookieProcessor
from http.cookiejar import CookieJar

# Create an in-memory cookie jar
cookie_jar = CookieJar()
opener = build_opener(HTTPCookieProcessor(cookie_jar))

# All requests made via this opener automatically store and send cookies!
opener.open("https://httpbin.org/cookies/set?session=xyz123")

# Inspect stored cookies
for cookie in cookie_jar:
    print(f"Cookie: {cookie.name} = {cookie.value}")
```

### Using HTTP Proxies

```python
from urllib.request import build_opener, ProxyHandler

proxy_handler = ProxyHandler({
    "http": "http://proxy.internal.corp:8080",
    "https": "https://proxy.internal.corp:8443",
})

proxy_opener = build_opener(proxy_handler)
# response = proxy_opener.open("https://example.org")
```
