# Bash Basics for DevOps

## What You'll Learn
- Writing your first bash script
- Using variables and data types
- Working with conditionals (if/else)
- Loops (for, while)
- Functions and script organization
- Input/output redirection
- Debugging bash scripts

## Prerequisites
- Bash shell installed (Linux/macOS default)
- Text editor (VS Code, nano, vim)
- Completed: [00-setup-and-tooling](../00-setup-and-tooling)
- Basic command line knowledge

## Key Concepts
- **Shebang**: `#!/bin/bash` - tells system to use bash interpreter
- **Variables**: Store data with `VAR=value` (no spaces around `=`)
- **Conditionals**: `if-then-else` for decision making
- **Loops**: `for` and `while` for repetition
- **Functions**: Reusable blocks of code
- **Quoting**: Single quotes `'` (literal), double quotes `"` (allows expansion)

## Hands-on Lab: Your First Bash Script

### Step 1: Create Your First Script
```bash
# Create a file named hello.sh
cat > hello.sh << 'EOF'
#!/bin/bash
echo "Hello, DevOps!"
EOF

# Make it executable
chmod +x hello.sh

# Run the script
./hello.sh

# Expected output:
# Hello, DevOps!
```

### Step 2: Working with Variables
```bash
# Create variables_demo.sh
cat > variables_demo.sh << 'EOF'
#!/bin/bash

# Define variables
name="DevOps Engineer"
age=30
project="Automation"

# Print variables
echo "Name: $name"
echo "Age: $age"
echo "Project: $project"

# Expected output:
# Name: DevOps Engineer
# Age: 30
# Project: Automation
EOF

chmod +x variables_demo.sh
./variables_demo.sh
```

### Step 3: Using Conditionals
```bash
# Create check_file.sh
cat > check_file.sh << 'EOF'
#!/bin/bash

file="test.txt"

if [ -f "$file" ]; then
    echo "File exists!"
else
    echo "File does not exist"
fi

# Expected output:
# File does not exist (until you create test.txt)
EOF

chmod +x check_file.sh
./check_file.sh
```

### Step 4: Loops
```bash
# Create loop_demo.sh
cat > loop_demo.sh << 'EOF'
#!/bin/bash

echo "Counting 1 to 3:"
for i in 1 2 3; do
    echo "Number: $i"
done

# Expected output:
# Number: 1
# Number: 2
# Number: 3
EOF

chmod +x loop_demo.sh
./loop_demo.sh
```

### Step 5: Functions
```bash
# Create function_demo.sh
cat > function_demo.sh << 'EOF'
#!/bin/bash

greet() {
    echo "Hello, $1!"
}

greet "DevOps Team"

# Expected output:
# Hello, DevOps Team!
EOF

chmod +x function_demo.sh
./function_demo.sh
```

## Validation Checklist
- [ ] Created and executed hello.sh
- [ ] Understood variable assignment and usage
- [ ] Used `if` statement correctly
- [ ] Used `for` loop successfully
- [ ] Called a function with parameters

## Cleanup
```bash
# Remove all scripts
rm -f hello.sh variables_demo.sh check_file.sh loop_demo.sh function_demo.sh test.txt
```

## Common Mistakes
1. **Spaces around `=` in assignment**: `VAR = value` (wrong) vs `VAR=value` (correct)
2. **Missing quotes around variables**: `$VAR` should be `"$VAR"` in conditionals
3. **Wrong shebang**: Using `#!/bin/sh` instead of `#!/bin/bash` loses bash features
4. **Missing `chmod +x`**: Script won't execute without proper permissions
5. **Forgetting `./` prefix**: Run `./script.sh` not `script.sh`

## Troubleshooting

### "Permission denied" when running script
- **Cause**: Script doesn't have execute permission
- **Solution**: Run `chmod +x script.sh`

### "Command not found" error
- **Cause**: Bash not found at specified location
- **Solution**: Verify shebang with `which bash` (usually `/bin/bash`)

### Variables not expanding
- **Cause**: Using single quotes `'$VAR'` instead of double quotes
- **Solution**: Use `"$VAR"` for variable expansion

### Syntax errors in conditionals
- **Cause**: Missing spaces in `[ condition ]`
- **Solution**: Always use spaces: `[ -f file ]` not `[-f file]`

## Next Steps
1. Practice with [exercises](exercises.md)
2. Study [bash cheatsheet](cheatsheet.md) for common commands
3. Move to [02-bash-advanced-scripting](../02-bash-advanced-scripting)
