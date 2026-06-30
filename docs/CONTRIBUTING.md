# Contributing to FlowDrive

First off, thank you for considering contributing to FlowDrive! It's people like you that make FlowDrive such a great tool.

Following these guidelines helps communicate that you respect the time of the developers managing and developing this open source project. In return, they should reciprocate that respect in addressing your issue, assessing changes, and helping you finalize your pull requests.

---

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [How Can I Contribute?](#how-can-i-contribute)
4. [Development Process](#development-process)
5. [Style Guidelines](#style-guidelines)
6. [Community](#community)

---

## Code of Conduct

This project and everyone participating in it is governed by our Code of Conduct. By participating, you are expected to uphold this code.

### Our Pledge

We are committed to making participation in our project a harassment-free experience for everyone, regardless of:
- Age, body size, disability
- Ethnicity, gender identity and expression
- Level of experience, nationality
- Personal appearance, race, religion
- Sexual identity and orientation

### Our Standards

**Positive behavior includes:**
- Using welcoming and inclusive language
- Being respectful of differing viewpoints
- Gracefully accepting constructive criticism
- Focusing on what is best for the community
- Showing empathy towards other community members

**Unacceptable behavior includes:**
- Trolling, insulting/derogatory comments, and personal or political attacks
- Public or private harassment
- Publishing others' private information without explicit permission
- Other conduct which could reasonably be considered inappropriate

---

## Getting Started

### For New Contributors

1. **Read the documentation**
   - [README.md](../README.md) - Project overview
   - [ARCHITECTURE.md](ARCHITECTURE.md) - System design
   - [DEVELOPMENT.md](DEVELOPMENT.md) - Setup guide

2. **Set up your development environment**
   - Follow [DEVELOPMENT.md](DEVELOPMENT.md) for setup instructions
   - Make sure all tests pass: `./scripts/run-tests.sh`

3. **Find an issue to work on**
   - Look for issues labeled `good-first-issue`
   - Comment on the issue to let others know you're working on it

### For Experienced Contributors

- Look for issues labeled `help-wanted` or `enhancement`
- Propose new features via GitHub Discussions
- Help review pull requests

---

## How Can I Contribute?

### Reporting Bugs

**Before submitting a bug report:**
- Check the [documentation](docs/) for solutions
- Search [existing issues](https://github.com/yourusername/flowdrive/issues) to avoid duplicates
- Gather information about the bug

**How to submit a good bug report:**

Use our bug report template and include:
- **Title:** Clear, descriptive title
- **Description:** Detailed description of the issue
- **Steps to reproduce:**
  1. Step one
  2. Step two
  3. See error
- **Expected behavior:** What should happen
- **Actual behavior:** What actually happens
- **Environment:**
  - OS: Windows 11, Ubuntu 22.04, etc.
  - Version: FlowDrive v0.5.0
  - Python version: 3.11
- **Logs:** Relevant log output (use code blocks)
- **Screenshots:** If applicable

**Example:**

```markdown
### Bug: Optimization crashes on large files

**Description:** The optimization process crashes when processing files larger than 10GB.

**Steps to reproduce:**
1. Add a 15GB video file to monitored directory
2. Run manual optimization
3. Watch crash after ~5 minutes

**Expected:** File should be optimized successfully
**Actual:** Process crashes with "Memory allocation failed" error

**Environment:**
- OS: Windows 11 Pro
- RAM: 16GB
- FlowDrive: v0.5.0-beta

**Logs:**
\`\`\`
[ERROR] 2025-10-27 14:32:15 - Memory allocation failed for file: large_video.mp4
[ERROR] 2025-10-27 14:32:15 - Stack trace: ...
\`\`\`
```

### Suggesting Enhancements

**Before suggesting:**
- Check if the feature already exists
- Search for similar suggestions in issues and discussions
- Consider if it fits the project scope

**How to suggest enhancements:**

```markdown
### Feature Request: Dark mode

**Problem:** Current UI only supports light theme, hard to use at night

**Proposed Solution:** Add dark mode toggle in settings

**Alternatives Considered:**
- Auto dark mode based on system settings
- Time-based theme switching

**Additional Context:**
- Many users work late hours
- Modern applications support dark mode
- Could reduce eye strain
```

### Pull Requests

**Good pull requests** are:
- Focused on a single feature or fix
- Well-documented
- Tested
- Following our style guidelines

**Process:**

1. **Fork and create a branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**
   - Write code following our [Style Guidelines](#style-guidelines)
   - Add tests for new functionality
   - Update documentation

3. **Test your changes**
   ```bash
   # Run tests
   ./scripts/run-tests.sh
   
   # Check code style
   # Python
   black src/ tests/
   flake8 src/ tests/
   mypy src/ tests/
   
   # C++
   clang-format -i src/**/*.cpp src/**/*.h
   ```

4. **Commit with a good message**
   ```
   feat: add dark mode support
   
   - Add theme toggle in settings
   - Implement dark theme stylesheets
   - Update documentation
   - Add tests
   
   Closes #123
   ```

5. **Push and create PR**
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Fill out the PR template**
   - Clear description of changes
   - Link to related issues
   - Screenshots (if UI changes)
   - Checklist completed

**PR Template:**

```markdown
## Description
Brief description of what this PR does

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Related Issues
Closes #123
Related to #456

## How Has This Been Tested?
Describe the tests you ran and their results

## Screenshots (if applicable)
![Screenshot](url)

## Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code where necessary
- [ ] I have updated the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix/feature works
- [ ] New and existing tests pass locally
- [ ] Any dependent changes have been merged
```

---

## Development Process

### Branching Strategy

We use Git Flow:

- `main` - Production-ready code
- `develop` - Integration branch for features
- `feature/*` - New features
- `fix/*` - Bug fixes
- `release/*` - Release preparation
- `hotfix/*` - Emergency fixes for production

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Code style (formatting, etc.)
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: Performance improvement
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**

```
feat(ml): add LSTM prediction model

Implement LSTM-based file access prediction with:
- Sequence preprocessing
- Model architecture
- Training pipeline
- Evaluation metrics

Closes #45

---

fix(core): prevent crash on file permission error

Handle PermissionError exception when accessing locked files.
Add retry logic with exponential backoff.

Fixes #123

---

docs(api): update API reference for v1.0

- Add new endpoints
- Update examples
- Fix typos
```

### Code Review Process

**All code changes require review before merging.**

**For Reviewers:**
1. Check code quality and style
2. Verify tests pass and coverage is adequate
3. Ensure documentation is updated
4. Test functionality locally if needed
5. Provide constructive feedback

**Review Checklist:**
- [ ] Code follows style guidelines
- [ ] Tests are comprehensive
- [ ] Documentation is clear and complete
- [ ] No security vulnerabilities introduced
- [ ] Performance impact acceptable
- [ ] Error handling is appropriate

**Approval Process:**
- 1 approval required for minor changes
- 2 approvals required for major changes
- Maintainer approval required for breaking changes

### Testing Requirements

**All contributions must include tests.**

**Test Coverage:**
- Unit tests: >80% coverage
- Integration tests for new features
- Performance tests for optimization changes

**Running Tests:**
```bash
# All tests
./scripts/run-tests.sh

# Specific component
pytest src/intelligence/tests/ -v

# With coverage
pytest --cov=src/intelligence --cov-report=html
```

---

## Style Guidelines

### General Principles

- **Readability:** Code is read more than written
- **Simplicity:** Simple solutions are usually better
- **Consistency:** Follow existing patterns
- **Documentation:** Code should be self-documenting, with comments for "why"

### Python

**Follow PEP 8 with type hints:**

```python
from typing import List, Optional, Dict

def optimize_files(
    file_paths: List[str],
    priority: int = 5,
    force: bool = False
) -> Optional[Dict[str, Any]]:
    """
    Optimize specified files.
    
    Args:
        file_paths: List of file paths to optimize
        priority: Optimization priority (1-10)
        force: Force optimization even if recently optimized
        
    Returns:
        Dictionary with optimization results, or None if failed
        
    Raises:
        ValueError: If priority is out of range
        FileNotFoundError: If any file doesn't exist
    """
    if not 1 <= priority <= 10:
        raise ValueError(f"Priority must be 1-10, got {priority}")
    
    # Implementation
    pass
```

**Use tools:**
```bash
# Format
black src/ tests/

# Lint
flake8 src/ tests/
pylint src/ tests/

# Type check
mypy src/ tests/
```

### C++

**Modern C++17/20:**

```cpp
#include <memory>
#include <string>
#include <vector>

class FileOptimizer {
public:
    explicit FileOptimizer(const Config& config);
    
    // Use Result or std::expected for error handling
    std::expected<OptimizationResult, Error> Optimize(
        const std::vector<std::string>& file_paths,
        int priority = 5
    );
    
private:
    std::unique_ptr<MLEngine> ml_engine_;
    std::shared_ptr<Database> db_;
    
    // Private member variables end with underscore
    int max_threads_;
};
```

**Use tools:**
```bash
# Format
clang-format -i src/**/*.cpp src/**/*.h

# Lint
clang-tidy src/*.cpp --

# Static analysis
cppcheck src/ --enable=all
```

### Rust

**Idiomatic Rust:**

```rust
use std::path::Path;
use anyhow::Result;

pub struct FileOptimizer {
    ml_engine: MLEngine,
    db: Arc<Database>,
}

impl FileOptimizer {
    pub fn new(config: Config) -> Self {
        Self {
            ml_engine: MLEngine::new(config.ml_config),
            db: Arc::new(Database::connect(config.db_path)?),
        }
    }
    
    pub async fn optimize(
        &self,
        file_paths: &[String],
        priority: u8,
    ) -> Result<OptimizationResult> {
        // Implementation
        Ok(OptimizationResult::default())
    }
}
```

**Use tools:**
```bash
# Format
cargo fmt

# Lint
cargo clippy -- -D warnings

# Check
cargo check
```

### Documentation

**All public APIs must be documented.**

**Python docstrings** (Google style):
```python
def complex_function(arg1: str, arg2: int) -> Dict[str, Any]:
    """
    Brief description.
    
    Longer description with more details about what the function does,
    edge cases, performance characteristics, etc.
    
    Args:
        arg1: Description of arg1
        arg2: Description of arg2
        
    Returns:
        Dictionary containing:
            - key1 (str): Description
            - key2 (int): Description
            
    Raises:
        ValueError: When arg2 is negative
        
    Example:
        >>> result = complex_function("test", 5)
        >>> print(result['key1'])
        'test_processed'
    """
    pass
```

**C++ documentation** (Doxygen style):
```cpp
/**
 * @brief Optimizes a collection of files
 *
 * This function takes a list of file paths and optimizes their storage
 * placement based on ML predictions and user configuration.
 *
 * @param file_paths Vector of file paths to optimize
 * @param priority Optimization priority from 1 (low) to 10 (high)
 * @return Result containing optimization statistics or error
 *
 * @throws std::invalid_argument if priority is out of range
 *
 * @note This function may take significant time for large files
 * @warning Ensure files are not in use before optimization
 *
 * @example
 * @code
 * std::vector<std::string> files = {"/path/to/file1", "/path/to/file2"};
 * auto result = optimizer.Optimize(files, 8);
 * if (result.has_value()) {
 *     std::cout << "Optimized " << result->files_count << " files\n";
 * }
 * @endcode
 */
std::expected<OptimizationResult, Error> Optimize(
    const std::vector<std::string>& file_paths,
    int priority
);
```

---

## Community

### Communication Channels

- **GitHub Issues:** Bug reports, feature requests
- **GitHub Discussions:** General questions, ideas
- **Discord/Slack:** Real-time chat (links TBD)
- **Email:** dev@flowdrive.io (for private matters)

### Getting Help

**Before asking:**
1. Check documentation
2. Search existing issues/discussions
3. Try debugging yourself

**When asking:**
- Be specific about your problem
- Include relevant code/logs
- Describe what you've tried
- Be patient and respectful

### Recognition

**Contributors are recognized through:**
- GitHub contributors page
- Release notes acknowledgments
- Special badges for significant contributions
- Potential inclusion in core team

---

## License

By contributing to FlowDrive, you agree that your contributions will be licensed under the MIT License.

---

## Questions?

Don't hesitate to ask! We're here to help:
- Open a [GitHub Discussion](https://github.com/yourusername/flowdrive/discussions)
- Reach out on Discord (link TBD)
- Email: dev@flowdrive.io

**Thank you for contributing to FlowDrive! 🚀**
