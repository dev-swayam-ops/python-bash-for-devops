# Bash Basics: Exercises

## Exercise 1 (Easy): Create a Simple Echo Script
Create a bash script that echoes three different lines:
- Your name
- Your role (DevOps Engineer)
- Current date

**Hint**: Use `echo` and `date` command

---

## Exercise 2 (Easy): Variable Assignment and Usage
Create a script that stores your favorite tool name in a variable and prints it 5 times in different formats.

**Hint**: Use loop or echo multiple times with the variable

---

## Exercise 3 (Easy): Basic Arithmetic
Create a script that calculates and prints:
- Sum of 10 + 5
- Difference of 20 - 7
- Product of 3 * 4

**Hint**: Use `$(( ))` syntax or `expr`

---

## Exercise 4 (Easy): Conditional Check
Create a script that checks if a number is greater than 10. Use a variable to store the number and print appropriate message.

**Hint**: Use `if [ $num -gt 10 ]`

---

## Exercise 5 (Medium): User Input Script
Create a script that asks user for their name and age, then prints a personalized message with those values.

**Hint**: Use `read` command for user input

---

## Exercise 6 (Medium): File Existence Checker
Create a script that checks if a specific file exists. If it does, print its content; if not, create it with some default text.

**Hint**: Use `[ -f file ]` condition and `cat` or `echo >`

---

## Exercise 7 (Medium): Loop Through Files
Create a script that loops through all `.sh` files in the current directory and prints their names.

**Hint**: Use `for file in *.sh` loop

---

## Exercise 8 (Medium): Function with Parameters
Create a script with a function that takes two numbers as parameters and returns their sum. Call it with different values.

**Hint**: Parameters are accessed as `$1`, `$2` inside functions

---

## Exercise 9 (Medium): Array Usage
Create a script that stores names of 5 DevOps tools in an array, then loops through and prints each one.

**Hint**: Use array syntax `TOOLS=("Git" "Docker" "Kubernetes")`

---

## Exercise 10 (Medium): Case Statement
Create a script that takes an argument (like a tool name) and prints a description based on what it is (Docker/Git/Python).

**Hint**: Use `case "$1" in` statement
