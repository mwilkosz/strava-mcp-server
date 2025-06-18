# Strava MCP Server

![Python Package](https://github.com/tomekkorbak/strava-mcp-server/workflows/Python%20Package/badge.svg)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/downloads/release/python-3100/)

A [Model Context Protocol](https://modelcontextprotocol.io/introduction) (MCP) server that provides access to the Strava API. It allows language models to query athlete activities data from the Strava API.

## What’s new? :boom: (2025-06 edition)

Heart-rate support – every activity now includes heartrate_data plus average_ / max_ / min_heartrate_bpm fields, pulled live from the Strava streams endpoint.

Example configuration shows how to launch the server from a Python virtual environment inside Claude Desktop (without uvx wrapper required).

## Available Tools

The server exposes the following tools:

### Activities Queries

- `get_activities(limit: int = 10)`: Get the authenticated athlete's recent activities
- `get_activities_by_date_range(start_date: str, end_date: str, limit: int = 30)`: Get activities within a specific date range
- `get_activity_by_id(activity_id: int)`: Get detailed information about a specific activity
- `get_recent_activities(days: int = 7, limit: int = 10)`: Get activities from the past X days

Dates should be provided in ISO format (`YYYY-MM-DD`).

## Activity Data Format

| Field | Description | Unit |
|-------|-------------|------|
| `name` | Activity name | – |
| `sport_type` | Sport | – |
| `start_date` | Start ISO-8601 | – |
| `distance_metres` | Distance | m |
| `elapsed_time_seconds` | Elapsed time | s |
| `moving_time_seconds` | Moving time | s |
| `average_speed_mps` | Avg. speed | m · s⁻¹ |
| `max_speed_mps` | Max speed | m · s⁻¹ |
| `total_elevation_gain_metres` | Elevation gain | m |
| `elev_high_metres` | High point | m |
| `elev_low_metres` | Low point | m |
| `calories` | Calories | kcal |
| `start_latlng` | Start coords | `[lat,lng]` |
| `end_latlng` | End coords | `[lat,lng]` |
| **Heart-rate extras** |||
| `average_heartrate_bpm` | Average HR | bpm |
| `max_heartrate_bpm` | Max HR | bpm |
| `min_heartrate_bpm` | Min HR | bpm |
| `heartrate_data` | Raw HR stream | array |

## Authentication

To use this server, you'll need to authenticate with the Strava API. Follow these steps:

1. Create a Strava API application:
   - Go to [Strava API Settings](https://www.strava.com/settings/api)
   - Create an application to get your Client ID and Client Secret
   - Set the Authorization Callback Domain to `localhost`

2. Get your refresh token:
   - Use the included `get_strava_token.py` script:
   ```bash
   python get_strava_token.py
   ```
   - Follow the prompts to authorize your application
   - The script will save your tokens to a `.env` file

3. Set environment variables:
   The server requires the following environment variables:
   - `STRAVA_CLIENT_ID`: Your Strava API Client ID
   - `STRAVA_CLIENT_SECRET`: Your Strava API Client Secret
   - `STRAVA_REFRESH_TOKEN`: Your Strava API Refresh Token

## Usage

## Local setup with a virtual environment (recommended)

```bash
git clone https://github.com/mwilkosz/strava-mcp-server.git
cd strava-mcp-server

python3 -m venv .venv
# macOS/Linux
source .venv/bin/activate
# Windows
.venv\Scripts\Activate.ps1

pip install -e .
```

### Run the server from the venv

```bash
python -m strava_mcp_server.server
#  or
uvicorn strava_mcp_server.server:app --reload
```

---

## Claude integration

### Claude Desktop

Edit `claude_desktop_config.json` (macOS `~/Library/Application Support/Claude/…`, Windows `%APPDATA%\Claude\…`):

```jsonc
{
  "mcpServers": {
    "strava": {
      "command": "/absolute/path/to/strava-mcp-server/.venv/bin/python",
      "args": ["-m", "strava_mcp_server.server"],
      "env": {
        "STRAVA_CLIENT_ID":     "YOUR_CLIENT_ID",
        "STRAVA_CLIENT_SECRET": "YOUR_CLIENT_SECRET",
        "STRAVA_REFRESH_TOKEN": "YOUR_REFRESH_TOKEN"
      }
    }
  }
}
```

### Claude Web

For Claude Web, you can run the server locally and connect it using the MCP extension.

## Example Queries

Once connected, you can ask Claude questions like:

- "What are my recent activities?"
- "Show me my activities from last week"
- "What was my longest run in the past month?"
- "Get details about my latest cycling activity"

## Error Handling

The server provides human-readable error messages for common issues:

- Invalid date formats
- API authentication errors
- Network connectivity problems

## License

This project is licensed under the MIT License - see the LICENSE file for details.
