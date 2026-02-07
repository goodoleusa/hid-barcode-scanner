# Contributing to the BLE Security Learning Wiki

Thank you for your interest in improving this educational resource! This guide will help you contribute effectively.

## Types of Contributions Welcome

### 📝 Content Improvements
- Fixing typos and grammatical errors
- Clarifying confusing explanations
- Adding missing information
- Updating outdated content
- Improving code examples

### 🎓 New Educational Content
- Additional exercises
- Case studies
- Real-world examples
- Video tutorials (links)
- Translations to other languages

### 🔧 Technical Updates
- Updated tool instructions
- New security techniques
- Current vulnerability information
- Tool version updates

### 🛠️ Code Examples
- Better example scripts
- Additional payloads (for authorized testing only)
- Improved detection scripts
- Defensive tools

## Contribution Guidelines

### Before You Start

1. **Check Existing Issues**: See if someone else is already working on it
2. **Create an Issue**: Propose significant changes before implementing
3. **Follow Ethics**: All content must promote ethical, legal security practice
4. **Test Your Content**: Verify code examples work and instructions are accurate

### Ethical Standards

**All contributions MUST**:
- ✅ Promote legal and ethical security testing
- ✅ Include appropriate warnings and disclaimers
- ✅ Emphasize getting authorization before testing
- ✅ Follow responsible disclosure principles
- ✅ Not include actual exploits against real systems
- ✅ Respect privacy and confidentiality

**Never include**:
- ❌ Zero-day exploits
- ❌ Specific vulnerabilities in unpatched systems
- ❌ Personally identifiable information
- ❌ Actual credentials or API keys
- ❌ Content encouraging illegal activity

### Content Quality Standards

#### Writing Style
- Clear and concise
- Appropriate for target audience (note skill level)
- Use active voice
- Break complex topics into digestible sections
- Include practical examples

#### Code Quality
- Well-commented
- Follow language best practices
- Include error handling
- Test on multiple platforms when applicable
- Use placeholder values (e.g., `your-api-key-here`, `attacker.com`)

#### Structure
- Use consistent markdown formatting
- Follow existing document structure
- Include table of contents for long documents
- Cross-reference related topics
- Add to glossary when introducing new terms

### Technical Requirements

#### Markdown Format
- Use standard markdown syntax
- Code blocks with language specification
- Proper heading hierarchy (h1 → h2 → h3)
- Use tables for comparisons
- Include links to related content

#### Code Examples

**Python**:
```python
# Good example
def example_function():
    """
    Clear description of what this does
    """
    # Implementation with comments
    pass
```

**PowerShell**:
```powershell
# Good example
# Clear description
$variable = "value"
Write-Host "Description of output"
```

**Bash**:
```bash
# Good example
# Description of what command does
command --option value
```

#### Screenshots and Images
- Use clear, high-resolution screenshots
- Annotate important areas
- Crop to relevant content
- Save in appropriate format (PNG for screenshots)
- Optimize file size
- Include alt text for accessibility

## How to Contribute

### Step 1: Fork and Clone

```bash
# Fork the repository on GitHub

# Clone your fork
git clone https://github.com/YOUR-USERNAME/hid-barcode-scanner.git
cd hid-barcode-scanner

# Add upstream remote
git remote add upstream https://github.com/Fabi019/hid-barcode-scanner.git
```

### Step 2: Create a Branch

```bash
# Create branch for your contribution
git checkout -b docs/improve-ble-security-intro

# Branch naming conventions:
# docs/add-exercise-X
# docs/fix-typo-module-Y
# docs/update-tool-Z
```

### Step 3: Make Your Changes

1. Edit files in `docs/` directory
2. Test any code examples
3. Verify all links work
4. Check markdown formatting
5. Run spell check

### Step 4: Test Your Changes

**Preview Markdown**:
- Use GitHub's markdown preview
- Or use VS Code with markdown preview
- Check formatting, links, images

**Test Code Examples**:
- Run all code snippets
- Test on target platforms
- Verify output is as expected
- Check for security issues

**Review Checklist**:
- [ ] Spelling and grammar checked
- [ ] Code examples tested
- [ ] Links verified
- [ ] Ethical guidelines followed
- [ ] No sensitive information included
- [ ] Images optimized
- [ ] Cross-references updated

### Step 5: Commit Changes

```bash
# Stage your changes
git add docs/

# Commit with clear message
git commit -m "docs: improve BLE security introduction clarity

- Clarify pairing methods section
- Add visual diagram for protocol stack
- Fix typos in challenge-response explanation
- Add reference to relevant exercise"

# Commit message format:
# docs: brief description
# 
# - Detailed change 1
# - Detailed change 2
```

### Step 6: Push and Create Pull Request

```bash
# Push to your fork
git push origin docs/improve-ble-security-intro

# Go to GitHub and create Pull Request
```

### Pull Request Guidelines

**Title**: Clear, concise description
- ✅ "docs: add RFCOMM fuzzing examples"
- ✅ "docs: fix broken links in exercise 2"
- ❌ "Update docs"
- ❌ "Fixed stuff"

**Description**: Include:
- What changes were made
- Why changes were needed
- Related issues (if any)
- Testing performed
- Screenshots (if UI/visual changes)

**Example PR Description**:
```markdown
## Changes
Added practical examples for RFCOMM protocol fuzzing in module 5.

## Motivation
Students requested more hands-on fuzzing examples. Current content
was too theoretical.

## Testing
- Tested all code examples on Ubuntu 22.04 and Windows 11
- Verified fuzzing techniques against test BLE device
- Confirmed no syntax errors

## Related Issues
Closes #123

## Checklist
- [x] Code examples tested
- [x] No sensitive information included
- [x] Ethical guidelines followed
- [x] Links verified
```

## Content Structure

### File Organization

```
docs/
├── README.md                    # Main wiki index
├── QUICKSTART.md               # Quick start guide
├── CONTRIBUTING.md             # This file
├── 01-ble-security-introduction.md
├── 02-bluetooth-hid-protocol.md
├── ...
├── exercises/
│   ├── ex01-hid-injection-basics.md
│   ├── ex02-ble-reconnaissance.md
│   └── ...
└── reference/
    ├── tools.md
    ├── glossary.md
    └── resources.md
```

### Document Template

Use this template for new modules:

```markdown
# Module Title

## Overview
Brief description of what this module covers.

## Table of Contents
1. [Section 1](#section-1)
2. [Section 2](#section-2)
...

## Learning Objectives
- Objective 1
- Objective 2

## Prerequisites
- Prerequisite knowledge
- Required tools

## Section 1
Content...

### Subsection
More detailed content...

## Hands-On Example
Practical example with code...

## Quiz
1. Question 1?
2. Question 2?

## Next Steps
- Link to next module
- Related exercises

## Additional Resources
- External links
- Further reading

---
[← Previous Module](link) | [Next Module →](link)
```

## Style Guide

### Terminology
- Use consistent terminology (see glossary)
- Define acronyms on first use
- Use industry-standard terms

### Code Formatting
- Inline code: Use backticks \`code\`
- Code blocks: Use triple backticks with language
- Commands: Prefix with `$` for user commands, `#` for root

### Warnings and Notes

Use consistent formatting:

```markdown
**⚠️ WARNING**: Critical security information

**Note**: Additional information

**Tip**: Helpful suggestion

**Important**: Key point to remember
```

### Cross-References

Link to related content:
```markdown
See [Module Name](../05-module-name.md) for details.

Complete [Exercise 3](exercises/ex03-name.md) to practice.

Refer to the [Glossary](reference/glossary.md) for definitions.
```

## Review Process

### What Reviewers Look For

1. **Technical Accuracy**: Is the information correct?
2. **Clarity**: Is it easy to understand?
3. **Completeness**: Does it cover the topic adequately?
4. **Ethics**: Does it promote responsible security practice?
5. **Testing**: Have code examples been tested?
6. **Formatting**: Does it follow style guidelines?

### Responding to Feedback

- Be open to suggestions
- Address all comments
- Ask for clarification if needed
- Update PR based on feedback
- Thank reviewers for their time

## Recognition

Contributors will be recognized in:
- Git commit history
- Contributors section (if significant contributions)
- Community recognition posts

## Questions?

- **Technical Questions**: Open a GitHub Discussion
- **Contribution Questions**: Comment on related Issue
- **General Questions**: Open new Issue with "question" label

## Code of Conduct

### Expected Behavior
- Be respectful and inclusive
- Provide constructive feedback
- Focus on improving educational content
- Help others learn
- Follow ethical guidelines

### Unacceptable Behavior
- Sharing exploits for malicious purposes
- Encouraging illegal activity
- Harassment or discrimination
- Publishing others' private information
- Plagiarism

### Reporting Issues
Report concerns to repository maintainers via:
- Private GitHub message
- Email (if provided)
- Issue with "code of conduct" label

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (GNU GPL v3.0 for code, CC BY-SA 4.0 for documentation).

## Thank You!

Your contributions help create better security education for everyone. Every improvement, no matter how small, makes a difference.

**Happy contributing!** 🎓🔒

---

## Quick Contribution Checklist

Before submitting your PR:
- [ ] Content is accurate and tested
- [ ] Follows ethical guidelines
- [ ] No sensitive information included
- [ ] Markdown formatting correct
- [ ] Links work
- [ ] Code examples tested
- [ ] Spell-checked
- [ ] Cross-references updated
- [ ] Clear commit messages
- [ ] PR description complete

---

[← Back to Wiki Home](README.md)
