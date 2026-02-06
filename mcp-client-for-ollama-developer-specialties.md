# MCP Client for Ollama - Developer Specialties Analysis

## Project Overview

**Project:** mcp-client-for-ollama (ollmcp)
**Type:** Interactive TUI client with agent delegation system
**Purpose:** Connect local Ollama LLMs to Model Context Protocol (MCP) servers, enabling advanced tool use and multi-agent workflow automation
**Version:** 0.43.5
**Architecture:** Async-first Python with 25+ specialized agents

---

## 13 Core Developer Specialties

### 1. Agent Delegation System Design

Understanding the core architecture where complex tasks are decomposed into subtasks executed by 25+ specialized agents (PLANNER, EXECUTOR, SHELL_EXECUTOR, CODER, READER, DEBUGGER, RESEARCHER, etc.). Requires knowledge of:

- Task planning and decomposition strategies
- Agent selection based on task requirements
- Result aggregation patterns
- Sequential and parallel execution models
- Agent configuration via JSON definitions

**Key Files:**
- `mcp_client_for_ollama/agents/delegation_client.py:33` - Main DelegationClient orchestrator
- `mcp_client_for_ollama/agents/agent_config.py` - Agent configuration loader
- `mcp_client_for_ollama/agents/definitions/*.json` - 25 agent definition files

**Skills Required:**
- System architecture design
- Workflow orchestration
- State management across agents
- Error propagation and handling

---

### 2. Model Context Protocol (MCP) Integration

Working with MCP servers, tools, and multiple transport types (STDIO, SSE, Streamable HTTP). Understanding:

- Tool discovery and registration
- MCP tool execution lifecycle
- Tool schema validation
- Server connection management
- Multi-server coordination

**Key Files:**
- `mcp_client_for_ollama/server/connector.py` - ServerConnector for MCP integration
- `mcp_client_for_ollama/tools/manager.py` - ToolManager for tool lifecycle
- `mcp_client_for_ollama/tools/builtin.py:11` - Builtin tools implementation

**Skills Required:**
- Protocol implementation
- Inter-process communication
- Schema validation (JSON Schema)
- Transport layer abstractions

---

### 3. Async Python Architecture

Deep knowledge of Python's asyncio patterns throughout the entire codebase:

- `async/await` patterns for concurrent operations
- `AsyncExitStack` for resource management
- Async generators for streaming
- `asyncio.Semaphore` for parallelism control
- Async context managers

**Key Files:**
- `mcp_client_for_ollama/client.py:1` - Main MCPClient with async methods
- `mcp_client_for_ollama/agents/delegation_client.py:8` - Async delegation workflow
- All agent execution flows

**Skills Required:**
- Async/await programming
- Concurrent task management
- Race condition prevention
- Async debugging techniques

---

### 4. Ollama LLM Integration

Interfacing with Ollama for local LLM execution:

- Model management and switching
- Parameter configuration (temperature, context window, top_p, top_k, etc.)
- Streaming response handling
- Model-specific features (thinking mode for deepseek-r1, qwen3, etc.)
- Token counting and context management
- Cloud model support

**Key Files:**
- `mcp_client_for_ollama/models/manager.py` - ModelManager
- `mcp_client_for_ollama/models/config_manager.py` - ModelConfigManager
- `mcp_client_for_ollama/utils/streaming.py` - StreamingManager

**Skills Required:**
- LLM API integration
- Streaming protocols
- Model parameter tuning
- Context window optimization

---

### 5. Prompt Engineering for Multi-Agent Systems

Crafting effective system prompts for specialized agents with strict token budgets:

- JSON output formatting enforcement
- Tool protocol instructions
- Agent behavior constraints
- Few-shot learning examples
- Prompt compression (reduced from 57K to 3K chars in v0.43.0)

**Key Files:**
- `mcp_client_for_ollama/agents/definitions/planner.json` - PLANNER agent prompt
- `mcp_client_for_ollama/agents/definitions/executor.json` - EXECUTOR agent prompt
- `mcp_client_for_ollama/agents/definitions/shell_executor.json` - SHELL_EXECUTOR agent prompt
- All 25 agent definition files

**Skills Required:**
- LLM prompt design
- Token budget management
- JSON output enforcement
- Instruction clarity and precision

---

### 6. Code-Based Plan Validation

Implementing validation logic that enforces planning rules through code rather than relying solely on prompt engineering:

- Plan quality validation patterns
- Retry mechanisms with error feedback loops
- Anti-pattern detection (e.g., "list + process each" split)
- Validation error messaging
- Multi-attempt planning with learning

**Key Files:**
- `mcp_client_for_ollama/agents/delegation_client.py:_validate_plan_quality()` - Validation logic
- `mcp_client_for_ollama/agents/delegation_client.py:create_plan()` - Retry loop implementation

**Skills Required:**
- Pattern recognition
- Rule-based validation
- Feedback loop design
- Error recovery strategies

---

### 7. Session Memory System

Architecture for long-running sessions with persistent state:

- Goals and features tracking
- Progress logging across sessions
- Test result persistence
- Domain-specific memory (coding, writing, music, etc.)
- Memory initialization rituals
- Project context scanning

**Key Files:**
- `mcp_client_for_ollama/memory/base_memory.py` - DomainMemory base class
- `mcp_client_for_ollama/memory/storage.py` - MemoryStorage for persistence
- `mcp_client_for_ollama/memory/tools.py` - MemoryTools for CRUD operations
- `mcp_client_for_ollama/memory/boot_ritual.py` - BootRitual for initialization
- `mcp_client_for_ollama/memory/initializer.py` - MemoryInitializer

**Skills Required:**
- Persistent state management
- Schema evolution handling
- Context compression
- Memory retrieval strategies

---

### 8. Comprehensive Trace Logging

Detailed execution logging for debugging multi-agent behavior:

- Agent interaction traces
- Tool call logging
- LLM request/response capture
- Timing metrics
- Decision tree reconstruction
- Trace file management

**Key Files:**
- `mcp_client_for_ollama/utils/trace_logger.py` - TraceLogger implementation
- Trace output at `/home/mcstar/Nextcloud/VTCLLC/.trace/`

**Skills Required:**
- Structured logging
- Performance profiling
- Debug trace design
- Log analysis

---

### 9. Rich Terminal UI (TUI) Development

Building interactive CLIs with modern styling:

- Rich library (panels, syntax highlighting, progress bars)
- prompt-toolkit for keyboard bindings
- FZF-style fuzzy completion
- Dynamic status displays
- Collapsible output management
- Markdown rendering in terminal

**Key Files:**
- `mcp_client_for_ollama/client.py:37` - MCPClient with Rich console
- `mcp_client_for_ollama/utils/fzf_style_completion.py` - FZFStyleCompleter
- `mcp_client_for_ollama/utils/streaming.py` - Streaming display
- `mcp_client_for_ollama/utils/collapsible_output.py` - CollapsibleOutput

**Skills Required:**
- Terminal UI frameworks
- ANSI escape sequences
- Interactive prompt design
- Real-time output formatting

---

### 10. Builtin Tools System

Managing 30+ builtin tools covering diverse operations:

- File operations (read, write, patch, list)
- Python/Bash execution in sandboxes
- pytest integration
- Memory management tools
- Configuration updates
- MCP server management
- Security controls and path validation
- User approval flows (HIL)

**Key Files:**
- `mcp_client_for_ollama/tools/builtin.py:11` - BuiltinToolManager with 30+ tools
- `mcp_client_for_ollama/tools/manager.py` - ToolManager orchestration

**Skills Required:**
- Tool interface design
- Sandbox security
- File system operations
- Dynamic tool loading

---

### 11. Configuration Management

JSON-based configuration for agents, servers, models, and application settings:

- Auto-discovery from Claude's config
- Project-specific `.config/` directories
- Dynamic runtime updates
- Data-driven agent definitions
- Server configuration formats
- Model parameter persistence

**Key Files:**
- `mcp_client_for_ollama/config/manager.py` - ConfigManager
- `mcp_client_for_ollama/agents/agent_config.py` - AgentConfig loader
- `~/Library/Application Support/Claude/claude_desktop_config.json` - Claude config discovery

**Skills Required:**
- Configuration schema design
- JSON parsing and validation
- Default value management
- Configuration migration

---

### 12. Human-in-the-Loop (HIL) Controls

Safety mechanisms for reviewing and approving tool executions:

- Pattern matching for dangerous operations
- User prompts for confirmation
- Per-tool approval rules
- Batch approval workflows
- Safety guardrails

**Key Files:**
- `mcp_client_for_ollama/utils/hil_manager.py` - HumanInTheLoopManager

**Skills Required:**
- Security pattern recognition
- User interaction design
- Risk assessment
- Approval workflow design

---

### 13. Circuit Breakers and Error Handling

Implementing safeguards to prevent infinite loops and handle errors gracefully:

- Loop limits to prevent infinite agent loops
- Timeout management for task execution
- Graceful degradation when tools fail
- Retryable vs. fatal error distinction
- Error propagation across agent boundaries

**Key Files:**
- `mcp_client_for_ollama/agents/definitions/shell_executor.json:19` - loop_limit configuration
- `mcp_client_for_ollama/agents/delegation_client.py` - Retry logic and error handling

**Skills Required:**
- Failure mode analysis
- Circuit breaker patterns
- Timeout strategies
- Error classification

---

## Bonus Specialties

### 14. Multi-Modal Support

Handling image inputs alongside text for vision-capable models:

- Base64 encoding for images
- Vision model integration (llava, cogvlm, bakllava, moondream)
- Image analysis in agent workflows
- Multi-modal prompt construction

**Key Files:**
- Image handling in `mcp_client_for_ollama/tools/builtin.py` (read_image tool)

---

### 15. Web Interface Development

Optional web UI for remote access:

- Server-Sent Events (SSE) for streaming
- REST API endpoints
- Session management
- Authentication
- Static file serving

**Key Files:**
- `mcp_client_for_ollama/web/app.py` - Flask/FastAPI web server
- `mcp_client_for_ollama/web/api/` - REST API endpoints
- `mcp_client_for_ollama/web/sse/` - SSE streaming
- `mcp_client_for_ollama/web/templates/` - Frontend templates

---

## Technology Stack Summary

**Core Technologies:**
- Python 3.10+ with async/await
- Ollama for local LLM execution
- Model Context Protocol (MCP)
- Rich library for TUI
- prompt-toolkit for interactive prompts
- Typer for CLI argument parsing

**Key Libraries:**
- `mcp>=1.21.0` - MCP protocol implementation
- `ollama~=0.6.0` - Ollama Python client
- `rich~=14.2.0` - Terminal formatting
- `prompt-toolkit~=3.0.52` - Interactive prompts
- `typer~=0.20.0` - CLI framework

**Development Tools:**
- pytest for testing
- UV package manager
- Git for version control

---

## Architecture Patterns

1. **Agent Delegation Pattern**: Breaking complex tasks into specialized subtasks
2. **Code Validation Over Prompting**: Using code to enforce rules when prompts fail
3. **Retry with Feedback**: Giving agents multiple attempts with error context
4. **Collapsible Output**: Managing verbose output for better UX
5. **Memory-Augmented Agents**: Persisting context across sessions
6. **Human-in-the-Loop**: Safety gates for dangerous operations

---

## Version History Context

The project has undergone significant evolution:

- **v0.35.x**: Initial agent delegation system
- **v0.42.x**: Memory system integration
- **v0.43.0**: Major prompt reduction (90% smaller), circuit breakers
- **v0.43.1**: Code-based plan validation
- **v0.43.2**: Retry loop with error feedback
- **v0.43.3**: Python sandbox clarifications
- **v0.43.4**: Explicit workflow examples
- **v0.43.5**: Loop limit increases, deployment fixes

**Key Insight**: Discovered that code validation is more reliable than prompt engineering when prompts exceed 57K chars with memory context.

---

## Related Projects

- **ollama-ocr**: Used by pdf_extract_mcp for OCR processing
- **FastMCP**: Framework for building MCP servers
- **Claude Desktop**: Original MCP configuration source
- **pdf_extract_mcp**: Example MCP server developed alongside this client

---

*Last Updated: 2026-01-09*
*Project Repository: https://github.com/jonigl/mcp-client-for-ollama*
