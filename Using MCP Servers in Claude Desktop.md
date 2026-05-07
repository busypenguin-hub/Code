Using MCP Servers in Claude Desktop
The Model Context Protocol (MCP) was open sourced by Anthropic in November 2024 to provide users and developers with an easy way to extend the capabilities of AI-powered apps by integrating them with data sources and applications.

MCP was built to work with Anthropic's Claude Large Language Models, and most direct and feature-rich implementations of MCPs can be experienced through the Claude Desktop application on macOS or Windows.

This article walks you through a step-by-step process to install and configure MCPs on your computer.

Requirements
To follow along you need:

A Claude.ai account (MCP support is available for all account types)
The Claude Desktop app, available for macOS and Windows
uv - a Rust-based Python package manager (full installation instructions):
macOS via Homebrew:

brew install uv
Windows via WinGet:

winget install --id=astral-sh.uv  -e
Adding the fetch MCP server to Claude Desktop
The Model Context Protocol GitHub repository provides a long list of reference MCP servers you can install and experiment with. One of these is fetch, which visits a provided URL, processes its content, and converts it to Markdown for easier access.

MCPs are added to Claude Desktop through the claude_desktop_config.json file. The file location is OS-dependent:

macOS:
~/Library/Application Support/Claude/claude_desktop_config.json
Windows:
%APPDATA%\Claude\claude_desktop_config.json
If you have not used MCPs with Claude Desktop before, the file has not been created. To generate the file or get to its location, perform the following steps:

Open Claude Desktop
Go to the application menu and select "Settings"
In the Settings panel, select "Developer" in the sidebar
Click the "Edit Config" button
This will open a file browser with the claude_desktop_config.json file highlighted.

To add the fetch MCP:

Open claude_desktop_config.json in your preferred code editor
Add the following code:
{
    "mcpServers": {
        "fetch": {
            "command": "uvx",
            "args": ["mcp-server-fetch"]
        }
    }
}
 
Save the file
Reboot Claude
If there are no errors, the MCP Server has been successfully installed
Common error: uvx ENOENT
If you get a series of errors, one of them saying "uvx ENOENT" it means either uv is not installed on your system or uvx is not in the path. To fix the problem, reinstall uv following the instructions above, or alter the command property in the fetch object to show the full path to uv on your computer.

To find the path to uv on your computer, enter the following command in a terminal:

macOS:

which uv
Windows:

where uv
The resulting code in claude_desktop_config.json will look similar to this:

{
    "mcpServers": {
        "fetch": {
            "command": "/Users/your-username/.local/bin/uvx",
            "args": ["mcp-server-fetch"]
        }
    }
}
Using an MCP server in Claude Desktop
If there are no errors, prompt Claude to retrieve a URL for you, for example: "Fetch https://modelcontextprotocol.io/quickstart/user and give me the highlights."

Claude Desktop will recognize this as a task for the fetch MCP and a popup appears asking you to approve the use of the MCP. Click "Allow for this chat" or "Allow once" and the MCP server is activated.

Adding more MCP servers to Claude Desktop
claude_desktop_config.json controls what MCP servers are available to Claude Desktop. To add new MCP servers, add a new object to the mcpServers object, separating each server with a comma like this:

{
    "mcpServers": {
        "fetch": {
            "command": "uvx",
            "args": ["mcp-server-fetch"]
        },
        "filesystem": {
            "command": "npx",
            "args": [
                "-y",
                "@modelcontextprotocol/server-filesystem",
                "/Users/your-username/Desktop",
                "/Users/your-username/folder/of/your/choice"
            ]
        }
    }
}
MCP server arguments
The example above adds the filesystem MCP server to Claude. While fetch is written in Python, filesystem is written in TypeScript which is why its command is npx. This is an example of the universality of the MCP protocol: MCP is language agnostic and its configuration is universal. The JSON structure and mcpServers object is the standardized way of loading MCP servers into any MCP client, and the config can therefore be transferred without modification between clients.

For a broad range of examples of how to implement and run MCP servers using Python and TypeScript, explore the reference servers provided in the modelcontextprotocol/servers GitHub repository!