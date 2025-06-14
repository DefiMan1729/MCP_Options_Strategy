# MCP Options Strategy Server

This repository contains the MCP server code for executing **Call and Put Options** on **Claude Desktop**. It provides a streamlined approach to managing options trading operations efficiently.

## Getting Started

### Prerequisites

- Python 3.8+ installed
- `uv` for managing dependencies

### Setting Up Your Environment

To begin, set up your virtual environment and install uv. Then proceed as follows:

```bash
uv init mcp-server-demo
cd mcp-server-demo
```
Then add MCP to your project dependencies:
```bash
uv add "mcp[cli]"
```
To run the mcp command with uv:
```bash
uv run mcp
```
## MCP server code

```python
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
Run the MCP server
```bash
mcp install server.py
```

## Claude Desktop config file and screenshot
```JSON
{
  "mcpServers": {
    "server": {
      "command": "/Users/arkaroychowdhury/.local/bin/uv",
      "args": [
        "run",
        "--with",
        "mcp[cli]",
        "mcp",
        "run",
        "/Users/arkaroychowdhury/Desktop/2025-Portfolio/Code/MCP/mcp-server-demo/server.py"
      ]
    }
  }
}
```
<img width="766" alt="Screenshot 2025-06-14 at 5 43 31 PM" src="https://github.com/user-attachments/assets/fd6c17d5-4fec-4ecc-b3d4-f4d46b390e0f" />
