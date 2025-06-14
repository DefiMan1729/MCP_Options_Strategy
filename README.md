# MCP Options Strategy Server

This repository contains the MCP server code for executing **Call and Put Options** on **Claude Desktop**. It provides a streamlined approach to managing options trading operations efficiently.

## Getting Started

### Prerequisites

- Python 3.8+ installed
- `uv` for managing dependencies

### Setting Up Your Environment

To begin, set up your virtual environment:

```bash
uv init mcp-server-demo
cd mcp-server-demo
```

Then, add MCP to your project dependencies:
```
uv add "mcp[cli]"
```
Running the Server
Once dependencies are installed, you can start the MCP Options Strategy server:
```
python mcp_server.py
```
## Server Code
``` python
# server.py
# This script initializes an MCP server for options calculations, 
# providing tools to compute breakeven points and payoffs for Call and Put options.

from mcp.server.fastmcp import FastMCP
from options_library import Put, Call

# Create an instance of the FastMCP server
# "Demo_Options" serves as the namespace for the tools
mcp = FastMCP("Demo_Options")

# Define a tool for Call Option analysis
@mcp.tool()
def call_option(strike: float, premium: float) -> dict:
    """
    Computes breakeven price and potential payoffs for a Call Option.
    
    Parameters:
        strike (float): Strike price of the Call option.
        premium (float): Premium paid to purchase the option.
    
    Returns:
        dict: A dictionary containing:
            - breakeven: Price at which the option breaks even.
            - max_profit: Theoretically infinite (since profits scale with the underlying asset price).
            - max_loss: Fixed loss, equal to the premium paid.
    """
    call = Call(strike, premium)
    return {
        "breakeven": call.breakeven(),
        "max_profit": "infinite",  # Unlimited profit potential
        "max_loss": call.premium   # Limited loss equal to the premium
    }

# Define a tool for Put Option analysis
@mcp.tool()
def put_option(strike: float, premium: float) -> dict:
    """
    Computes breakeven price and potential payoffs for a Put Option.
    
    Parameters:
        strike (float): Strike price of the Put option.
        premium (float): Premium paid to purchase the option.
    
    Returns:
        dict: A dictionary containing:
            - breakeven: Price at which the option breaks even.
            - max_profit: Maximum profit if asset price drops to zero.
            - max_loss: Fixed loss, equal to the premium paid.
    """
    put = Put(strike, premium)
    return {
        "breakeven": put.breakeven(),
        "max_profit": strike - premium,  # Profit is capped at the strike price minus the premium
        "max_loss": premium               # Limited loss equal to the premium
    }
```

## Calling MCP tools from Claude Desktop

```json
{
  "mcpServers": {
    "Demo": {
      "command": "/Users/arkaroychowdhury/.local/bin/uv",
      "args": [
        "run",
        "--with",
        "mcp[cli]",
        "mcp",
        "run",
        "/Users/arkaroychowdhury/Desktop/2025-Portfolio/Code/MCP/mcp-server-demo/server.py"
      ]
    },
    "DemoOptions": {
      "command": "/Users/arkaroychowdhury/.local/bin/uv",
      "args": [
        "run",
        "--with",
        "mcp[cli]",
        "mcp",
        "run",
        "/Users/arkaroychowdhury/Desktop/2025-Portfolio/Code/MCP/mcp-server-demo/server3.py"
      ]
    }
  }
}
```
Screenshot from Claude desktop
<img width="766" alt="Screenshot 2025-06-14 at 5 43 31 PM" src="https://github.com/user-attachments/assets/1192e51c-31e6-4261-867d-6913ca3806cc" />
