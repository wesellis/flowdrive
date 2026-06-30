# FlowDrive Development Guide

Welcome to the FlowDrive development team! This guide will help you set up your development environment and start contributing.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Development Environment Setup](#development-environment-setup)
3. [Project Structure](#project-structure)
4. [Building the Project](#building-the-project)
5. [Running Tests](#running-tests)
6. [Development Workflow](#development-workflow)
7. [Coding Standards](#coding-standards)
8. [Debugging](#debugging)
9. [Common Tasks](#common-tasks)

---

## Prerequisites

### Required Software

**Core Development:**
- **Git** 2.30+
- **C++ Compiler:**
  - Windows: Visual Studio 2022 (Community Edition OK)
  - Linux: GCC 11+ or Clang 14+
  - macOS: Xcode 14+
- **OR Rust:** 1.70+ (if choosing Rust path)
- **Python:** 3.10 or 3.11
- **CMake:** 3.20+ (for C++ build)
- **Qt6:** 6.5+ (for UI development)

**Additional Tools:**
- **Protocol Buffers:** 3.21+ (for gRPC)
- **SQLite:** 3.38+ (usually included with Python)
- **Node.js:** 18+ (for documentation generation)

### Recommended IDEs

**Core Engine (C++):**
- Visual Studio 2022 (Windows)
- CLion (Cross-platform)
- VS Code with C++ extensions

**Core Engine (Rust):**
- VS Code with rust-analyzer
- IntelliJ IDEA with Rust plugin
- Vim/Neovim with rust-analyzer

**Intelligence Layer & UI (Python):**
- PyCharm Professional or Community
- VS Code with Python extensions
- Vim/Neovim with Python LSP

---

## Development Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/flowdrive.git
cd flowdrive
```

### 2. C++ Path Setup

#### Windows (Visual Studio)

```powershell
# Install dependencies via vcpkg
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install

# Install FlowDrive dependencies
.\vcpkg install grpc:x64-windows
.\vcpkg install protobuf:x64-windows
.\vcpkg install sqlite3:x64-windows
.\vcpkg install gtest:x64-windows
.\vcpkg install boost-filesystem:x64-windows
```

#### Linux/macOS

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install -y build-essential cmake git
sudo apt install -y libgrpc++-dev libprotobuf-dev protobuf-compiler-grpc
sudo apt install -y libsqlite3-dev libboost-filesystem-dev
sudo apt install -y libgtest-dev

# macOS (via Homebrew)
brew install cmake grpc protobuf sqlite3 boost googletest
```

#### Build Core Engine

```bash
cd src/core-engine
mkdir build && cd build
cmake ..
cmake --build . --config Release

# Run tests
ctest --output-on-failure
```

### 3. Rust Path Setup (Alternative)

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Install dependencies and build
cd src/core-engine-rust
cargo build
cargo test
```

### 4. Python Intelligence Layer Setup

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# Install dependencies
pip install --upgrade pip
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Run tests
pytest tests/
```

**requirements.txt:**
```
# ML and Data Processing
scikit-learn>=1.3.0
tensorflow>=2.13.0
pandas>=2.1.0
numpy>=1.24.0

# Communication
grpcio>=1.56.0
grpcio-tools>=1.56.0

# Database
sqlalchemy>=2.0.0

# System Monitoring
psutil>=5.9.0

# Configuration
pyyaml>=6.0

# Utilities
python-dateutil>=2.8.0
```

**requirements-dev.txt:**
```
# Testing
pytest>=7.4.0
pytest-cov>=4.1.0
pytest-asyncio>=0.21.0

# Linting and Formatting
black>=23.7.0
flake8>=6.1.0
mypy>=1.5.0
pylint>=2.17.0

# Documentation
sphinx>=7.1.0
sphinx-rtd-theme>=1.3.0
```

### 5. UI Development Setup

```bash
# Install PyQt6
pip install PyQt6>=6.5.0
pip install PyQt6-tools>=6.5.0

# Install additional UI dependencies
pip install matplotlib>=3.7.0  # For charts
pip install pyqtgraph>=0.13.0  # For real-time plots
```

---

## Project Structure

```
flowdrive/
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── Initial Project Documentation/
│   │   ├── 01-PROJECT_SPECIFICATION.md
│   │   └── 02-TECHNICAL_APPENDIX.md
│   ├── ARCHITECTURE.md
│   ├── DEVELOPMENT.md (this file)
│   ├── API_REFERENCE.md
│   ├── ML_MODELS.md
│   ├── ROADMAP.md
│   └── CONTRIBUTING.md
├── src/
│   ├── core-engine/          # C++ or Rust core
│   │   ├── CMakeLists.txt    # C++ build
│   │   ├── Cargo.toml        # Rust build
│   │   ├── include/          # Header files
│   │   ├── src/              # Source files
│   │   └── tests/            # Unit tests
│   ├── intelligence/         # Python ML service
│   │   ├── __init__.py
│   │   ├── ml_engine/
│   │   ├── analytics/
│   │   ├── optimizer/
│   │   ├── models/           # Trained ML models
│   │   └── tests/
│   ├── ui/                   # PyQt6 UI
│   │   ├── __init__.py
│   │   ├── main_window.py
│   │   ├── widgets/
│   │   ├── resources/        # Images, icons
│   │   └── tests/
│   ├── proto/                # gRPC protocol definitions
│   │   ├── flowdrive.proto
│   │   └── generate.sh       # Generate code from proto
│   └── common/               # Shared utilities
│       ├── database/
│       ├── config/
│       └── logging/
├── tests/
│   ├── integration/          # Integration tests
│   ├── performance/          # Performance benchmarks
│   └── fixtures/             # Test data
├── scripts/
│   ├── setup-dev.sh          # Dev environment setup
│   ├── build.sh              # Build all components
│   └── run-tests.sh          # Run all tests
├── config/
│   ├── config.yaml.example   # Example configuration
│   └── logging.yaml          # Logging configuration
└── tools/
    ├── benchmark/            # Benchmarking tools
    └── profiling/            # Performance profiling
```

---

## Building the Project

### Full Build (All Components)

```bash
# From project root
./scripts/build.sh

# Or manually:

# 1. Build Protocol Buffers
cd src/proto
./generate.sh

# 2. Build Core Engine
cd ../core-engine
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .

# 3. Install Python packages
cd ../../intelligence
pip install -e .

# 4. Build UI
cd ../ui
pyrcc6 resources.qrc -o resources_rc.py
```

### Development Build (Fast Iteration)

```bash
# Core Engine (C++)
cd src/core-engine/build
cmake --build . --config Debug

# Python components (no build needed)
# Just edit and run

# UI with auto-reload
cd src/ui
python main.py --debug
```

---

## Running Tests

### Unit Tests

```bash
# Core Engine (C++)
cd src/core-engine/build
ctest --output-on-failure

# Or with Rust
cd src/core-engine-rust
cargo test

# Intelligence Layer (Python)
cd src/intelligence
pytest tests/ -v

# UI (Python)
cd src/ui
pytest tests/ -v
```

### Integration Tests

```bash
cd tests/integration
pytest -v --log-cli-level=INFO
```

### Performance Tests

```bash
cd tests/performance
python benchmark_suite.py --output=results.json
```

### Code Coverage

```bash
# Python
pytest --cov=src/intelligence --cov-report=html
# Open htmlcov/index.html

# C++ (with lcov)
cmake -DCMAKE_BUILD_TYPE=Coverage ..
make coverage
# Open coverage/index.html
```

---

## Development Workflow

### 1. Create a Feature Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

### 2. Make Changes

- Write code following [Coding Standards](#coding-standards)
- Add tests for new functionality
- Update documentation if needed

### 3. Test Your Changes

```bash
# Run relevant tests
pytest tests/test_your_feature.py -v

# Run full test suite
./scripts/run-tests.sh
```

### 4. Commit Changes

```bash
git add .
git commit -m "feat: add ML prediction caching

- Implement LRU cache for predictions
- Add cache hit rate metrics
- Update tests

Closes #123"
```

**Commit Message Format:**
```
<type>: <subject>

<body>

<footer>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### 5. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a PR on GitHub with:
- Clear description of changes
- Link to relevant issues
- Screenshots (if UI changes)
- Test results

---

## Coding Standards

### C++ Style Guide

Follow [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) with these additions:

```cpp
// Use modern C++17/20 features
auto result = some_function();

// Use smart pointers
std::unique_ptr<FileMonitor> monitor = std::make_unique<FileMonitor>();

// Use const correctness
const std::string& GetFilePath() const;

// Use RAII for resource management
class FileHandle {
public:
    FileHandle(const std::string& path);
    ~FileHandle();  // Automatic cleanup
};

// Error handling with exceptions or Result types
std::expected<FileInfo, Error> GetFileInfo(const std::string& path);
```

**Naming Conventions:**
- Classes: `PascalCase`
- Functions: `PascalCase` or `camelCase` (be consistent)
- Variables: `snake_case`
- Constants: `kPascalCase` or `UPPER_SNAKE_CASE`
- Member variables: `member_name_` (trailing underscore)

### Rust Style Guide

Follow [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/):

```rust
// Use idiomatic Rust patterns
pub struct FileMonitor {
    handles: Vec<FileHandle>,
}

impl FileMonitor {
    pub fn new() -> Self {
        Self {
            handles: Vec::new(),
        }
    }
    
    pub fn start(&mut self) -> Result<(), Error> {
        // Use Result for error handling
        Ok(())
    }
}

// Use descriptive variable names
let file_path = Path::new("/path/to/file");
let metadata = fs::metadata(&file_path)?;

// Use cargo fmt and cargo clippy
```

### Python Style Guide

Follow [PEP 8](https://peps.python.org/pep-0008/) with type hints:

```python
from typing import List, Optional, Dict
from dataclasses import dataclass

@dataclass
class FileMetadata:
    """Metadata for a tracked file."""
    path: str
    size_bytes: int
    importance_score: float
    last_accessed: datetime

def predict_next_access(
    history: List[FileAccess],
    context: Dict[str, Any]
) -> Optional[Prediction]:
    """
    Predict which file will be accessed next.
    
    Args:
        history: List of recent file access events
        context: Current system context
        
    Returns:
        Prediction object or None if cannot predict
    """
    pass

# Use black for formatting
# Use mypy for type checking
# Use pylint for linting
```

**Python Naming:**
- Classes: `PascalCase`
- Functions: `snake_case`
- Variables: `snake_case`
- Constants: `UPPER_SNAKE_CASE`
- Private: `_leading_underscore`

---

## Debugging

### Core Engine (C++)

**Visual Studio:**
```
F5 - Start debugging
F9 - Toggle breakpoint
F10 - Step over
F11 - Step into
```

**GDB (Linux):**
```bash
gdb ./flowdrive-core
(gdb) break main
(gdb) run
(gdb) next
(gdb) print variable_name
```

### Python Components

**VS Code (launch.json):**
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Intelligence Service",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/src/intelligence/main.py",
            "console": "integratedTerminal"
        },
        {
            "name": "UI Application",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/src/ui/main.py",
            "console": "integratedTerminal"
        }
    ]
}
```

**PyCharm:**
- Right-click on file → Debug
- Set breakpoints with mouse click
- Use Debug Console for inspection

---

## Common Tasks

### Add a New ML Model

1. Create model class in `src/intelligence/ml_engine/models/`:
```python
class NewModel:
    def train(self, data):
        pass
    
    def predict(self, input):
        pass
    
    def save(self, path):
        pass
    
    def load(self, path):
        pass
```

2. Register in `MLEngine`:
```python
self.new_model = NewModel()
```

3. Add tests in `tests/ml_engine/`:
```python
def test_new_model_accuracy():
    model = NewModel()
    # Test implementation
```

### Add a New File Operation

1. Define in proto file:
```protobuf
message NewOperation {
    string file_path = 1;
    // Add fields
}
```

2. Implement in Core Engine:
```cpp
bool DefragEngine::ExecuteNewOperation(const NewOperation& op) {
    // Implementation
}
```

3. Add Python wrapper:
```python
def execute_new_operation(self, file_path: str) -> bool:
    request = flowdrive_pb2.NewOperation(file_path=file_path)
    response = self.stub.ExecuteNewOperation(request)
    return response.success
```

### Update Database Schema

1. Create migration script:
```python
# migrations/001_add_new_table.py
def upgrade(conn):
    conn.execute("""
        CREATE TABLE new_table (
            id INTEGER PRIMARY KEY,
            data TEXT
        )
    """)

def downgrade(conn):
    conn.execute("DROP TABLE new_table")
```

2. Apply migration:
```bash
python scripts/migrate.py upgrade
```

### Add New UI Widget

1. Create widget file:
```python
# src/ui/widgets/new_widget.py
from PyQt6.QtWidgets import QWidget

class NewWidget(QWidget):
    def __init__(self, parent=None):
        super().__init__(parent)
        self.init_ui()
    
    def init_ui(self):
        # Setup UI
        pass
```

2. Add to main window:
```python
self.new_widget = NewWidget(self)
self.layout.addWidget(self.new_widget)
```

---

## Getting Help

- **Documentation:** Read docs in `docs/` folder
- **Issues:** Check [GitHub Issues](https://github.com/yourusername/flowdrive/issues)
- **Discussions:** Use [GitHub Discussions](https://github.com/yourusername/flowdrive/discussions)
- **Chat:** Join our Discord/Slack (when available)

---

## Next Steps

1. ✅ Set up your development environment
2. ✅ Build the project
3. ✅ Run the test suite
4. 📖 Read the [Architecture Documentation](ARCHITECTURE.md)
5. 🎯 Pick an issue labeled `good-first-issue`
6. 💻 Start coding!

**Happy coding! 🚀**
