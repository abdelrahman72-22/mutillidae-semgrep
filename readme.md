# Mutillidae Semgrep SAST Project

This repository contains my work for the **Static Application Security Testing (SAST)** assignment using **Semgrep** on the intentionally vulnerable OWASP Mutillidae II application.

The project includes:

- Custom Semgrep rules written in YAML  
- Semgrep scan results  
- Documentation & explanation  

---

## 📁 Repository Structure

- semgrep-rules/
     - dangerous-shell-exec.yml
     - sql-injection-concat.yml
     - README.md
-  src/ # Mutillidae application source code

 - docker-compose.yml

 

---

## 🛠️ Custom Semgrep Rules

### 🔹 1. dangerous-shell-exec.yml  
Detects **Command Injection** when user-controlled data reaches:

- `shell_exec()`
- `system()`
- `exec()`
- Backtick execution

**Why dangerous?**  
User input inside OS commands allows remote code execution.

---

### 🔹 2. sql-injection-concat.yml  
Detects **SQL Injection** patterns created by unsafe string concatenation such as:

```php
$query = "SELECT * FROM accounts WHERE username='" . $_POST['username'] . "'";

docker run --rm -v "${PWD}:/src" returntocorp/semgrep \
  semgrep -c /src/semgrep-rules /src/src --verbose

```
---
## 🔥 Command Injection Findings (14)

The Semgrep scan identified **14 instances** of potential Command Injection vulnerabilities.  
These occur when user-controlled input reaches OS command execution functions such as  
`shell_exec()`, `exec()`, `system()`, or backticks.

### 📌 Files Affected

- **content-security-policy.php**  
- **database-offline.php**  
- **dns-lookup.php**  
- **echo.php**  
- **REST endpoints**
  - `/webservices/rest/ws-dns-lookup.php`
  - `/webservices/rest/ws-echo.php`
  - `/webservices/rest/ws-login.php`
  - `/webservices/rest/ws-cors-echo.php`
- **SOAP endpoints**
  - `/webservices/soap/ws-dns-lookup.php`
  - `/webservices/soap/ws-echo.php`
  - `/webservices/soap/ws-login.php`

### 🧨 Risk
---
## 🛑 SQL Injection Findings (119)

The Semgrep scan detected **119 SQL Injection vulnerabilities** across multiple PHP files.  
These occur where user-controlled input is concatenated directly into SQL queries without  
parameterization or proper sanitization.

### 📌 Files Affected

SQL Injection risks were found across large parts of the application, including:

- **ajax**
  - `lookup-pen-test-tool.php`

- **classes**
  - `LogHandler.php`
  - `MySQLHandler.php`
  - `SQLQueryHandler.php`
  - `ClientInformationHandler.php`
  - `CustomErrorHandler.php`
  - `RequiredSoftwareHandler.php`
  - `XMLHandler.php`
  - `YouTubeVideoHandler.php`

- **root pages**
  - `client-side-control-challenge.php`
  - `conference-room-lookup.php`
  - `index.php`
  - `set-up-database.php`
  - `includes/header.php`
  - `includes/footer.php`
  - `includes/constants.php`
  - `includes/process-login-attempt.php`

- **test & lookup pages**
  - `test-connectivity.php`
  - `view-user-privilege-level.php`
  - `pen-test-tool-lookup.php`

- **REST services**
  - `ws-echo.php`
  - `ws-cors-echo.php`
  - `ws-dns-lookup.php`
  - `ws-login.php`
  - `ws-authenticate-jwt-token.php`

- **SOAP services**
  - `ws-dns-lookup.php`
  - `ws-echo.php`
  - `ws-login.php`
  - `lib/nusoap.php`

### 🧨 Risk

SQL Injection allows an attacker to:

- Extract sensitive data (user credentials, logs, credit card data…)
- Bypass authentication (`' OR '1'='1`)
- Modify or delete database contents
- Gain remote access through stacked queries (if enabled)
- Fully compromise the entire database server

### ⚠️ Common Vulnerable Pattern

```php
$query = "SELECT * FROM accounts WHERE username='" . $username . "'";
$result = $db->executeQuery($query);


Command injection allows an attacker to execute arbitrary commands on the server,  
leading to **remote code execution (RCE)**, full system compromise, and data leakage.


