# Webserv

## 📝 Project Overview

**Webserv** is a custom HTTP/1.1 server written in **C++98**, built as part of the 42 School curriculum. It supports non-blocking I/O, full request parsing, static file serving, CGI execution, routing via a flexible configuration file, and more.

---

## 🎯 Features

- ✅ **Non-Blocking I/O** using `poll()` (or `epoll` / `kqueue`)  
- ✅ **HTTP/1.1 Compliance**: persistent connections, chunked transfer, correct status codes  
- ✅ **Config File** (NGINX-like syntax) for vhosts, ports, error pages, limits, routes  
- ✅ **Static Content** (HTML, CSS, JS, images, etc.)  
- ✅ **CGI Support** (PHP, Python, etc.)  
- ✅ **File Uploads** with `multipart/form-data` handling  
- ✅ **Routing**: per-location `root`/`alias`, methods, autoindex, index files, redirects  
- ✅ **Methods**: `GET`, `POST`, `DELETE`  
- ✅ **Error Handling**: default & custom 4xx/5xx pages  
- ✅ **Graceful Shutdown** on `SIGINT` / `SIGQUIT`  
- ✅ **Virtual Hosts** & multi-port support  

---

## 🚀 Getting Started

### Prerequisites

- **C++98** compiler (e.g. `g++`)  
- **make**  
- UNIX-like environment (Linux/macOS)

### Installation

```bash
# Clone
git clone git@github.com:Zoroki110/Webserv.git
cd Webserv

# Build
make
```

### Usage

```bash
# Run with default config
./webserv

# Or specify a config file
./webserv configs/example_site.conf
```
Open your browser at http://<host>:<port> (default: 127.0.0.1:8080)

## 🔧 Configuration

Configurations live under configs/ and follow an NGINX-like syntax:

```nginx
server {
    listen 127.0.0.1:8080;
    server_name localhost;

    error_page 404 /errors/404.html;
    client_max_body_size 5M;

    location / {
        root ./public;
        index index.html;
        methods GET DELETE;
        autoindex on;
    }

    location /upload {
        root ./uploads;
        methods POST;
        upload_path ./uploads;
    }

    location /php/ {
        root ./www;
        cgi_pass /usr/bin/php-cgi;
        methods GET POST;
    }
}
```
- listen: address and port
- server_name: domain(s)
- error_page: custom error page path
- client_max_body_size: limit on request body
- location: route block with:
    - root / alias
    - methods (GET|POST|DELETE)
    - autoindex (on|off)
    - index default file
    - redirect URL
    - cgi_pass executable
    - upload_path for POST

## 🏗️ Project Structure

```
webserv/
    │── src/
    │   ├── main.cpp
    │   ├── Server.cpp
    │   ├── Request.cpp
    │   ├── Response.cpp
    │   ├── Router.cpp
    │   ├── CGIHandler.cpp
    │   ├── ConfigParser.cpp
    │   └── utils/
    │       ├── Socket.cpp
    │       ├── Timer.cpp
    │       └── Logger.cpp
    │── includes/
    │   ├── webserv.hpp
    │   └── Config.hpp
    │── configs/
    │   ├── default.conf
    │   └── example_site.conf
    │── Makefile
    │── README.md
```

## 🔧 Features in Detail

### 🔹 Non-Blocking I/O & Event Loop

All sockets run in non-blocking mode and are managed by a single `poll()`/`epoll`/`kqueue` loop, ensuring high concurrency without blocking.

### 🔹 HTTP/1.1 Parsing & Compliance

Parses request lines, headers, chunked transfer bodies; generates correct status lines, headers, and responses (with chunked or Content-Length).

### 🔹 Static File Serving

Translates URIs to filesystem paths, checks permissions, serves files (including large streaming) without blocking the main loop.

### 🔹 CGI Execution

Forks and `execve()` external scripts, setting up CGI environment (`REQUEST_METHOD`, `PATH_INFO`, `QUERY_STRING`, etc.), streams script output back to client.

### 🔹 File Uploads & Body Limits

Handles `multipart/form-data`, saves uploaded files to a configurable directory, enforces `client_max_body_size`, and returns `413 Payload Too Large` when exceeded.

### 🔹 Routing & Virtual Hosts

Supports multiple `server` blocks with `server_name` matching and per-location directives (`root`/`alias`, `index`, `redirect`, `autoindex`, allowed methods, `cgi_pass`, `upload_path`).

### 🔹 Error Handling

Provides default HTML pages for all 4xx/5xx errors, with ability to override via the `error_page` directive.

### 🔹 Graceful Shutdown

Catches `SIGINT`/`SIGQUIT` to close all connections cleanly, free resources, and exit without crashes.

## 🏆 Acknowledgments
This project was completed as part of the **42 School** curriculum.
</br>
Special thanks to [Simon](https://github.com/Simonnawara) and [Ismael](https://github.com/ismaeljda) for this group project.



✨ Happy HTTP Serving! 🚀