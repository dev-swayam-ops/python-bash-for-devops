# Log Analysis and Parsing: Exercises

## Exercise 1 (Easy): Count Errors in Log
Write a script that counts how many ERROR and WARNING messages are in a log file.

**Hint**: Use `grep -c` to count matches

---

## Exercise 2 (Easy): Filter by Time Range
Create a script that extracts all logs between 10:30 and 10:35 from a log file.

**Hint**: Use `grep` with time pattern like `10:3[0-5]`

---

## Exercise 3 (Easy): Extract Specific Columns
Write a script that extracts timestamp and message from logs with format: `TIME LEVEL MESSAGE`.

**Hint**: Use `awk '{print $1, $NF}'` or similar

---

## Exercise 4 (Easy): Find Critical Errors
Create a script that shows only CRITICAL and ERROR level logs with line numbers.

**Hint**: Use `grep -n` for line numbers with pattern

---

## Exercise 5 (Medium): Log Statistics Report
Write a script that generates a report: total lines, error count, warning count, unique IPs (if present).

**Hint**: Combine `wc`, `grep`, `awk`, `sort`, `uniq`

---

## Exercise 6 (Medium): Parse Apache Access Logs
Create a script that extracts top 5 most requested URLs from Apache access logs.

**Hint**: Extract 7th field (URL), sort by count

---

## Exercise 7 (Medium): JSON Log Parser
Write a Python script that reads JSON-formatted logs and extracts severity, timestamp, and message fields.

**Hint**: Use `json.loads()` or `json.JSONDecoder`

---

## Exercise 8 (Medium): Log Comparison
Create a script that compares two log files and shows differences (errors in new but not in old).

**Hint**: Use `comm` or `diff` command

---

## Exercise 9 (Medium): Real-time Log Tail with Filter
Write a script that tails a log file and highlights ERROR lines in real-time.

**Hint**: Use `tail -f` with `grep --line-buffered` or similar

---

## Exercise 10 (Medium): Log Rotation and Archive
Create a script that rotates logs (rename with date), compresses old ones, keeps last 7 days.

**Hint**: Check file age with `find -mtime`, use `tar` or `gzip`
