# CapRover Web App Access Fix

## Problem
- CapRover only exposes one port per app
- Original setup had MCP on port 8000 and Web UI on port 3100
- Web interface wasn't accessible when deployed to CapRover

## Solution
Created a unified server that serves both MCP and Web interface on the same port.

### How it Works

```
Single Port (80 in CapRover)
    ├── /         → Web Interface (Notes UI)
    ├── /notes    → Web API endpoints
    ├── /mcp      → MCP Protocol endpoint
    └── /health   → Health check
```

### Files Created/Modified

1. **src/remote_mcp/unified_server.py** (NEW)
   - Combines routes from both MCP and Web servers
   - Serves everything on a single port

2. **run_unified_server.py** (NEW)
   - Entry point for the unified server
   - Used locally and in Docker

3. **deploy/caprover/Dockerfile** (MODIFIED)
   - Updated to use unified server
   - Now runs `run_unified_server.py` instead of `run_server.py`

4. **run_unified.bat** (NEW)
   - Windows convenience script

### Testing Locally

```bash
# Run the unified server
python run_unified_server.py

# Access points:
# - Web UI: http://localhost:8000/
# - MCP: http://localhost:8000/mcp
```

### CapRover Deployment

After pushing these changes:
1. CapRover will rebuild automatically
2. The new unified server will be deployed
3. Both web interface and MCP will be accessible on the same domain

### Benefits

1. **Works with CapRover**: Single port limitation resolved
2. **Simpler deployment**: One app serves everything
3. **Better resource usage**: Single process instead of two
4. **Automatic HTTPS**: CapRover SSL works for both services
5. **Easier management**: One app to monitor and scale

The web interface is now accessible at your CapRover app URL!
