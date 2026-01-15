# Bash Advanced Scripting: Exercises

## Exercise 1 (Easy): Exit Code Validation
Create a script that runs a command and checks its exit code. Print different messages based on success ($? == 0) or failure.

**Hint**: Save `$?` immediately after command execution

---

## Exercise 2 (Easy): Configuration File Parser
Create a config file with KEY=VALUE pairs, then write a script that reads it and displays the values in a formatted way.

**Hint**: Use `source` or read file line by line with loops

---

## Exercise 3 (Easy): Basic Text Processing
Create a log file with multiple lines. Use `grep` to find lines containing "ERROR" and count them.

**Hint**: Use `grep -c` to count matching lines

---

## Exercise 4 (Easy): Simple awk Usage
Create a CSV file with names and ages. Use `awk` to print only people older than 25.

**Hint**: Use `awk -F',' '$2 > 25'` for field separator and comparison

---

## Exercise 5 (Medium): Regex Validation Script
Create a script that validates user email input using regex pattern matching and reports if it's valid or not.

**Hint**: Use regex pattern like `[a-zA-Z0-9]+@[a-zA-Z0-9]+\.[a-z]+`

---

## Exercise 6 (Medium): Log File Analyzer
Write a script that reads a log file and extracts statistics (total lines, errors, warnings) and writes report to a new file.

**Hint**: Use `grep`, `wc`, and pipe results to output file with `>`

---

## Exercise 7 (Medium): Backup with Timestamps
Create a script that backs up a directory with a timestamped filename, validates the backup, and logs the operation.

**Hint**: Use `tar`, `date +%Y%m%d_%H%M%S` for timestamp, and error handling

---

## Exercise 8 (Medium): sed for Text Replacement
Write a script that takes a template file (like app.conf.template) and replaces placeholders like {{APP_NAME}} with actual values.

**Hint**: Use `sed 's/{{PATTERN}}/value/g'` for replacement

---

## Exercise 9 (Medium): Retry Logic
Create a script that attempts a failing command multiple times before giving up. Use exit codes to determine retry necessity.

**Hint**: Use a loop with counter and `break` on success

---

## Exercise 10 (Medium): Parallel Job Processing
Write a script that processes multiple files in parallel using background jobs. Wait for all to complete and report status.

**Hint**: Use `command &` for background, `wait` to complete, and `$!` to get process ID
