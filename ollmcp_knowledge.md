## Agent Delegation System Design

  Understanding the core architecture where complex tasks are decomposed into subtasks executed by 25+ specialized agents (PLANNER, EXECUTOR, SHELL_EXECUTOR, CODER, READER, DEBUGGER, etc.). Requires knowledge of task planning, agent selection, and result aggregation patterns.

  Key files: mcp_client_for_ollama/agents/delegation_client.py:33, mcp_client_for_ollama/agents/agent_config.py

## Model Context Protocol (MCP) Integration


  Working with MCP servers, tools, and multiple transport types (STDIO, SSE, Streamable HTTP). Understanding tool discovery, execution, and the MCP specification for connecting LLMs to external data sources and tools.

  Key files: mcp_client_for_ollama/server/connector.py, mcp_client_for_ollama/tools/manager.py

## Async Python Architecture

  Deep knowledge of Python's asyncio patterns, async/await, AsyncExitStack for resource management, concurrent execution with semaphores, and async generators for streaming responses. The entire codebase is async-first.

  Key files: mcp_client_for_ollama/client.py:1, mcp_client_for_ollama/agents/delegation_client.py:8

## Ollama LLM Integration

  Interfacing with Ollama for local LLM execution, model management, parameter configuration (temperature, context window, sampling), streaming responses, and handling model-specific features like thinking mode.

  Key files: mcp_client_for_ollama/models/manager.py, mcp_client_for_ollama/models/config_manager.py

## Prompt Engineering for Multi-Agent Systems

  Crafting effective system prompts for specialized agents with strict token budgets (reduced from 57K to 3K chars after discovering prompt bloat). Understanding JSON output formatting, tool protocol instructions, and agent behavior constraints.

  Key files: mcp_client_for_ollama/agents/definitions/*.json (25 agent definition files)

## Code-Based Plan Validation

  Implementing validation logic that enforces planning rules through code rather than relying solely on prompt engineering. Includes retry mechanisms with error feedback loops to guide the PLANNER toward valid task decompositions.

  Key files: mcp_client_for_ollama/agents/delegation_client.py:_validate_plan_quality(), mcp_client_for_ollama/agents/delegation_client.py:create_plan()

## Session Memory System

  Architecture for long-running sessions with persistent state including goals, features, progress tracking, test results, and domain-specific memory. Supports multi-session context that overcomes limited model context windows.

  Key files: mcp_client_for_ollama/memory/base_memory.py, mcp_client_for_ollama/memory/storage.py, mcp_client_for_ollama/memory/tools.py

## Comprehensive Trace Logging

  Detailed execution logging capturing agent interactions, tool calls, LLM responses, timing metrics, and decision trees. Critical for debugging multi-agent behavior and understanding failure modes.

  Key files: mcp_client_for_ollama/utils/trace_logger.py, trace output at /home/mcstar/Nextcloud/VTCLLC/.trace/
## Rich Terminal UI (TUI) Development

  Building interactive CLIs with Rich library (panels, syntax highlighting, progress bars), prompt-toolkit for keyboard bindings and FZF-style fuzzy completion, and dynamic status displays showing current model, tools, and metrics.

  Key files: mcp_client_for_ollama/client.py:37, mcp_client_for_ollama/utils/fzf_style_completion.py, mcp_client_for_ollama/utils/streaming.py

## Builtin Tools System

  Managing 30+ builtin tools covering file operations, Python/Bash execution, pytest integration, memory management, configuration updates, and MCP server management. Includes security controls, path validation, and user approval flows.

  Key files: mcp_client_for_ollama/tools/builtin.py:11, mcp_client_for_ollama/tools/manager.py

## Configuration Management

  JSON-based configuration for agents, servers, models, and application settings. Auto-discovery from Claude's config, project-specific .config/ directories, and dynamic runtime updates. Agent definitions are data-driven, not hardcoded.

  Key files: mcp_client_for_ollama/config/manager.py, mcp_client_for_ollama/agents/agent_config.py

## Human-in-the-Loop (HIL) Controls

  Safety mechanisms for reviewing and approving tool executions before they run. Pattern matching for dangerous operations, user prompts for confirmation, and per-tool approval rules. Essential for production safety.

  Key files: mcp_client_for_ollama/utils/hil_manager.py

## Circuit Breakers and Error Handling

  Implementing loop limits to prevent infinite agent loops, timeout management for task execution, graceful degradation when tools fail, and distinguishing between retryable and fatal errors in multi-step workflows.

  Key files: mcp_client_for_ollama/agents/definitions/shell_executor.json:19 (loop_limit), mcp_client_for_ollama/agents/delegation_client.py (retry logic)

  ---
## Multi-Modal Support 
- Handling image inputs alongside text for vision-capable models (llava, cogvlm), base64 encoding, and integrating visual analysis into the agent workflow.