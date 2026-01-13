# Part 3: Prompt Preparation



**Selected PR:** PR #1116 - "Register tools from a path"  
**PR Link:** https://github.com/FoundationAgents/MetaGPT/pull/1116



## 3.1.1 Repository Context

MetaGPT is a multi-agent AI framework that helps developers build systems where multiple AI agents work together as a team. Each agent has a clear role such as Product Manager, Architect, Engineer, or QA and they collaborate to solve problems, generate code, and run workflows. Instead of relying on a single AI, the framework coordinates these specialized agents so they can communicate, share responsibilities, and complete complex tasks more effectively.

This repository is mainly meant for AI researchers, software developers, and organizations that are creating automated software development tools, task automation systems, or enterprise-level automation solutions. It is especially useful for projects that involve complex problem solving, writing and testing code, generating documentation, and managing structured workflows. Teams building AI-powered developer tools, documentation generators, or research systems benefit from MetaGPT because it allows multiple AI agents to work together in an organized way.

MetaGPT focuses on the domain of multi agent AI orchestration and automated software development. Traditional single agent AI systems often struggle with large or complex tasks because they lack specialization and coordination. MetaGPT addresses this limitation by enabling different agents to take on specific roles such as defining requirements, designing system architecture, implementing code, and collaborate through structured message passing. The framework manages agent communication, workflow execution, tool usage, and overall coordination, so developers do not have to build these mechanisms from scratch.

From a technical perspective, the repository follows Python best practices and is built around an agentbased, role driven architecture. It can coordinate complex workflows, run tasks in parallel, and lets you easily add new features through plugins. MetaGPT works with popular AI services like OpenAI and Anthropic, and comes with built in tools for web scraping, file management, and code execution. Together, these features make it a complete and flexible platform for developing advanced multi agent AI applications.


## 3.1.2 Pull Request Description

This pull request enhances MetaGPT’s tool registration system by introducing the ability to register tools using a file path. Earlier, tools had to be manually registered inside the core codebase, which created a limitation for developers who wanted to add custom tools or extend MetaGPT’s functionality. The main changes involve adding path based tool discovery and registration to the tool registry system, enabling the framework to automatically discover and register tools that exist inside specified directories.

These changes are necessary because the previous tool registration system required every tool to be manually added to the core framework code. This caused tight coupling between custom tools and the main MetaGPT codebase, making it harder for developers to add new tools without changing the framework itself. For instance, developers who wanted to create custom tools for specific use cases, such as domain specific utilities, custom API integrations, or specialized processing functions, had to edit core MetaGPT files. This increased maintenance effort and made sharing tools across different projects more difficult. Earlier, all tools needed explicit registration in the tool registry code, which limited extensibility and blocked the adoption of a plugin style architecture.

The new behavior introduces dynamic tool discovery, allowing developers to place tool definitions inside chosen directories. The framework automatically scans those directories, finds tool definitions, checks them against the required tool interface, and registers them in the tool registry. This preserves all existing tool system features, including tool recommendation, execution, and conversion, while adding a flexible way to include custom tools.This creates a plugin system where developers can build tools separately, and MetaGPT automatically finds and uses them when it runs. This makes it much easier to add new features to MetaGPT and helps build a community where people can share their tools.


## 3.1.3 Acceptance Criteria

1. When a user calls `register_tools_from_path(path)` using a valid directory path, the system should scan that directory for Python modules that contain tool definitions and automatically register every discovered tool into the tool registry.

2. When the system finds a tool definition inside a scanned directory, it should verify that the tool follows the expected tool interface, such as having required methods and valid return types, before completing registration.

3. When tools are registered through a path, they should integrate smoothly with the existing tool recommendation system, ensuring that dynamically registered tools are available for recommendation and execution in the same way as manually registered tools.

4. The path based registration process should correctly manage module loading, including proper handling of Python import paths, module initialization, and robust error handling for invalid or incorrectly defined tool modules.

5. The implementation should preserve backward compatibility, making sure that existing manually registered tools continue to function without changes and that the tool registry fully supports both manual and path based registration approaches.

6. When scanning directories to locate tools, the system should follow file system conventions, such as ignoring `__pycache__`directories, correctly handling package structures, and providing clear error messages for invalid paths or inaccessible directories.

7. The tool conversion utilities should operate correctly with tools registered from paths, ensuring that dynamically discovered tools can be converted into the appropriate format required for execution by agents.

8. The implementation should properly handle edge cases, including duplicate tool registrations where the same tool is registered manually and from a path, tools with invalid interfaces, and tools that fail to load because of missing dependencies.

9. The implementation should include thorough unit tests that cover path scanning, tool discovery, validation, registration, integration with the recommendation system, error handling, and edge cases such as invalid paths or malformed tool definitions.

10. The code should comply with the repository’s coding standards, including proper type hints, clear docstrings, adherence to the existing code style, and smooth integration with MetaGPT’s established architecture patterns.



## 3.1.4 Edge Cases

1. **Invalid or Inaccessible Paths**: What happens if a user provides a path that doesn't exist, is not a directory, or is not accessible due to permissions? The implementation should handle this gracefully by raising clear, informative errors that help users understand what went wrong and how to fix it. This edge case is important because users might provide incorrect paths or paths on different systems with different permission models.

2. **Duplicate Tool Registration**: What happens if a tool is registered both manually (in core code) and discovered from a path? The implementation needs to decide whether to allow duplicates, raise an error, or use a precedence rule (e.g., manually registered tools override path discovered tools, or vice versa). This is important for maintaining predictable behavior and avoiding conflicts.

3. **Circular Dependencies or Import Errors**: When loading tools from paths, what happens if a tool module has circular dependencies, missing dependencies, or import errors? The implementation should handle these gracefully, either by skipping the problematic tool with a warning, or by raising clear errors that indicate which tool failed and why. This prevents one bad tool from breaking the entire registration process.

4. **Tool Interface Validation Failures**: What happens if a discovered tool doesn't conform to the expected interface (missing required methods, incorrect signatures, wrong return types)? The implementation should validate tools before registration and either reject them with clear error messages or provide helpful guidance on what needs to be fixed. This ensures that only valid tools are registered and prevents runtime errors later.

5. **Nested Directory Structures**: When scanning a directory, how should the system handle nested subdirectories? Should it recursively scan all subdirectories, or only the immediate directory? The implementation should define clear behavior for nested structures and handle package style directory layouts (with `__init__.py` files) correctly.

6. **Tool Registry State During Runtime**: What happens if tools are registered from a path after the system has already started using the tool registry? Should the new tools be immediately available, or should there be a mechanism to refresh the registry? The implementation should consider whether dynamic registration during runtime is supported and how it interacts with the recommendation and execution systems.



## 3.1.5 Initial Prompt

You are tasked with implementing a path based tool registration system for MetaGPT's tool registry. This feature will allow developers to register tools from file system paths, enabling dynamic tool discovery and a plugin like architecture for extending MetaGPT's capabilities.

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
   - Handle both flat directory structures and package-style layouts (with `__init__.py`)
   - Respect file system conventions (ignore `__pycache__`, handle hidden files appropriately)
   - Load Python modules and identify tool definitions within them
   - Support recursive scanning of subdirectories (or provide option to control this)

3. **Implement tool validation**:
   - Validate that discovered tools conform to the expected tool interface
   - Check for required methods, proper signatures, and correct return types
   - Provide clear error messages for validation failures
   - Handle tools that partially conform but have issues

4. **Integration with existing systems**:
   - Ensure dynamically registered tools work with the tool recommendation system
   - Integrate with tool conversion utilities (`tool_convert.py`)
   - Maintain compatibility with manually registered tools
   - Ensure tools from paths are treated identically to manually registered tools

### Acceptance Criteria

Refer to section 3.1.3 for the complete list of acceptance criteria. Key points:
- Path-based tool discovery and registration must work correctly
- Tool validation must ensure only valid tools are registered
- Backward compatibility with manual registration must be maintained
- Integration with recommendation and conversion systems is required
- Comprehensive error handling is essential

### Edge Cases to Consider

Refer to section 3.1.4 for detailed edge cases. Pay special attention to:
- Invalid or inaccessible paths
- Duplicate tool registrations
- Import errors and missing dependencies
- Tool interface validation failures
- Nested directory structures
- Runtime registration behavior

### Implementation Guidelines

1. **File Locations**:
   - Main implementation: `metagpt/tools/tool_registry.py` (add path-based registration method)
   - Tool conversion: `metagpt/tools/tool_convert.py` (ensure compatibility with path-registered tools)
   - Tool recommendation: `metagpt/tools/tool_recommend.py` (ensure path-registered tools are included)
   - Add tests: `tests/metagpt/tools/test_tool_convert.py` and new test file for tool registry

2. **Code Style**:
   - Follow existing code patterns and architecture
   - Use type hints throughout (the codebase uses typing extensively)
   - Add comprehensive docstrings
   - Follow PEP 8 and the repository's style guide
   - Maintain consistency with MetaGPT's agent-based architecture patterns

3. **Testing Requirements**:
   - Write unit tests for path scanning and tool discovery
   - Test tool validation and error handling
   - Test integration with recommendation and conversion systems
   - Test edge cases (invalid paths, duplicate tools, import errors, etc.)
   - Ensure tests pass with the existing test suite

4. **Example Usage Pattern**:
   The implementation should support usage like this:
   ```python
   from metagpt.tools.tool_registry import ToolRegistry
   
   registry = ToolRegistry()
   
   # Register tools from a custom directory
   registry.register_tools_from_path("/path/to/custom/tools")
   
   # Tools are now available for recommendation and use
   tools = registry.get_recommended_tools(query="file operations")
   ```

### Reference Implementation

You can reference PR #1116 (https://github.com/FoundationAgents/MetaGPT/pull/1116) for the original implementation, but you should implement this from scratch based on the current codebase structure and requirements. The goal is to create a clean, maintainable implementation that integrates well with the existing codebase and supports MetaGPT's extensibility goals.

### Deliverables

1. Implementation of `register_tools_from_path()` method in the tool registry
2. Directory scanning and tool discovery logic
3. Tool validation mechanism
4. Integration with existing tool recommendation and conversion systems
5. Comprehensive unit and integration tests
6. Updated documentation or docstrings explaining the new API

Ensure that your implementation is production-ready, handles all edge cases, maintains backward compatibility, and follows the repository's coding standards and patterns.
