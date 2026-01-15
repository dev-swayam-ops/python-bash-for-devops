# Python Basics for DevOps: Exercises

## Exercise 1 (Easy): Print Server Information
Create a script that stores server details (name, IP, port) in variables and prints them in a formatted way.

**Hint**: Use f-strings for formatting: `f"Server: {name}"`

---

## Exercise 2 (Easy): List Operations
Create a script with a list of tools (git, docker, kubernetes) and:
- Print the list
- Add a new tool
- Print the updated list

**Hint**: Use `.append()` method for lists

---

## Exercise 3 (Easy): Dictionary Access
Create a dictionary with server config (host, port, timeout) and print each value.

**Hint**: Use `dict[key]` or `dict.get(key)` to access values

---

## Exercise 4 (Easy): Conditional Logic
Write a script that checks if a port number is valid (1-65535) and prints appropriate message.

**Hint**: Use `if` and `else` statements

---

## Exercise 5 (Medium): Loop Through Dictionary
Create a script with a dictionary of services (name: port) and loop through printing each one in a formatted way.

**Hint**: Use `for key, value in dict.items()`

---

## Exercise 6 (Medium): Function with Return Value
Write a function that calculates uptime percentage given up_seconds and total_seconds, then call it with test data.

**Hint**: Return calculated value and use the result in print

---

## Exercise 7 (Medium): Error Handling
Create a script that reads user input for a port number, validates it's an integer, and handles errors gracefully.

**Hint**: Use `try/except` for error handling and `int()` for conversion

---

## Exercise 8 (Medium): File Operations
Write a script that creates a config file, writes some server configurations to it, then reads and displays the content.

**Hint**: Use `open(file, 'w')` for writing and `open(file, 'r')` for reading

---

## Exercise 9 (Medium): List Comprehension
Create a list of numbers 1-10, use list comprehension to filter even numbers only.

**Hint**: Use `[x for x in range(1,11) if x % 2 == 0]`

---

## Exercise 10 (Medium): JSON Handling
Create a Python script that stores server configuration as a dictionary, converts it to JSON, saves to file, and reads it back.

**Hint**: Use `json.dumps()` and `json.loads()` with file operations
