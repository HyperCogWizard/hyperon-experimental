# Module System Architecture

The Hyperon module system implements a sophisticated hierarchical structure enabling recursive dependency loading, adaptive namespace management, and emergent cognitive patterns through modular composition.

## Module System Overview

```mermaid
graph TD
    A[Metta Runner] --> B[ModuleInitState]
    A --> C[Module Names Tree]
    A --> D[Module Vector]
    A --> E[Module Descriptors]
    
    B --> F[Loading Context]
    F --> G[Module Frames]
    G --> H[Dependency Resolution]
    
    C --> I[Hierarchical Names]
    I --> J[Name Resolution]
    J --> K[Module Location]
    
    D --> L[Loaded Modules]
    L --> M[ModId References]
    M --> N[Module Access]
    
    E --> O[Package Descriptors]
    O --> P[Version Management]
    P --> Q[Compatibility Checking]
    
    style A fill:#e1f5fe
    style B fill:#e8f5e8
    style C fill:#fff3e0
    style D fill:#fce4ec
    style E fill:#f3e5f5
```

## MettaMod Structure and Lifecycle

Each module encapsulates a complete cognitive unit with its own space, tokenizer, and dependency context.

```mermaid
classDiagram
    class MettaMod {
        -String mod_path
        -Option~PathBuf~ resource_dir
        -DynSpace space
        -Shared~Tokenizer~ tokenizer
        -Mutex~HashMap~ModId,DynSpace~~ imported_deps
        -Option~Box~ModuleLoader~~ loader
        
        +new_with_tokenizer() MettaMod
        +import_all_from_dependency() Result
        +import_dependency_as() Result
        +import_item_from_dependency_as() Result
        +add_atom() Result
        +get_resource() Resource
        +remap_imported_deps()
    }
    
    class ModuleLoader {
        <<interface>>
        +load(context: RunContext) Result
        +get_resource(key: ResourceKey) Resource
        +prepare(descriptor, mode) Option~ModuleLoader~
    }
    
    class ModuleSpace {
        -DynSpace base_space
        -HashMap imported_spaces
        +query() BindingsSet
        +add() Result
        +remove() bool
    }
    
    class RunContext {
        -Metta metta
        -ModId mod_id
        -ModuleInitState init_state
        +init_self_module()
        +load_module() ModId
        +import_all_from_dependency() Result
    }
    
    MettaMod --> ModuleSpace : contains
    MettaMod --> ModuleLoader : uses
    RunContext --> MettaMod : manages
    ModuleLoader --> RunContext : interacts with
```

## Module Loading and Initialization Flow

The module loading process follows a recursive pattern that enables complex dependency graphs and emergent module interactions.

```mermaid
sequenceDiagram
    participant C as Client Code
    participant M as Metta Runner
    participant RS as RunnerState
    participant RC as RunContext
    participant MIS as ModuleInitState
    participant ML as ModuleLoader
    participant MM as MettaMod
    
    C->>M: load_module_direct(loader, "module_name")
    M->>RS: new_with_module(TOP)
    RS->>RC: run_in_context()
    RC->>RC: normalize_module_name()
    RC->>MIS: init_module()
    
    Note over MIS: Create new ModuleInitFrame
    MIS->>RS: new_for_loading()
    RS->>RC: run_in_context(loader.load)
    RC->>ML: load(context)
    
    Note over ML: Module-specific loading logic
    ML->>RC: init_self_module(space, resource_dir)
    RC->>MM: new MettaMod instance
    ML->>RC: push_parser(MeTTa code)
    
    Note over RC: Execute module initialization
    RC->>RC: run_inline()
    RC->>RC: step() loop until complete
    
    Note over RS: Module loading complete
    RS->>MIS: finalize_loading()
    MIS->>M: merge_init_state()
    M->>M: add_module_to_name_tree()
    M->>C: return ModId
```

## Recursive Dependency Resolution

The system implements sophisticated dependency resolution with support for version constraints and hierarchical module spaces.

```mermaid
flowchart TD
    A[Module Import Request] --> B{Module Already Loaded?}
    B -->|Yes| C[Return Existing ModId]
    B -->|No| D[Parse Module Path]
    
    D --> E[Load Parent Modules]
    E --> F{Parent Exists?}
    F -->|No| G[Recursive Parent Loading]
    G --> E
    F -->|Yes| H[Resolve in Parent Context]
    
    H --> I[Check Package Info]
    I --> J[Apply Version Constraints]
    J --> K[Query Catalogs]
    
    K --> L{Module Found?}
    L -->|No| M[Resolution Error]
    L -->|Yes| N[Check Descriptor Cache]
    
    N --> O{Already Loaded?}
    O -->|Yes| P[Create Alias]
    O -->|No| Q[Initialize New Module]
    
    Q --> R[Recursive Dependency Load]
    R --> S[Module Integration]
    S --> T[Update Name Tree]
    T --> U[Return ModId]
    
    P --> U
    C --> U
    
    style A fill:#e1f5fe
    style E fill:#e8f5e8
    style H fill:#fff3e0
    style Q fill:#fce4ec
    style R fill:#f3e5f5
```

## Module Namespace and Scoping

The module system implements hierarchical namespacing with adaptive scope resolution and emergent visibility patterns.

```mermaid
stateDiagram-v2
    [*] --> TopLevel: Module Request
    TopLevel --> NameParsing: Parse Module Path
    NameParsing --> RelativeResolution: Relative Path
    NameParsing --> AbsoluteResolution: Absolute Path
    
    RelativeResolution --> ContextLookup: Current Module Context
    ContextLookup --> ParentTraversal: Search Parent Scopes
    ParentTraversal --> ContextLookup: Not Found
    ParentTraversal --> ModuleLocation: Found
    
    AbsoluteResolution --> GlobalNamespace: From Root
    GlobalNamespace --> ModuleLocation: Direct Lookup
    
    ModuleLocation --> VisibilityCheck: Check Access
    VisibilityCheck --> AccessGranted: Public/Interface
    VisibilityCheck --> AccessDenied: Private/Internal
    
    AccessGranted --> ModuleBinding: Create Reference
    ModuleBinding --> [*]: Success
    
    AccessDenied --> [*]: Error
    
    note right of ParentTraversal
        Hierarchical scope traversal:
        - Current module scope
        - Parent module scope  
        - Transitive parent scopes
        - Global namespace
    end note
    
    note right of VisibilityCheck
        Visibility qualifiers:
        - Public: Available to all
        - Interface: Exported symbols
        - Private: Internal only
        - Implementation: Hidden details
    end note
```

## Package Management Integration

The module system integrates with package management for distributed module loading and version resolution.

```mermaid
graph LR
    A[Module Request] --> B[PkgInfo Resolution]
    B --> C[Version Constraints]
    C --> D[Catalog Query]
    
    D --> E[Local Cache]
    D --> F[Remote Repositories]
    D --> G[File System]
    
    E --> H{Cache Hit?}
    H -->|Yes| I[Load from Cache]
    H -->|No| J[Fetch Required]
    
    F --> K[Network Fetch]
    K --> L[Version Validation]
    L --> M[Security Check]
    M --> N[Cache Update]
    
    G --> O[Local Module Discovery]
    O --> P[Path Resolution]
    P --> Q[Format Detection]
    
    I --> R[Module Preparation]
    N --> R
    Q --> R
    
    R --> S[Dependency Analysis]
    S --> T[Recursive Loading]
    T --> U[Module Integration]
    
    style A fill:#e1f5fe
    style B fill:#e8f5e8
    style D fill:#fff3e0
    style R fill:#fce4ec
    style T fill:#f3e5f5
```

## Module Import Patterns and Strategies

Different import patterns enable various levels of cognitive integration and namespace management.

```mermaid
flowchart TD
    A[Import Request] --> B{Import Type}
    
    B -->|import_all| C[Complete Module Import]
    B -->|import_as| D[Selective Import]
    B -->|import_item| E[Specific Item Import]
    
    C --> F[Deep Copy Module Space]
    F --> G[Strip Transitive Dependencies]
    G --> H[Add as Grounded Atom]
    H --> I[Merge Tokenizer Entries]
    I --> J[Complete Integration]
    
    D --> K[Dependency Registration]
    K --> L[Tokenizer Entry Creation]
    L --> M[Namespace Binding]
    M --> N[Controlled Access]
    
    E --> O[Item Resolution]
    O --> P{Tokenizer Entry?}
    P -->|Yes| Q[Import Token Pattern]
    P -->|No| R[Resolve as Atom]
    
    Q --> S[Direct Token Import]
    R --> T[Atom Resolution]
    T --> U[Optional Renaming]
    U --> V[Targeted Integration]
    
    J --> W[Emergent Behavior]
    N --> W
    S --> W
    V --> W
    
    style A fill:#e1f5fe
    style C fill:#e8f5e8
    style D fill:#fff3e0
    style E fill:#fce4ec
    style W fill:#f3e5f5
```

## Cognitive Module Interaction Patterns

Modules interact through cognitive patterns that enable emergent behavior and adaptive learning.

```mermaid
mindmap
  root((Module Interactions))
    Space Integration
      Hierarchical Spaces
        Base Module Space
        Imported Dependency Spaces
        Transitive Space Access
      Query Propagation
        Local Space Query
        Dependency Space Search
        Result Aggregation
    Token Sharing
      Context-Independent Tokens
        Mathematical Operations
        Logical Operators
        Basic Data Types
      Context-Dependent Tokens
        Module-Specific Functions
        Domain Operations
        Cognitive Primitives
    Dependency Patterns
      Import All
        Complete Cognitive Integration
        Emergent Behavior Patterns
        Namespace Pollution Risk
      Selective Import
        Controlled Integration
        Explicit Dependencies
        Clean Interfaces
      Item Import
        Precise Control
        Minimal Footprint
        Targeted Enhancement
    Cognitive Synergy
      Pattern Recognition
        Cross-Module Patterns
        Emergent Relationships
        Adaptive Learning
      Knowledge Integration
        Symbolic Reasoning
        Neural Processing
        Hybrid Cognition
```

## Module Resource Management

Each module can provide resources that extend beyond executable code, supporting rich cognitive environments.

```mermaid
sequenceDiagram
    participant C as Client
    participant RC as RunContext
    participant MM as MettaMod
    participant ML as ModuleLoader
    participant FS as File System
    
    C->>RC: load_resource_from_module(mod_name, resource_key)
    RC->>RC: get_module_by_name(mod_name)
    
    alt Module Already Loaded
        RC->>MM: get_resource(resource_key)
        MM->>ML: get_resource(resource_key)
        ML->>FS: load resource file
        FS-->>ML: resource data
        ML-->>MM: Resource object
        MM-->>RC: Resource object
    else Module Not Loaded
        RC->>RC: resolve_module(mod_name)
        RC->>ML: new ModuleLoader
        ML->>ML: get_resource(resource_key)
        ML->>FS: load resource file
        FS-->>ML: resource data
        ML-->>RC: Resource object
    end
    
    RC-->>C: Resource object
    
    Note over ML, FS: Resources can include:<br/>- Data files<br/>- Model weights<br/>- Configuration<br/>- Documentation<br/>- Binary assets
```

This module system architecture enables sophisticated cognitive applications through hierarchical organization, recursive dependency resolution, and adaptive integration patterns that support both symbolic reasoning and neural processing capabilities.