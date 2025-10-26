# MCP Client Setup Guide

This document explains how to use the YouTube Transcript MCP Server with MCP clients such as Claude Desktop, Claude Code, and Cursor.

## Claude Desktop Configuration

### 1. Configuration File Location

Claude Desktop's configuration file is located at:

- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

### 2. Configuration Methods

#### Method 1: With Go Runtime Environment

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "go",
      "args": ["run", "/path/to/youtube-mcp/cmd/mcp/main.go"],
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true",
        "YOUTUBE_DEFAULT_LANGUAGES": "en,ja,es,fr,de"
      }
    }
  }
}
```

**Note**: Claude Desktop requires an STDIO mode MCP server (`cmd/mcp/main.go`), not the HTTP server (`cmd/server/main.go`).

#### Method 2: Using Pre-built Binary

First, build the STDIO mode binary:

```bash
cd /path/to/youtube-mcp
go build -o youtube-mcp-stdio ./cmd/mcp/
```

Then add to the configuration file:

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-mcp-stdio",
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true",
        "YOUTUBE_DEFAULT_LANGUAGES": "en,ja"
      }
    }
  }
}
```

#### Method 3: Using Docker

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-p", "8080:8080",
        "--env-file", "/path/to/youtube-mcp/.env",
        "youtube-transcript-mcp:latest"
      ]
    }
  }
}
```

### 3. Environment Variable Configuration

Important environment variables:

- `YOUTUBE_DEFAULT_LANGUAGES`: Default subtitle languages (comma-separated)
- `CACHE_ENABLED`: Enable/disable caching
- `LOG_LEVEL`: Log level (debug, info, warn, error)
- `YOUTUBE_REQUEST_TIMEOUT`: Request timeout
- `YOUTUBE_RATE_LIMIT_PER_MINUTE`: Rate limit per minute

### 4. Advanced Configuration

Using proxy:

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-transcript-mcp",
      "env": {
        "YOUTUBE_PROXY_URL": "http://proxy.example.com:8080",
        "YOUTUBE_ENABLE_PROXY_ROTATION": "true",
        "YOUTUBE_PROXY_LIST": "http://proxy1.com:8080,http://proxy2.com:8080"
      }
    }
  }
}
```

Enabling authentication:

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-transcript-mcp",
      "env": {
        "SECURITY_ENABLE_AUTH": "true",
        "SECURITY_API_KEYS": "your-secret-api-key-here"
      }
    }
  }
}
```

## Claude Code Configuration

Claude Code (claude.ai/code) automatically detects MCP servers.

### 1. Configuration Methods

#### Method 1: Using Go Runtime Environment

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "go",
      "args": ["run", "/path/to/youtube-mcp/cmd/mcp/main.go"],
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true",
        "YOUTUBE_DEFAULT_LANGUAGES": "en,ja"
      }
    }
  }
}
```

#### Method 2: Using Pre-compiled Binary

```bash
# First build the binary
cd /path/to/youtube-mcp
make build
```

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-transcript-mcp",
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true"
      }
    }
  }
}
```

### 2. Claude Code Features

- Automatic MCP server detection and integration
- Real-time subtitle fetching and processing
- Parallel processing of multiple videos

## Cursor Configuration

Cursor supports MCP servers.

### 1. Configuration Method

1. Open Cursor settings (macOS: `Cmd+,`, Windows/Linux: `Ctrl+,`)
2. Search for "MCP" or "Model Context Protocol"
3. Add the following configuration:

#### Using Go Runtime Environment

```json
{
  "mcp.servers": {
    "youtube-transcript": {
      "command": "go",
      "args": ["run", "/path/to/youtube-mcp/cmd/server/main.go"],
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true",
        "YOUTUBE_DEFAULT_LANGUAGES": "en,ja"
      }
    }
  }
}
```

#### Using Pre-compiled Binary

```json
{
  "mcp.servers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-transcript-mcp",
      "env": {
        "LOG_LEVEL": "info",
        "CACHE_ENABLED": "true"
      }
    }
  }
}
```

### 2. Cursor Use Cases

- Automatically add video content to code comments
- Extract code snippets from tutorial videos
- Reflect technical explanation video summaries in documentation

## Usage

After restarting MCP clients (Claude Desktop, Claude Code, Cursor), the following tools will be available:

### 1. Get Video Subtitles

```text
Please get the subtitles in Japanese for this YouTube video: https://www.youtube.com/watch?v=VIDEO_ID
```

### 2. Batch Get Multiple Video Subtitles

```text
Please get subtitles for all of the following videos:
- https://www.youtube.com/watch?v=VIDEO_ID1
- https://www.youtube.com/watch?v=VIDEO_ID2
```

### 3. Translate Subtitles

```text
Please translate the subtitles for this video from English to Japanese:
https://www.youtube.com/watch?v=VIDEO_ID
```

### 4. Check Available Languages

```text
Please tell me what subtitle languages are available for this video:
https://www.youtube.com/watch?v=VIDEO_ID
```

### 5. Get Subtitles in SRT Format

```text
Please get the subtitles for this video in SRT format:
https://www.youtube.com/watch?v=VIDEO_ID
```

## Troubleshooting

### Server Won't Start

1. Check if Go is installed:
   ```bash
   go version
   ```

2. Check if dependencies are installed:
   ```bash
   cd /path/to/youtube-mcp
   make deps
   ```

3. Check if port is not in use:
   ```bash
   lsof -i :8080
   ```

### Cannot Get Subtitles

1. Check if the video has subtitles
2. Check if it's not a private video or region-restricted
3. Check if you're not hitting rate limits

### Log Checking

Enable debug logging:

```json
{
  "mcpServers": {
    "youtube-transcript": {
      "command": "/path/to/youtube-mcp/youtube-transcript-mcp",
      "env": {
        "LOG_LEVEL": "debug"
      }
    }
  }
}
```

## Security Notes

- When using API keys, use `.env` files instead of directly writing them in environment variables
- When using proxies, use trusted proxy services
- Comply with YouTube's Terms of Service

## Support

If you encounter issues, please check:

1. [README.md](../README.md) - Basic usage instructions
2. [GitHub Issues](https://github.com/yourusername/youtube-transcript-mcp/issues) - Known issues
3. Log files - Debug information
