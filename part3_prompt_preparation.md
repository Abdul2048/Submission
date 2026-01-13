# Part 3: Prompt Preparation

**Selected PR:** PR #1116 - "Register tools from a path"  
**PR Link:** https://github.com/FoundationAgents/MetaGPT/pull/1116

## 3.1 Repository Context

MetaGPT is a multi agent AI framework coordinating specialized agents (Product Manager, Architect, Engineer, QA) to collaborate on complex tasks. It manages agent communication, workflow execution, and tool usage, enabling developers to build automated software development tools without building coordination mechanisms from scratch. The repository targets AI researchers, software developers, and organizations building AI powered developer tools. It follows Python best practices with an agent based architecture and integrates with OpenAI and Anthropic.

## 3.2 Pull Request Description

This PR enables path based tool discovery for MetaGPT's tool registration. Previously, tools required manual registration in core code, creating tight coupling. The changes add automatic tool discovery from specified directories, enabling a plugin style architecture. Developers can place tool definitions in directories; the framework scans, validates, and registers them, preserving existing features while enabling easier extensibility.

## 3.3 Acceptance Criteria

 - When a user calls `register_tools_from_path(path)` with a valid directory path, the system should scan that directory for Python modules containing tool definitions and automatically register all discovered tools into the tool registry.

- When the system discovers a tool definition during path scanning, it should validate that the tool conforms to the expected tool interface (required methods, proper signatures, correct return types) before completing registration.

- When tools are registered through a path, they should integrate seamlessly with the existing tool recommendation system, ensuring that dynamically registered tools are available for recommendation and execution in the same way as manually registered tools.

- The implementation should handle invalid or inaccessible paths by raising clear, informative errors that help users understand what went wrong and how to fix it.

- When a tool is registered both manually and from a path, the implementation should handle the duplicate registration scenario by either allowing duplicates, raising an error, or using a precedence rule (e.g. manually registered tools override path discovered tools).

- When scanning directories, the system should handle nested directory structures by either recursively scanning all subdirectories or only the immediate directory, with clear behavior defined for package style layouts (with `__init__.py` files).

- The implementation should preserve backward compatibility, ensuring that existing manually registered tools continue to function without changes and that the tool registry fully supports both manual and path based registration approaches.

## 3.4 Edge Cases

1. **Invalid or Inaccessible Paths (Boundary Condition)**: When a user provides a path that doesn't exist, is not a directory, or is inaccessible due to permissions, the implementation should handle this gracefully by raising clear, informative errors. This edge case is important because users might provide incorrect paths or paths on different systems with different permission models.

2. **Circular Dependencies or Import Errors (Error Handling)**: When loading tools from paths, if a tool module has circular dependencies, missing dependencies, or import errors, the implementation should handle these gracefully. The system should either skip the problematic tool with a warning or raise clear errors indicating which tool failed and why, preventing one bad tool from breaking the entire registration process.

3. **Tool Interface Validation Failures (Unusual Input)**: When a discovered tool doesn't conform to the expected interface (missing required methods, incorrect signatures, wrong return types), the implementation should validate tools before registration and either reject them with clear error messages or provide helpful guidance on what needs to be fixed. This ensures only valid tools are registered and prevents runtime errors later.

4. **Large Directory Structures (Performance Consideration)**: When scanning directories with hundreds or thousands of files, the implementation should handle performance efficiently. The system should avoid loading all modules into memory at once, use efficient directory traversal, and provide progress feedback for large scans to prevent timeouts or memory issues.

## 3.5 Initial Prompt

You are tasked with implementing a path-based tool registration system for MetaGPT's tool registry. This feature will allow developers to register tools from file system paths, enabling dynamic tool discovery and a plugin like architecture for extending MetaGPT's capabilities.

### Context

MetaGPT is a multi agent AI framework that uses a tool system to provide agents with various capabilities. Currently, tools must be manually registered in the core codebase, which creates tight coupling and limits extensibility. This implementation should add path based tool registration that allows tools to be discovered and registered automatically from specified directories, while maintaining full compatibility with the existing manual registration system.

### Requirements

1. **Add `register_tools_from_path(path)` method** to the tool registry:
   - Takes a directory path (string or Path object) as argument
   - Scans the directory for Python modules containing tool definitions
   - Validates discovered tools against the expected tool interface
   - Registers valid tools in the tool registry
   - Returns information about successfully registered tools and any failures
   - Should handle errors gracefully and provide clear error messages

2. **Implement directory scanning and tool discovery**:
   - Traverse the specified directory to find Python modules
   - Handle both flat directory structures and package style layouts (with `__init__.py`)
   - Respect file system conventions (ignore `__pycache__`, handle hidden files appropriately)
   - Load Python modules and identify tool definitions within them
   - Support recursive scanning of subdirectories

3. **Implement tool validation**:
   - Validate that discovered tools conform to the expected tool interface
   - Check for required methods, proper signatures, and correct return types
   - Provide clear error messages for validation failures
   - Handle tools that partially conform but have issues

4. **Integration with existing systems**:
   - Ensure dynamically registered tools work with the tool recommendation system
   - Integrate with tool conversion utilities
   - Maintain compatibility with manually registered tools
   - Ensure tools from paths are treated identically to manually registered tools

### Implementation Guidelines

- Main implementation: `metagpt/tools/tool_registry.py`
- Follow existing code patterns and architecture
- Use type hints throughout (the codebase uses typing extensively)
- Add comprehensive docstrings
- Follow PEP 8 and the repository's style guide
- Write comprehensive unit tests for path scanning, tool discovery, validation, integration, and edge cases
- Support usage: `registry.register_tools_from_path("/path/to/custom/tools")`

Reference PR #1116 for the original implementation, but implement from scratch based on the current codebase structure. Ensure the implementation is production ready, handles all edge cases, maintains backward compatibility, and follows the repository's coding standards.
