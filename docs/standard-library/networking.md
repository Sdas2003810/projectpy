# Networking & Internet Protocols (`socket`, `http`, `urllib`)

Python was designed from the beginning as an internet-connected language. Its standard library includes modules for working with raw network sockets (TCP/UDP), HTTP clients and servers, URL parsing, SSL encryption, and email protocols.

---

## Low-Level Sockets with the `socket` Module

The `socket` module provides direct access to the operating system's BSD socket interface.

### Building a Basic TCP Echo Server

```python
import socket

SERVER_HOST = "127.0.0.1"
SERVER_PORT = 9000

# Create an IPv4 (AF_INET), TCP (SOCK_STREAM) socket
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server_socket:
    # Allow immediate reuse of the address after restart
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    
    server_socket.bind((SERVER_HOST, SERVER_PORT))
    server_socket.listen(5)
    print(f"Echo server listening on {SERVER_HOST}:{SERVER_PORT}...")

    conn, addr = server_socket.accept()
    with conn:
        print(f"Client connected from {addr}")
        while True:
            data = conn.recv(1024)
            if not data:
                break  # Client disconnected
            # Echo data back to client
            conn.sendall(data)
```

### Building a TCP Client

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client:
    client.connect(("127.0.0.1", 9000))
    client.sendall(b"Hello from ProjectPy!")
    response = client.recv(1024)
    print(f"Received from server: {response.decode('utf-8')}")
```

---

## HTTP Clients: `urllib.request` & `urllib.parse`

For making HTTP requests without third-party libraries (like `requests` or `httpx`), use the standard library `urllib` package:

### Making GET Requests with Custom Headers

```python
import urllib.request
import json

url = "https://api.github.com/zen"

# Add custom User-Agent (GitHub requires this)
headers = {
    "User-Agent": "ProjectPy-DocEngine/1.0",
    "Accept": "text/plain"
}

req = urllib.request.Request(url, headers=headers)

with urllib.request.urlopen(req, timeout=5) as response:
    status = response.status
    content = response.read().decode("utf-8")
    print(f"Status: {status}")
    print(f"Response: {content}")
```

### Making POST Requests with JSON Payload

```python
import urllib.request
import json

target_url = "https://httpbin.org/post"
payload = {"query": "python standard library", "limit": 10}

# Convert payload dictionary to encoded JSON bytes
data_bytes = json.dumps(payload).encode("utf-8")

req = urllib.request.Request(
    target_url,
    data=data_bytes,  # Passing data converts the request to POST
    headers={"Content-Type": "application/json"}
)

with urllib.request.urlopen(req) as resp:
    result = json.loads(resp.read().decode("utf-8"))
    print("Echoed JSON:", result["json"])
```

### URL Parsing with `urllib.parse`

Deconstruct and assemble URLs safely:

```python
from urllib.parse import urlsplit, parse_qs, urlencode

raw_url = "https://example.com/search?category=books&sort=price_asc#results"

parsed = urlsplit(raw_url)
print(parsed.scheme)    # "https"
print(parsed.netloc)    # "example.com"
print(parsed.path)      # "/search"
print(parsed.query)     # "category=books&sort=price_asc"

# Parse query string into dictionary
query_params = parse_qs(parsed.query)
print(query_params)     # {'category': ['books'], 'sort': ['price_asc']}

# Encode dictionary back into query string
new_params = {"q": "python docs", "page": 2}
encoded_query = urlencode(new_params)
print(encoded_query)    # "q=python+docs&page=2"
```

---

## Instant HTTP Server with `http.server`

Python includes a built-in static web server. You can launch it directly from the command line to serve the current directory:

```bash
# Serves current directory on port 8000
python -m http.server 8000
```

### Custom HTTP Server in Code

You can also subclass `BaseHTTPRequestHandler` to create lightweight web services:

```python
from http.server import HTTPServer, BaseHTTPRequestHandler
import json

class SimpleAPIHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path == "/api/health":
            self.send_response(200)
            self.send_header("Content-Type", "application/json")
            self.end_headers()
            response = {"status": "healthy", "service": "projectpy"}
            self.wfile.write(json.dumps(response).encode("utf-8"))
        else:
            self.send_error(404, "Not Found")

def run(port=8080):
    server = HTTPServer(("127.0.0.1", port), SimpleAPIHandler)
    print(f"API server running on http://127.0.0.1:{port}/api/health")
    server.serve_forever()

if __name__ == "__main__":
    # run()
    pass
```

---

## Sending Email with `smtplib` and `email`

```python
import smtplib
from email.message import EmailMessage

msg = EmailMessage()
msg.set_content("Hello from Python! Your build finished successfully.")
msg["Subject"] = "Build Status: SUCCESS"
msg["From"] = "ci-bot@example.com"
msg["To"] = "developer@example.com"

# In production, use SSL context and real credentials:
# with smtplib.SMTP_SSL("smtp.example.com", 465) as smtp:
#     smtp.login("user", "password")
#     smtp.send_message(msg)
```

---

## Standard Networking Modules Summary

| Module | Purpose |
| :--- | :--- |
| `socket` | Low-level TCP, UDP, Unix domain sockets |
| `ssl` | TLS/SSL encryption wrappers for sockets |
| `urllib.request` | High-level HTTP/FTP client |
| `urllib.parse` | URL decomposition, encoding, and parameter manipulation |
| `http.server` | Lightweight static and dynamic HTTP web servers |
| `http.client` | Low-level HTTP 1.1 protocol client |
| `smtplib` | SMTP protocol client for outgoing email |
| `email` | RFC 5322 MIME message formatting and parsing |
