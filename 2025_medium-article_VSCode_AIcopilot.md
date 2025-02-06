# Setting up a Remote AI Code Assistant: Ollama + Continue in VS Code

# Setting up a Remote AI Code Assistant: Ollama + Continue in VS Code

![Remote Development Setup with MacStudio](https://image-url-1-here)
*Figure 1: Remote development setup with MacStudio as server and VS Code integration*


A step-by-step guide to creating a powerful, private AI coding assistant using Ollama and Continue extension in VS Code, with remote server capabilities.

## Introduction
![VS Code with Continue AI Assistant](https://image-url-2-here)
*Figure 2: VS Code environment with Continue AI extension and code suggestions*

As developers, we're always looking for ways to enhance our coding workflow while maintaining privacy and control over our tools. In this guide, I'll show you how to set up a powerful, locally-hosted AI coding assistant using Ollama models and the Continue extension for VS Code, with a unique twist: running it on a remote server while accessing it from any client machine.

## Why This Setup?

- **Privacy**: All AI operations run on your hardware
- **Performance**: Utilize powerful server hardware for AI processing
- **Flexibility**: Access from any client machine
- **Cost-effective**: Free, open-source solution
- **Customizable**: Choose and configure your own models

## System Architecture

Our setup consists of two main components:

1. **Server (MacStudio)**:
   - VS Code Server
   - Ollama server running AI models
   - Handles all AI computations

2. **Client (MacBook Air or any machine)**:
   - VS Code with remote tunnel connection
   - Continue extension
   - Connects to server for AI assistance

## Prerequisites

- Server machine (MacStudio in our case) with:
  - VS Code Server installed
  - Ollama installed
  - Sufficient RAM (32GB+ recommended)
  - GPU (optional but recommended)

- Client machine with:
  - VS Code installed
  - Continue extension installed
  - Network access to server

## Step-by-Step Setup

### 1. Server Setup (MacStudio)

First, let's configure the server components:

```bash
# Install VS Code Server
code-server --install-extension continue.continue

# Install Ollama
curl https://ollama.ai/install.sh | sh

# Configure Ollama for remote access in .zshrc
export OLLAMA_HOST=0.0.0.0

# Pull required models
ollama pull llama3.3:70b
ollama pull codestral
ollama pull nomic-embed-text
ollama pull linux6200/bge-reranker-v2-m3
```

### 2. Client Setup (MacBook Air)

On your client machine:

1. Install VS Code
2. Install Continue extension (continue.dev)
3. Set up VS Code tunnel:
   - Command Palette (Cmd + Shift + P)
   - "Remote Tunnels: New"
   - Follow authentication process

### 3. Configure Continue Extension

Create or update Continue's config.json:

```json
{
    "models": [
        {
            "title": "Llama 3.3 70B",
            "provider": "ollama",
            "model": "llama3.3:70b",
            "baseUrl": "http://[SERVER-IP]:11434",
            "apiBase": "http://[SERVER-IP]:11434"
        }
    ],
    "tabAutocompleteModel": {
        "title": "Code Completion",
        "provider": "ollama",
        "model": "codestral",
        "baseUrl": "http://[SERVER-IP]:11434",
        "apiBase": "http://[SERVER-IP]:11434"
    },
    "embeddingModel": {
        "title": "Text Embedding",
        "provider": "ollama",
        "model": "nomic-embed-text",
        "baseUrl": "http://[SERVER-IP]:11434",
        "apiBase": "http://[SERVER-IP]:11434"
    },
    "rerankingModel": {
        "title": "Reranking",
        "provider": "ollama",
        "model": "linux6200/bge-reranker-v2-m3",
        "baseUrl": "http://[SERVER-IP]:11434",
        "apiBase": "http://[SERVER-IP]:11434"
    }
}
```
_Remember to replace [SERVER-IP] with your actual server IP address in all configuration examples._

### 4. Enable Network Access

Critical step for macOS users (client):

1. Open System Settings
2. Navigate to Privacy & Security > Network
3. Find Visual Studio Code
4. Toggle ON local network access

## Model Configuration

Our setup uses specific models for different tasks:

1. **Chat Model**: Llama3.3:70b
   - Comprehensive code understanding
   - Natural language interaction
   - Complex problem-solving

2. **Autocompletion**: Codestral
   - Efficient fill-in-middle completions
   - Context-aware suggestions
   - Fast response times

3. **Embedding**: nomic-embed-text
   - Semantic code search
   - Similar code identification
   - Context understanding

4. **Reranking**: bge-reranker-v2-m3
   - Improved search results
   - Better context matching
   - Enhanced relevance sorting

## Advanced Features

Continue provides a robust tools system that enhances AI capabilities through custom function calling. Here's how different models handle tool integration:

### Built-in Tools

1. File Operations: Create, modify, and manage files
2. Git Integration: Generate commit messages, analyze diffs
3. Terminal Commands: Execute and manage CLI operations
4. Search Codebase: Find relevant code snippets
5. Workspace Management: Handle files and directories

### Model-Specific Tool Capabilities

1. Claude Models:

   - Native function calling with structured outputs
   - Support for complex multi-step reasoning
   - Can chain multiple tools together
Example:
```json
{
  "tools": {
    "terminal": {
      "description": "Run terminal commands",
      "parameters": {
        "command": "string"
      }
    }
  }
}
```
2. Ollama Models:

   - System prompt-based tool invocation
   - Supports basic command execution
   - Works best with clear, single-step instructions
Example:
```json
{
  "systemPrompt": "You can run commands using /cmd {command}",
  "tools": {
    "cmd": {
      "description": "Execute terminal command"
    }
  }
}
```
3. Custom Tool Integration
You can add your own tools by modifying Continue's config:
```json
{
  "customTools": [
    {
      "name": "database",
      "description": "Query the database",
      "parameters": {
        "query": "string",
        "database": "string"
      },
      "handler": async (params) => {
        // Custom implementation
      }
    }
  ]
}
```

### Best Practices for Tool Usage

1. Security:

   - Always validate tool inputs
   - Use permission prompts for sensitive operations
   - Limit tool access scope


2. Performance:

   - Cache frequent tool results
   - Use async operations for long-running tasks
   - Implement proper error handling


3. Integration:

   - Keep tool descriptions clear and specific
   - Test tools with different model behaviors
   - Document custom tool implementations


## Troubleshooting

Common issues and solutions:

1. **Remote Connection Errors (Issue #1090)**:
   - Problem: Continue fails to connect to remote Ollama server
   - Solution: Add `apiBase` to model configuration:
   ```json
   {
       "models": [{
           "title": "Llama 3.3 70B",
           "provider": "ollama",
           "model": "llama3.3:70b",
           "baseUrl": "http://[SERVER-IP]:11434",
           "apiBase": "http://[SERVER-IP]:11434"  // Add this line
       }]
   }
   ```
   - Remember to add this for all Ollama models in your config

2. **EHOSTUNREACH Error on macOS (Issue #1145)**:
   - Problem: Error message: "connect EHOSTUNREACH [SERVER-IP]:11434"
   - Cause: macOS security settings blocking VS Code network access
   - Solution:
     1. Open System Settings
     2. Navigate to Privacy & Security > Network
     3. Find Visual Studio Code
     4. Toggle ON local network access
   - Note: This is mandatory for macOS Ventura and later versions

3. **General Connection Issues**:
   - Verify Ollama is running: `curl http://[SERVER-IP]:11434/api/tags`
   - Check firewall settings: Allow port 11434

4. **Model Loading Issues**:
   - Verify model installation: `ollama list`
   - Check server resources
   - Enable detailed Ollama logging:
     ```bash
     # Stop any running Ollama instance first
     pkill ollama
     
     # Start Ollama with debug logging
     OLLAMA_DEBUG=1 ollama serve
     ```
   - Monitor Ollama logs: `tail -f ~/.ollama/logs/ollama.log`
   
5. **Debugging Steps**:
   - Enable Continue debug logs in settings.json:
   ```json
   {
       "continue.enableDebugLogs": true,
       "continue.showLogInConsole": true
   }
   ```
   - Check VS Code Developer Tools (Help > Toggle Developer Tools)
   - Verify network requests in Developer Tools Network tab

## Conclusion

This setup provides a powerful, private AI coding assistant that combines the best of local processing with remote accessibility. The Continue extension with Ollama offers a flexible, customizable solution that can enhance your coding workflow while maintaining control over your data and models.

## Resources

- [Continue Documentation](https://continue.dev/docs)
- [Ollama GitHub](https://github.com/ollama/ollama), (https://github.com/ollama/ollama/blob/main/README.md#quickstart)
- [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)



---
Tags: AI, Development, VS Code, Ollama, Continue, Remote Development
