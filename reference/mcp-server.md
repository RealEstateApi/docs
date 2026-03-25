# Real Estate API MCP Server - Connection Guide

This guide explains how to connect to the Real Estate API MCP servers. We provide two MCP servers:
- **Production Server**: For accessing real estate data (requires API key)
- **Developer Server**: For IDE integration with documentation and code examples (no authentication required)

## Production MCP Server

### Authentication

The production MCP server requires an API key for authentication. You can provide this key in two ways:
- **URL Parameter**: `x-api-key=YOUR_API_KEY`
- **HTTP Header**: `x-api-key: YOUR_API_KEY`

### Connection Methods

#### 1. Claude Desktop & MCP-Compatible Applications

For applications that support MCP configuration files (like Claude Desktop), add the following to your configuration:

```json
{
  "mcpServers": {
    "realestate-api-mcp": {
      "url": "https://mcp.realestateapi.com/sse",
      "headers": {
        "x-api-key": "YOUR_API_KEY"
      }
    }
  }
}
```

**Configuration file locations:**
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

#### 2. Programmatic Integration (Claude API)

When building applications that call the Claude API with MCP server support, use the following configuration:

```javascript
const mcpServers = [
  {
    type: "url",
    url: "https://mcp.realestateapi.com/sse?x-api-key=YOUR_API_KEY",
    name: "realestate-api-mcp",
    tool_configuration: {
      enabled: true
    }
  }
]
```

## Developer MCP Server (IDE Integration)

### Overview

Our developer MCP server embeds Real Estate API documentation and code examples directly in your IDE. No authentication required!

### Features
- Full API documentation accessible within your IDE
- Code examples and snippets
- Interactive assistance for API integration
- Support for queries like "update this function to call the realestateapi propertysearch dynamically"

### IDE Configuration

#### Cline, Windsurf, and Other MCP-Compatible IDEs

Add the following to your MCP configuration:

```json
{
  "mcpServers": {
    "reapi-developer": {
      "url": "https://developer.mcp.realestateapi.com/sse"
    }
  }
}
```

Once configured, you can:
- Access complete API documentation without leaving your IDE
- Get contextual code examples
- Ask for help implementing specific API calls
- Request code modifications that integrate Real Estate API endpoints

### Example Use Cases
- "Show me how to search for properties in Chicago"
- "Update this function to call the realestateapi propertysearch dynamically"
- "What parameters does the property details endpoint accept?"
- "Generate a TypeScript interface for the property response"

## Complete Configuration Example

To use both servers together (recommended for development):

```json
{
  "mcpServers": {
    "realestate-api-mcp": {
      "url": "https://mcp.realestateapi.com/sse",
      "headers": {
        "x-api-key": "YOUR_API_KEY"
      }
    },
    "reapi-developer": {
      "url": "https://developer.mcp.realestateapi.com/sse"
    }
  }
}
```

This gives you:
- **realestate-api-mcp**: Access to live real estate data
- **reapi-developer**: Documentation and code assistance in your IDE

## Claude Code Command Example

```bash
claude mcp add --transport sse realestate-api-mcp https://mcp.realestateapi.com/sse --header "X-API-Key: YOUR_API_KEY"
claude mcp add --transport sse reapi-developer https://developer.mcp.realestateapi.com/sse
```

## Compatibility

Both MCP servers are compatible with:
- Claude Desktop
- Cline
- Windsurf
- Applications using the Anthropic API with MCP support
- Any MCP-compliant client, IDE, or framework
- Custom applications implementing the MCP protocol

## Quick Start

1. **For development**: Add the developer server to your IDE configuration (no API key needed)
2. **For production data**: Obtain your API key and configure the production server
3. Restart your application/IDE after updating configuration
4. Start building with full documentation and live data access!

## Support

For additional help or to report issues, please visit our documentation at [your-support-url] or contact our support team.
