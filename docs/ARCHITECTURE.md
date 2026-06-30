# FlowDrive Architecture

**Version:** 1.0 (Planning Phase)  
**Last Updated:** October 2025  
**Status:** Draft

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Layers](#architecture-layers)
3. [Component Details](#component-details)
4. [Data Flow](#data-flow)
5. [Technology Decisions](#technology-decisions)
6. [Deployment Architecture](#deployment-architecture)
7. [Security Architecture](#security-architecture)
8. [Performance Considerations](#performance-considerations)

---

## System Overview

FlowDrive is built as a layered architecture with clear separation of concerns:

```
┌───────────────────────────────────────────────────────┐
│                  Presentation Layer                    │
│              (PyQt6 Desktop Application)               │
└─────────────────────────┬─────────────────────────────┘
                          │
┌─────────────────────────┴─────────────────────────────┐
│                  Intelligence Layer                    │
│                   (Python Service)                     │
│  ┌────────────────────────────────────────────────┐  │
│  │  ML Engine                                      │  │
│  │  • Access Pattern Prediction (LSTM)             │  │
│  │  • File Importance Classification (RF)          │  │
│  │  • File Clustering (DBSCAN)                     │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Analytics Engine                               │  │
│  │  • Performance Metrics                          │  │
│  │  • Reporting                                    │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Optimization Planner                           │  │
│  │  • Strategy Selection                           │  │
│  │  • Priority Scheduling                          │  │
│  └────────────────────────────────────────────────┘  │
└─────────────────────────┬─────────────────────────────┘
                          │ gRPC
┌─────────────────────────┴─────────────────────────────┐
│                    Core Engine Layer                   │
│                    (C++/Rust Service)                  │
│  ┌────────────────────────────────────────────────┐  │
│  │  File System Monitor                            │  │
│  │  • Event Tracking                               │  │
│  │  • Access Logging                               │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Defragmentation Engine                         │  │
│  │  • Transaction Manager                          │  │
│  │  • Safe File Operations                         │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Storage Controller                             │  │
│  │  • Device Detection                             │  │
│  │  • I/O Optimization                             │  │
│  └────────────────────────────────────────────────┘  │
└─────────────────────────┬─────────────────────────────┘
                          │
┌─────────────────────────┴─────────────────────────────┐
│              Operating System Interface                │
│  • Windows: WinAPI, Defrag API, Minifilter Drivers   │
│  • Linux: inotify, fanotify, ioctl                   │
│  • macOS: FSEvents, Core Storage                     │
└───────────────────────────────────────────────────────┘
```

---

## Architecture Layers

### 1. Presentation Layer

**Technology:** PyQt6  
**Responsibilities:**
- User interface rendering
- User interaction handling
- Real-time status updates
- Configuration management
- Visualization of metrics

**Key Components:**
- Main Dashboard
- System Tray Icon
- Settings Panel
- Performance Charts
- Notification System

### 2. Intelligence Layer

**Technology:** Python 3.10+  
**Responsibilities:**
- Machine learning model training and inference
- Usage pattern analysis
- Optimization strategy generation
- Performance metric calculation
- Report generation

**Key Components:**

#### ML Engine
```python
class MLEngine:
    def __init__(self):
        self.access_predictor = LSTMPredictor()
        self.importance_classifier = RandomForestClassifier()
        self.file_clusterer = DBSCANClusterer()
    
    def predict_next_access(self, context):
        """Predict which files will be accessed next"""
        pass
    
    def classify_file_importance(self, file_metadata):
        """Determine file importance score"""
        pass
    
    def cluster_related_files(self, file_list):
        """Group files that should be stored together"""
        pass
```

#### Analytics Engine
- Tracks application launch times
- Measures I/O throughput
- Calculates performance improvements
- Generates usage reports

#### Optimization Planner
- Receives predictions from ML Engine
- Creates file movement plans
- Prioritizes operations
- Schedules execution

### 3. Core Engine Layer

**Technology:** C++ or Rust (Decision pending)  
**Responsibilities:**
- Low-level file system operations
- File access monitoring
- Safe defragmentation
- Storage device management
- Transaction logging

**Key Components:**

#### File System Monitor
```cpp
class FileSystemMonitor {
public:
    void StartMonitoring(const std::vector<std::string>& paths);
    void StopMonitoring();
    
    // Callbacks
    void OnFileOpen(const FileEvent& event);
    void OnFileClose(const FileEvent& event);
    void OnFileModify(const FileEvent& event);
    
private:
    // Platform-specific implementation
    #ifdef _WIN32
        HANDLE handle_;
    #elif __linux__
        int inotify_fd_;
    #endif
};
```

#### Defragmentation Engine
```cpp
class DefragEngine {
public:
    bool MoveFileTransactional(const FileOperation& op);
    void OptimizeDisk(const std::vector<FileOperation>& operations);
    
private:
    TransactionLog transaction_log_;
    ChecksumVerifier verifier_;
};
```

#### Storage Controller
- Detects SSD vs HDD
- Queries TRIM support
- Monitors storage health
- Manages I/O operations

### 4. Operating System Interface

Platform-specific implementations for:
- File system access
- Device enumeration
- Performance monitoring
- System notifications

---

## Component Details

### Communication Protocol (gRPC)

**Why gRPC?**
- Type-safe with Protocol Buffers
- Bi-directional streaming
- High performance
- Cross-language support
- Built-in error handling

**Service Definition:**

```protobuf
syntax = "proto3";

service FlowDriveService {
  // File monitoring
  rpc StreamFileEvents(StreamRequest) returns (stream FileEvent);
  
  // Optimization
  rpc StartOptimization(OptimizationRequest) returns (OptimizationResponse);
  rpc GetOptimizationStatus(StatusRequest) returns (OptimizationStatus);
  
  // Metrics
  rpc GetPerformanceMetrics(MetricsRequest) returns (MetricsResponse);
  
  // Configuration
  rpc UpdateConfiguration(ConfigUpdate) returns (ConfigResponse);
}
```

### Data Storage

**Local Database:** SQLite  
**Schema:**

```sql
-- File access tracking
CREATE TABLE file_access (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    file_path TEXT NOT NULL,
    access_time TIMESTAMP NOT NULL,
    access_type TEXT NOT NULL,  -- 'read', 'write', 'execute'
    application TEXT,
    duration_ms INTEGER,
    INDEX idx_file_path (file_path),
    INDEX idx_access_time (access_time)
);

-- File metadata
CREATE TABLE file_metadata (
    file_path TEXT PRIMARY KEY,
    size_bytes INTEGER NOT NULL,
    file_type TEXT,
    importance_score REAL DEFAULT 0.5,
    last_optimized TIMESTAMP,
    storage_device TEXT,
    fragment_count INTEGER
);

-- Performance metrics
CREATE TABLE performance_metrics (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    metric_type TEXT NOT NULL,
    value REAL NOT NULL,
    timestamp TIMESTAMP NOT NULL,
    context TEXT,
    INDEX idx_metric_type (metric_type),
    INDEX idx_timestamp (timestamp)
);

-- Optimization history
CREATE TABLE optimization_log (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    operation_type TEXT NOT NULL,
    files_affected INTEGER,
    bytes_moved INTEGER,
    duration_seconds INTEGER,
    performance_gain REAL,
    timestamp TIMESTAMP NOT NULL,
    status TEXT NOT NULL  -- 'success', 'failed', 'rolled_back'
);

-- ML model metadata
CREATE TABLE ml_models (
    model_name TEXT PRIMARY KEY,
    model_type TEXT NOT NULL,
    version INTEGER NOT NULL,
    trained_date TIMESTAMP NOT NULL,
    accuracy_score REAL,
    model_data BLOB
);
```

---

## Data Flow

### 1. File Access Monitoring Flow

```
User accesses file
       ↓
OS File System
       ↓
File System Monitor (C++/Rust)
       ↓
Log to SQLite database
       ↓
Stream event via gRPC
       ↓
Intelligence Layer (Python)
       ↓
ML Engine processes event
       ↓
Update predictions
```

### 2. Optimization Flow

```
ML Engine generates predictions
       ↓
Optimization Planner creates file movement plan
       ↓
Send plan via gRPC
       ↓
Core Engine validates plan
       ↓
Execute transactions (with rollback capability)
       ↓
Verify file integrity (checksums)
       ↓
Update file system pointers
       ↓
Report status back to Intelligence Layer
       ↓
Update UI with results
```

### 3. Performance Measurement Flow

```
Application launches
       ↓
Analytics Engine starts timer
       ↓
Monitor process startup
       ↓
Detect application ready state
       ↓
Record launch time
       ↓
Store metric in database
       ↓
Calculate improvement vs baseline
       ↓
Display in UI
```

---

## Technology Decisions

### Core Engine: C++ vs Rust

**Decision Status:** Under Evaluation

| Aspect | C++ | Rust |
|--------|-----|------|
| Memory Safety | Manual (prone to errors) | Automatic (borrow checker) |
| Performance | Excellent | Excellent |
| Ecosystem | Mature, extensive | Growing rapidly |
| Windows APIs | Native support | Via winapi crate |
| Learning Curve | Moderate (if experienced) | Steep (if new) |
| Team Expertise | More common | Less common |
| Development Speed | Moderate | Slower initially |
| Maintenance | Higher risk | Lower risk |

**Recommendation:** 
- **Rust** for new project (safety, modern tooling)
- **C++** if team has strong C++ experience already

### Intelligence Layer: Python

**Why Python?**
- ✅ Rich ML ecosystem (scikit-learn, TensorFlow, PyTorch)
- ✅ Rapid development
- ✅ Easy to iterate on ML models
- ✅ Large talent pool
- ✅ Excellent data processing libraries

### UI Framework: PyQt6

**Why PyQt6?**
- ✅ Native look and feel
- ✅ Cross-platform
- ✅ Rich widget set
- ✅ Excellent charting capabilities
- ✅ Good performance
- ✅ Same language as Intelligence Layer

**Alternative Considered:** Electron
- ❌ Too heavy for optimization tool
- ❌ High memory usage
- ✅ Modern web technologies
- ✅ Easier for some developers

---

## Deployment Architecture

### Development Environment

```
Developer Machine
├── Core Engine (C++/Rust)
│   ├── Build: CMake or Cargo
│   ├── Tests: GoogleTest or cargo test
│   └── Debug: GDB or lldb
├── Intelligence Layer (Python)
│   ├── Virtual Environment: venv
│   ├── Tests: pytest
│   └── Debug: pdb or debugpy
└── UI (PyQt6)
    ├── Designer: Qt Designer
    ├── Tests: pytest-qt
    └── Debug: Qt Creator
```

### Production Deployment

**Windows:**
```
C:\Program Files\FlowDrive\
├── flowdrive-core.exe       (C++/Rust engine)
├── flowdrive-intelligence\  (Python service)
├── flowdrive-ui.exe         (PyQt6 application)
├── config.yaml
├── models\                  (ML models)
└── data\                    (SQLite database)
```

**Linux:**
```
/opt/flowdrive/
├── flowdrive-core           (C++/Rust engine)
├── flowdrive-intelligence/  (Python service)
├── flowdrive-ui             (PyQt6 application)
├── config.yaml
├── models/
└── data/

/etc/systemd/system/
└── flowdrive.service        (Systemd service)
```

### Multi-Process Architecture

FlowDrive runs as three separate processes:

1. **Core Engine Service** (Always running)
   - Low-level system access
   - Minimal resource usage when idle
   - Runs with appropriate privileges

2. **Intelligence Service** (Runs periodically)
   - Processes accumulated data
   - Trains/updates ML models
   - Generates optimization plans
   - Can be scheduled or on-demand

3. **UI Application** (User-launched)
   - Shows status and metrics
   - Allows user configuration
   - Triggers manual optimizations
   - Can close while services run

---

## Security Architecture

### Privilege Separation

```
┌─────────────────────────────────────┐
│  UI Application (User Privileges)   │
└──────────────┬──────────────────────┘
               │ gRPC (localhost only)
┌──────────────┴──────────────────────┐
│  Intelligence Layer (User Privs)    │
└──────────────┬──────────────────────┘
               │ gRPC (localhost only)
┌──────────────┴──────────────────────┐
│  Core Engine (Elevated if needed)   │
└─────────────────────────────────────┘
```

### Data Protection

1. **Local Storage Only**
   - All data stays on user's machine
   - No cloud telemetry by default
   - Encrypted database (optional)

2. **File System Access**
   - Never touch system files
   - Respect file permissions
   - Transaction logs for auditing

3. **Network Communication**
   - gRPC only on localhost
   - No external connections required
   - Optional update checks (can be disabled)

### Transaction Safety

Every file operation follows ACID principles:
- **Atomic:** Operations complete fully or not at all
- **Consistent:** File system remains valid
- **Isolated:** Operations don't interfere
- **Durable:** Changes persist after commit

---

## Performance Considerations

### Resource Limits

**Core Engine:**
- CPU: <5% average during optimization
- Memory: <100MB for monitoring
- I/O: Throttled to avoid user impact

**Intelligence Layer:**
- CPU: Bursts during ML training (can be scheduled)
- Memory: <500MB with models loaded
- Storage: <1GB for database and models

**UI:**
- CPU: <2% when active
- Memory: <200MB
- Updates: Every 5 seconds (configurable)

### Scalability Targets

- Support up to 10 million files tracked
- Handle up to 1,000 file events per second
- Optimize 100GB+ in single session
- ML inference in <100ms per prediction

### Performance Optimization Strategies

1. **Async Operations**
   - Non-blocking I/O
   - Background processing
   - Queued operations

2. **Caching**
   - File metadata cache
   - ML predictions cache
   - Filesystem layout cache

3. **Batch Processing**
   - Group file operations
   - Batch database writes
   - Aggregate metrics

4. **Smart Scheduling**
   - Run during idle time
   - Pause on high system load
   - Respect battery status

---

## Future Architecture Considerations

### Phase 2: Multi-Device Support

```
┌─────────────┐      ┌─────────────┐
│  Desktop 1  │      │  Desktop 2  │
└──────┬──────┘      └──────┬──────┘
       │                    │
       └────────┬───────────┘
                │
       ┌────────┴────────┐
       │  Cloud Sync     │
       │  (Optional)     │
       └─────────────────┘
```

### Phase 3: Enterprise Deployment

```
┌──────────────────────────────────┐
│  Management Console (Web)        │
└────────────┬─────────────────────┘
             │
    ┌────────┴─────────┐
    │  Central Server   │
    └────────┬─────────┘
             │
  ┌──────────┼──────────┐
  │          │          │
┌─┴──┐    ┌─┴──┐    ┌─┴──┐
│ PC1 │    │ PC2 │    │ PC3 │
└────┘    └────┘    └────┘
```

---

## Appendix: Design Patterns Used

1. **Observer Pattern** - File system event notifications
2. **Strategy Pattern** - Optimization strategies
3. **Factory Pattern** - Platform-specific implementations
4. **Repository Pattern** - Data access layer
5. **Command Pattern** - File operations with undo
6. **Singleton Pattern** - Core engine instance
7. **Proxy Pattern** - gRPC communication

---

**Document Status:** Living Document  
**Next Review:** After tech stack finalization  
**Maintained By:** FlowDrive Development Team
