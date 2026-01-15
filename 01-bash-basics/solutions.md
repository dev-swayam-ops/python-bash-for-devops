# Bash Basics: Solutions

## Solution 1: Simple Echo Script

```bash
#!/bin/bash
# simple_echo.sh

echo "Shara"
echo "DevOps Engineer"
echo "$(date)"

# Output example:
# Shara
# DevOps Engineer
# Wed Jan 15 10:30:45 EST 2026
```

**Explanation**: `echo` prints text. `$(date)` runs the date command and inserts output.

---

## Solution 2: Variable Assignment and Usage

```bash
#!/bin/bash
# variable_loop.sh

tool="Docker"

echo "My favorite tool is: $tool"
echo "Tool name: $tool"
echo "Learning: $tool"
echo "Using: $tool"
echo "Deploying with: $tool"

# Output:
# My favorite tool is: Docker
# Tool name: Docker
# (repeats pattern)
```

**Explanation**: Variable is assigned once and reused throughout. Double quotes allow variable expansion.

---

## Solution 3: Basic Arithmetic

```bash
#!/bin/bash
# arithmetic.sh

sum=$((10 + 5))
diff=$((20 - 7))
product=$((3 * 4))

echo "Sum: $sum"
echo "Difference: $diff"
echo "Product: $product"

# Output:
# Sum: 15
# Difference: 13
# Product: 12
```

**Explanation**: `$(( ))` syntax evaluates mathematical expressions. Works with all basic operators: `+`, `-`, `*`, `/`, `%`.

---

## Solution 4: Conditional Check

```bash
#!/bin/bash
# check_number.sh

num=15

if [ $num -gt 10 ]; then
    echo "$num is greater than 10"
else
    echo "$num is not greater than 10"
fi

# Output:
# 15 is greater than 10
```

**Explanation**: `-gt` means "greater than". Other operators: `-lt` (less than), `-eq` (equal), `-ne` (not equal).

---

## Solution 5: User Input Script

```bash
#!/bin/bash
# user_input.sh

echo "Enter your name:"
read name

echo "Enter your age:"
read age

echo "Hello, $name! You are $age years old."

# Output example (interactive):
# Enter your name:
# John
# Enter your age:
# 30
# Hello, John! You are 30 years old.
```

**Explanation**: `read` pauses script and waits for user input. Input is stored in the specified variable.

---

## Solution 6: File Existence Checker

```bash
#!/bin/bash
# file_checker.sh

file="config.txt"

if [ -f "$file" ]; then
    echo "File exists. Content:"
    cat "$file"
else
    echo "File does not exist. Creating it..."
    echo "Default configuration content" > "$file"
    echo "File created: $file"
fi
```

**Explanation**: `-f` tests if file exists. `>` redirects output to file. Use `cat` to display file contents.

---

## Solution 7: Loop Through Files

```bash
#!/bin/bash
# list_scripts.sh

echo "Shell scripts in current directory:"
for file in *.sh; do
    if [ -f "$file" ]; then
        echo "- $file"
    fi
done

# Output example:
# Shell scripts in current directory:
# - script1.sh
# - script2.sh
```

**Explanation**: `*.sh` glob pattern matches all files ending in `.sh`. Loop iterates through each match.

---

## Solution 8: Function with Parameters

```bash
#!/bin/bash
# sum_function.sh

sum_numbers() {
    local num1=$1
    local num2=$2
    local result=$((num1 + num2))
    echo $result
}

result1=$(sum_numbers 5 10)
result2=$(sum_numbers 20 30)

echo "5 + 10 = $result1"
echo "20 + 30 = $result2"

# Output:
# 5 + 10 = 15
# 20 + 30 = 50
```

**Explanation**: `$1` and `$2` access function parameters. `local` makes variables function-scoped. Command substitution `$()` captures return value.

---

## Solution 9: Array Usage

```bash
#!/bin/bash
# array_tools.sh

tools=("Git" "Docker" "Kubernetes" "Jenkins" "Terraform")

echo "DevOps Tools:"
for tool in "${tools[@]}"; do
    echo "  - $tool"
done

# Output:
# DevOps Tools:
#   - Git
#   - Docker
#   - Kubernetes
#   - Jenkins
#   - Terraform
```

**Explanation**: Arrays use parentheses syntax. `"${array[@]}"` iterates all elements. `${array[0]}` accesses individual elements by index.

---

## Solution 10: Case Statement

```bash
#!/bin/bash
# tool_description.sh

tool=$1

case "$tool" in
    docker)
        echo "Docker: Container platform for application deployment"
        ;;
    git)
        echo "Git: Version control system for tracking code changes"
        ;;
    python)
        echo "Python: High-level programming language for automation"
        ;;
    *)
        echo "Unknown tool: $tool"
        ;;
esac

# Usage: ./tool_description.sh docker
# Output:
# Docker: Container platform for application deployment
```

**Explanation**: `case` statement matches different patterns. `*)` is default case. `;;` terminates each case. `$1` is the first argument passed to script.
