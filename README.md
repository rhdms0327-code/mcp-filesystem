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
        "C:\\Users\\username\\Desktop\\Test",
        "C:\\Users\\username\\Documents\\Projects"
      ]
    }
  }
}
```

> **Note**: The arguments provided after the package name act as your **Allowed Root Directories**.
> - **Multiple Paths Supported**: You can specify as many root directories as you need by adding them as separate string arguments in the `args` array (e.g., `"C:\\dir1"`, `"C:\\dir2"`, etc.).
> - **Security Boundary**: The MCP server will strictly restrict its file operations (read, write, search) to only these listed directories and their subdirectories.

## Important Variables & Usage Guidelines

### 1. Using the `path` Variable (Config & Tools)
- **At Server Setup**: Provide the allowed root paths in your `args` list (as shown above). 
- **During Tool Execution**: When the MCP client (AI) uses tools like `read_file` or `search_files`, it must specify the fully qualified absolute `path` (e.g., `C:\Users\username\Desktop\Test\file.txt`). If the AI requests a path outside of the configured root directories, the server will **deny access**.

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
