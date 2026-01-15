# Libs - Reusable Function Libraries

## What You'll Learn

- Create modular, well-organized function libraries
- Design functions for reusability across projects
- Implement common DevOps patterns as libraries
- Build a personal DevOps toolkit
- Share and version libraries effectively
- Test and document library functions
- Design clean APIs for functions

## Prerequisites

- Bash scripting and functions (modules 01-02)
- Understanding of script organization (scripts folder)
- Familiarity with DevOps tasks (modules 03-14)
- Basic understanding of code organization

## Key Concepts

### Library Design
- Single responsibility per library
- Consistent naming conventions
- Clear function interfaces
- Comprehensive documentation
- Error handling and validation

### Common Libraries
- Cloud operations (AWS, GCP, Azure)
- Kubernetes utilities
- Database operations
- Container management
- System administration

### Best Practices
- Namespace functions to avoid conflicts
- Provide sensible defaults
- Validate all inputs
- Log operations clearly
- Make libraries testable

## Hands-on Lab: Create Reusable Library

### Step 1: Design Library Structure
```bash
cat > libs/aws.sh << 'EOF'
#!/bin/bash
# AWS utility library
# Source this library: source ./libs/aws.sh

aws_init() {
  command -v aws >/dev/null 2>&1 || { echo "AWS CLI not found"; return 1; }
  echo "AWS library initialized"
}

aws_list_instances() {
  local region=${1:-us-east-1}
  aws ec2 describe-instances --region $region --query 'Reservations[].Instances[].[InstanceId,State.Name,InstanceType]' --output table
}
EOF
chmod +x libs/aws.sh
```

**Expected Output:**
```
AWS library initialized
```

### Step 2: Test Library Functions
```bash
source ./libs/aws.sh
aws_init
```

### Step 3: Create Kubernetes Library
```bash
cat > libs/kubernetes.sh << 'EOF'
#!/bin/bash
# Kubernetes utility library

k8s_get_nodes() {
  kubectl get nodes -o wide
}

k8s_pod_count() {
  local namespace=${1:-default}
  kubectl get pods -n $namespace --no-headers | wc -l
}
EOF
chmod +x libs/kubernetes.sh
```

### Step 4: Import Multiple Libraries
```bash
source ./libs/aws.sh
source ./libs/kubernetes.sh
aws_list_instances
k8s_pod_count
```

### Step 5: Verify Library Loading
```bash
# Test that functions are available
type aws_list_instances && echo "✓ AWS functions loaded"
type k8s_pod_count && echo "✓ K8s functions loaded"
```

## Validation

- [ ] Libraries source without errors
- [ ] Functions are accessible
- [ ] Functions execute correctly
- [ ] Multiple libraries can coexist
- [ ] No naming conflicts
- [ ] Error handling works

## Cleanup

```bash
# Keep libraries, they're reusable
```

## Common Mistakes

- Naming conflicts between libraries
- Functions depending on external state
- Poor documentation
- Not validating function inputs
- Hardcoded values instead of parameters
- Ignoring error cases

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Function name conflict | Use library prefix (aws_*, k8s_*) |
| Variables not exported | Use `export VAR=value` for globals |
| Library dependency missing | Add checks in library init |
| Hard to test | Design testable functions |
| Documentation missing | Add help/usage comments |

## Next Steps

- Create comprehensive library test suite
- Publish libraries to git/package repo
- Version libraries properly
- Create library documentation
- Build composite libraries from smaller ones
