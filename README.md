# Python and Bash for DevOps - Complete Learning Curriculum

A comprehensive, hands-on learning path from DevOps fundamentals to real-world project implementation. This curriculum combines bash scripting, Python automation, and practical DevOps tools into a structured, progressive learning journey.

## 📚 Curriculum Overview

This repository contains **68 files across 18 folders** organized as a complete DevOps training program:

- **4 files per module:** README.md, exercises.md, solutions.md, cheatsheet.md
- **Progressive difficulty:** Foundation → Operations → Deployment → Advanced → Real-world
- **Hands-on labs:** Every module includes practical examples with expected output
- **10 exercises per module:** Each with step-by-step solutions
- **Complete reference:** Cheatsheets for quick command lookup

---

## 🎯 Learning Path

### **Phase 1: Foundations (Modules 00-08)**
Build core DevOps knowledge with bash, Python, and Linux basics.

| Module | Topic | Key Learning |
|--------|-------|--------------|
| **00** | Setup & Tooling | Environment setup, essential tools |
| **01** | Bash Basics | Variables, loops, functions, scripting fundamentals |
| **02** | Advanced Bash | Arrays, string manipulation, pattern matching |
| **03** | Python Basics for DevOps | Syntax, libraries, file operations, automation |
| **04** | Python Advanced | Classes, APIs, data processing, automation patterns |
| **05** | CLI Tools & Argparse | Command-line applications, argument parsing |
| **06** | Linux Automation | System administration, user management, scheduling |
| **07** | Log Analysis & Parsing | Log processing, pattern matching, troubleshooting |
| **08** | Monitoring & Alerting | System monitoring, metrics collection, alerting |

**What you'll build:** Bash scripts, Python automation tools, system administration scripts

---

### **Phase 2: Operations & Infrastructure (Modules 09-11)**
Master DevOps operations and infrastructure automation.

| Module | Topic | Key Learning |
|--------|-------|--------------|
| **09** | Backup & Restore | Backup strategies, encryption, disaster recovery |
| **10** | Cloud Automation (AWS) | EC2, S3, RDS, CloudWatch, infrastructure management |
| **11** | Kubernetes Automation | Container orchestration, deployments, services, RBAC |

**What you'll build:** Automated backup systems, cloud infrastructure tools, K8s management scripts

---

### **Phase 3: Deployment & Pipeline (Module 12)**
Learn modern CI/CD practices and deployment automation.

| Module | Topic | Key Learning |
|--------|-------|--------------|
| **12** | CI/CD Helpers | GitHub Actions, pipelines, automated testing & deployment |

**What you'll build:** Complete CI/CD workflows, automated test & deploy pipelines

---

### **Phase 4: Security & Performance (Modules 13-14)**
Implement security, compliance, and performance optimization.

| Module | Topic | Key Learning |
|--------|-------|--------------|
| **13** | Security & Compliance | Auditing, compliance checks, vulnerability scanning |
| **14** | Performance & Troubleshooting | Monitoring, debugging, bottleneck analysis |

**What you'll build:** Security audit scripts, performance monitoring dashboards

---

### **Phase 5: Real-World Implementation (Module 15)**
Integrate everything into complete production systems.

| Module | Topic | Key Learning |
|--------|-------|--------------|
| **15** | Real-World DevOps Projects | Multi-environment deployments, incident response, GitOps |

**What you'll build:** End-to-end production systems with monitoring and rollback

---

### **Phase 6: Utilities (scripts & libs folders)**
Reusable tools and function libraries for daily DevOps work.

| Folder | Topic | Key Learning |
|--------|-------|--------------|
| **scripts/** | Common DevOps Utilities | Reusable scripts, command-line tools, utility functions |
| **libs/** | Function Libraries | AWS, Kubernetes, Database, Docker, Monitoring libraries |

**What you'll build:** Personal DevOps toolkit, shared function libraries

---

## 🚀 Quick Start

### Prerequisites
- **Bash 4.0+** - Check with `bash --version`
- **Linux/Unix environment** - Ubuntu, CentOS, macOS, or WSL
- **Text editor** - VS Code, vim, nano
- **Git** - For version control

### Getting Started

1. **Clone the repository:**
```bash
git clone <repo-url>
cd python-bash-for-devops
```

2. **Start with Module 00:**
```bash
cd 00-setup-and-tooling
cat README.md          # Read the overview
cat exercises.md       # See the exercises
cat solutions.md       # Study the solutions
cat cheatsheet.md      # Reference commands
```

3. **Follow the recommended learning order:**
   - Read README.md for concepts and hands-on lab
   - Attempt exercises in exercises.md
   - Check solutions.md if you get stuck
   - Use cheatsheet.md for quick command lookup

---

## 📖 Module Structure

Each module (00-15) contains exactly 4 files:

### **README.md** (Comprehensive Guide)
- What You'll Learn
- Prerequisites
- Key Concepts
- 1 Hands-on Lab (with commands + expected output)
- Validation Checklist
- Cleanup Instructions
- Common Mistakes
- Troubleshooting Tips
- Next Steps

### **exercises.md** (Progressive Challenges)
- 10 exercises per module
- Easy → Medium difficulty progression
- Clear requirements for each exercise
- Real-world scenarios

### **solutions.md** (Complete Implementations)
- Full solution code for each exercise
- Short explanations of approach
- Commands + output examples
- Best practices and tips

### **cheatsheet.md** (Quick Reference)
- Command reference tables (Command | Purpose | Example)
- Common patterns and syntax
- Troubleshooting tips
- Quick diagnostic scripts

---

## 🎓 How to Use This Curriculum

### **For Self-Study:**
1. Go through modules sequentially
2. Complete all exercises before moving to next module
3. Use cheatsheets for reference while coding
4. Run hands-on labs to understand concepts

### **For Classroom/Training:**
1. Use README.md as lecture material
2. Assign exercises as homework
3. Review solutions during class
4. Use cheatsheets as quick reference

### **For Reference:**
- Jump to specific module based on topic
- Use cheatsheets for quick command lookup
- Review solutions for implementation patterns
- Check troubleshooting section for common issues

---

## 💡 Learning Tips

### Best Practices
- **Type commands, don't copy-paste** - Builds muscle memory
- **Run hands-on labs first** - Understand concepts before exercises
- **Attempt exercises before solutions** - Learn by problem-solving
- **Create your own scripts** - Apply knowledge to your use cases
- **Build a toolkit** - Accumulate useful scripts in `/scripts` and `/libs`

### Progression Strategy
- **Week 1-2:** Modules 00-02 (bash fundamentals)
- **Week 3-4:** Modules 03-05 (Python and CLI tools)
- **Week 5-6:** Modules 06-08 (Linux and monitoring)
- **Week 7-10:** Modules 09-11 (cloud and containers)
- **Week 11:** Module 12 (CI/CD)
- **Week 12:** Modules 13-14 (security and performance)
- **Week 13-14:** Module 15 (real-world projects)

### Time Commitment
- **30 min:** Read README + do hands-on lab
- **60 min:** Complete 5-10 exercises
- **30 min:** Review solutions and cheatsheet
- **Total:** ~2 hours per module = 32-36 hours for full curriculum

---

## 📁 Repository Structure

```
python-bash-for-devops/
├── README.md (this file)
│
├── 00-setup-and-tooling/          # Foundations
├── 01-bash-basics/
├── 02-bash-advanced-scripting/
├── 03-python-basics-for-devops/
├── 04-python-advanced-for-automation/
├── 05-cli-tools-and-argparse/
├── 06-linux-automation-scripts/
├── 07-log-analysis-and-parsing/
├── 08-monitoring-and-alerting-scripts/
│
├── 09-backup-and-restore-automation/  # Operations
├── 10-cloud-automation-aws/
├── 11-kubernetes-automation/
│
├── 12-ci-cd-helpers/                  # Deployment
│
├── 13-security-and-compliance-scripts/  # Advanced
├── 14-performance-and-troubleshooting-tools/
│
├── 15-real-world-devops-projects/    # Integration
│
├── scripts/                            # Utilities
│   ├── README.md
│   ├── exercises.md
│   ├── solutions.md
│   └── cheatsheet.md
│
└── libs/                               # Function Libraries
    ├── README.md
    ├── exercises.md
    ├── solutions.md
    └── cheatsheet.md
```

Each module folder contains: **README.md**, **exercises.md**, **solutions.md**, **cheatsheet.md**

---

## 🎯 What You'll Learn

### Bash & Scripting
✅ Variables, loops, functions, arrays
✅ Pattern matching and regular expressions
✅ Error handling and debugging
✅ Script organization and best practices
✅ Reusable function libraries

### Python for DevOps
✅ Automation scripting with Python
✅ Working with APIs and cloud services
✅ Data processing and analysis
✅ Building command-line tools
✅ Integration with system tools

### Cloud & Infrastructure
✅ AWS EC2, S3, RDS, CloudWatch
✅ Kubernetes deployments and management
✅ Infrastructure as Code concepts
✅ Multi-environment deployments
✅ Disaster recovery and backups

### CI/CD & Deployment
✅ GitHub Actions workflows
✅ Automated testing and building
✅ Container image management
✅ Blue-green and canary deployments
✅ Automated rollback strategies

### Security & Operations
✅ Security auditing and compliance
✅ Vulnerability scanning
✅ Performance monitoring and tuning
✅ Log analysis and troubleshooting
✅ Incident response automation

### DevOps Tools & Utilities
✅ Build personal automation toolkit
✅ Create reusable function libraries
✅ Develop monitoring dashboards
✅ Master essential DevOps tools
✅ Automate daily operational tasks

---

## 🔍 Key Topics by Module

### **Bash Fundamentals (00-02)**
- Shell basics, variables, control flow
- Functions, arrays, string manipulation
- File operations, permissions, scheduling
- Pattern matching, regular expressions

### **Python Fundamentals (03-05)**
- Python syntax, data structures, functions
- Libraries: requests, json, subprocess, argparse
- File I/O, error handling, testing
- CLI tool development

### **System Administration (06-08)**
- User/group management, sudo, permissions
- Systemd, cron, process management
- Log parsing, monitoring, metrics collection
- Alerting and notifications

### **Cloud & DevOps Operations (09-14)**
- Backup strategies, encryption, disaster recovery
- AWS: EC2, S3, RDS, CloudWatch, VPC, IAM
- Kubernetes: Pods, Deployments, Services, RBAC
- CI/CD: GitHub Actions, automated testing/deployment
- Security: auditing, compliance, scanning
- Performance: monitoring, profiling, optimization

### **Real-World Integration (15)**
- Multi-environment deployments
- Blue-green/canary releases
- Monitoring and alerting integration
- Incident response automation
- Cost optimization

### **Development Tools (scripts, libs)**
- Logging utilities, health checks, config management
- AWS, Kubernetes, Database libraries
- Docker, monitoring, networking utilities
- Security and encryption functions

---

## ✨ Features

✅ **Beginner-Friendly** - Start from basics, progress to advanced topics
✅ **Hands-On Learning** - Every module has practical labs with expected output
✅ **Progressive Difficulty** - Easy → Medium exercises in each module
✅ **Complete Solutions** - All exercises have detailed solutions with explanations
✅ **Quick Reference** - Cheatsheets for commands and patterns
✅ **Real-World Scenarios** - Practical examples from actual DevOps work
✅ **Self-Paced** - Learn at your own speed, work through modules in order
✅ **No Prerequisites** - Assumes no prior DevOps experience
✅ **Reusable Code** - Build a toolkit of scripts and libraries

---

## 📈 Recommended Learning Path

### **Absolute Beginner:**
→ 00-02 → 03-05 → 06 → 07 → 08 → 09 → 10 → 11 → 12 → 13 → 14 → 15

### **With Bash Experience:**
→ 03-05 → 06 → 07 → 08 → 09 → 10 → 11 → 12 → 13 → 14 → 15

### **With Python Experience:**
→ 00-02 → 06 → 07 → 08 → 09 → 10 → 11 → 12 → 13 → 14 → 15

### **For Cloud DevOps Focus:**
→ 00-08 → 10 → 11 → 12 → 13 → 14 → 15

### **For Container/K8s Focus:**
→ 00-08 → 11 → 12 → 13 → 14 → 15

---

## 🛠️ Tools & Technologies Covered

**Languages:** Bash, Python, YAML, SQL, JSON

**Cloud:** AWS (EC2, S3, RDS, CloudWatch, VPC, IAM)

**Containers:** Docker, Kubernetes, Helm

**CI/CD:** GitHub Actions, ArgoCD concepts

**Monitoring:** Prometheus, Grafana, CloudWatch

**Databases:** MySQL, PostgreSQL, MongoDB basics

**Security:** GPG, OpenSSL, Vault concepts

**Additional:** Git, systemd, cron, rsync, sed/awk

---

## 📋 Checklist: What to Do First

- [ ] Clone this repository
- [ ] Verify bash version: `bash --version`
- [ ] Verify Python: `python3 --version`
- [ ] Read this README completely
- [ ] Start with **Module 00-setup-and-tooling**
- [ ] Work through Module 01 (Bash Basics)
- [ ] Build good habits (hands-on lab → exercises → solutions)
- [ ] Accumulate scripts in `/scripts` folder
- [ ] Create function libraries in `/libs` folder
- [ ] Track your progress through all 15 modules

---

## 🤝 Contributing

Found an issue? Have suggestions? Want to improve exercises or solutions?
- File an issue with details
- Submit pull requests with improvements
- Share your own automation scripts
- Suggest additional topics

---

## 📞 Support

### Getting Unstuck
1. Check the module's **Troubleshooting** section
2. Review the **cheatsheet.md** for command reference
3. Check solutions for similar problems
4. Run commands with `-v` or `--debug` flags
5. Use `bash -x script.sh` for debugging

### Common Issues
- **"command not found"** → Install the tool or check PATH
- **"permission denied"** → Use `chmod +x` or `sudo`
- **Script fails** → Add `set -x` to debug
- **Variables empty** → Check variable scope and exports

---

## 📝 License

Educational material for learning DevOps practices.

---

## 🎉 Getting Started Now

**Start learning right now:**

```bash
# Navigate to first module
cd 00-setup-and-tooling

# Read the overview
cat README.md

# Look at the exercises
cat exercises.md

# Run the hands-on lab
# (follow the README.md step-by-step)

# When stuck, check solutions
cat solutions.md

# Quick command reference
cat cheatsheet.md
```

---

**Happy Learning! 🚀**

This curriculum will take you from DevOps beginner to someone comfortable with production deployments, automation, and operational excellence.

**Estimated Time:** 32-36 hours (2 hours per module × 15 modules + utilities)
**Level:** Beginner → Intermediate
**Focus:** Practical, hands-on, real-world DevOps skills
