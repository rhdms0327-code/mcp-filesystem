# MCP Filesystem Server (Encoding Supported)

A Model Context Protocol (MCP) server for filesystem operations, optimized for intelligent interaction with large files and covering various text encodings (like UTF-8, EUC-KR).

## Quick Setup with `uvx`

You can easily run this MCP server using `uvx` (the execution tool provided by `uv`). To configure this server in your MCP client (such as Claude Desktop or Antigravity), add the following to your configuration file:

### MCP Client Configuration

Add the following JSON to your MCP settings file (For Claude Desktop):
- **macOS/Linux**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "mcp-filesystem-encoding": {
      "command": "uvx",
      "args": [
        "mcp-filesystem-encoding",
        "/absolute/path/to/allowed/dir1",
        "C:\\absolute\\path\\to\\allowed\\dir2"
      ]
    }
  }
}
```

> **Note**: You must pass the directories you want to expose to the server as arguments after the package name. The server will strictly isolate and securely restrict its operations to only these directories and their subdirectories.

## Important Variables & Usage Options

### 1. The `path` Variable
When providing root directories to the server configuration, or when using any file manipulation tools that require a `path` parameter, you must adhere to the following rules:
- **Absolute Paths Required**: All paths must be fully qualified absolute paths (e.g., `C:\Users\username\Desktop\Test` on Windows or `/Users/username/Desktop/Test` on macOS/Linux). Relative paths are not permitted and will result in errors.
- **Allowed Directories Constraint**: Any `path` passed to a tool runs through a security validator. The server will simply **deny access** to any file or directory outside the predefined roots configured during the server startup.

### 2. The `encoding` Option
This extended MCP server natively supports reading, writing, and searching files across different encodings, preventing character corruption in non-UTF-8 local environments (such as Korean `euc-kr` / `cp949`, or Japanese `shift-jis`).

The `encoding` parameter is optional (defaults to `"utf-8"`) and is readily available across the following tool operations:
- **Examine Content**: `read_file`, `read_multiple_files`, `head_file`, `tail_file`
- **Modify Content**: `write_file`, `edit_file`
- **Discover Content**: `search_files` (for precise text matching), `grep_files` (advanced Regex searches requiring decoding of target files)

**Usage Example in MCP Call**:
```json
// Example: Reading a legacy Korean document
{
  "path": "C:\\Users\\Username\\Documents\\legacy_document.txt",
  "encoding": "euc-kr"
}
```

## Available Tools Summary
- **Basic IO**: `read_file`, `write_file`, `edit_file`
- **Search**: `search_files`, `grep_files`
- **File System**: `list_directory`, `create_directory`, `directory_tree`
- **Analytics**: `compare_files`, `find_duplicate_files`

*(All internal tools strictly enforce the `path` policy boundaries and seamlessly support the `encoding` option to empower your environment without breaking localized text!)*
