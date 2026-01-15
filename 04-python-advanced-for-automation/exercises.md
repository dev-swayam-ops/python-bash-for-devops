# Python Advanced for Automation: Exercises

## Exercise 1 (Easy): Run System Command
Create a script that uses subprocess to run `ls -la` (or `dir` on Windows) and prints the output.

**Hint**: Use `subprocess.run()` with `capture_output=True` and `text=True`

---

## Exercise 2 (Easy): Environment Variable Access
Write a script that reads environment variables (like PATH, HOME) and prints them in a formatted table.

**Hint**: Use `os.environ.get()` or `os.getenv()`

---

## Exercise 3 (Easy): Basic Regex Search
Create a script that searches for email addresses in a sample text using regex pattern.

**Hint**: Use `re.findall()` with pattern `r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'`

---

## Exercise 4 (Easy): Working with Classes
Create a simple Server class with properties (name, ip, status) and methods to start/stop the server.

**Hint**: Use `__init__()` for initialization and `self` for instance variables

---

## Exercise 5 (Medium): API Request
Write a script that makes an HTTP GET request to a public API (like JSONPlaceholder) and parses JSON response.

**Hint**: Use `requests.get()` and `response.json()`

---

## Exercise 6 (Medium): Error Handling with Logging
Create a script that processes a file line by line, logs errors when parsing fails, and continues with next line.

**Hint**: Use `logging` module and `try/except` blocks

---

## Exercise 7 (Medium): Complex Regex Replacement
Write a script that reads a log file and uses regex to redact sensitive info (like IP addresses or timestamps).

**Hint**: Use `re.sub()` with pattern and replacement string

---

## Exercise 8 (Medium): Configuration from YAML/JSON
Create a script that loads configuration from YAML/JSON file and validates required fields are present.

**Hint**: Use `yaml.safe_load()` or `json.load()` with try/except

---

## Exercise 9 (Medium): Database or File-based State
Write a script that tracks state (like last_run time) in a JSON file to avoid duplicate processing.

**Hint**: Store state data, check before processing, update after success

---

## Exercise 10 (Medium): Multi-step Automation Script
Create a complete automation workflow: fetch config, validate, run commands, log results, and handle errors gracefully.

**Hint**: Combine classes, subprocess, logging, and error handling
