# CapRover Deployment - Unified Server

The unified server deployment allows both the MCP endpoint and Web interface to be served from a single CapRover app on the same port.

## Architecture

```
CapRover App (Port 80)
    ├── / (Web Interface)
    ├── /notes (Web API)
    ├── /mcp (MCP Endpoint)
    └── /health (Health Check)
```

## Deployment Instructions

### 1. Create CapRover App

1. Log into your CapRover dashboard
2. Click "Apps" → "One-Click Apps/Databases"
3. Search for "Create new App"
4. Enter app name (e.g., `mcp-notes`)
5. Click "Create"

### 2. Configure App Settings

1. Go to your app's settings
2. Under "App Configs":
   - Enable HTTPS (if you have SSL)
   - Set Container HTTP Port to `80`

### 3. Deploy from GitHub

1. In the app's "Deployment" tab
2. Select deployment method: "Deploy from GitHub"
3. Enter your repository URL
4. Set branch (usually `main` or `master`)
5. Click "Save and Update"
6. Configure webhook in GitHub (follow CapRover instructions)

### 4. Access Your App

Once deployed, you can access:

- **Web Interface**: `https://your-app.your-domain.com/`
- **MCP Endpoint**: `https://your-app.your-domain.com/mcp`
- **Health Check**: `https://your-app.your-domain.com/health`

## Environment Variables (Optional)

You can set these in the "App Configs" tab:

- `HOST`: Server bind address (default: `0.0.0.0`)
- `PORT`: Server port (default: `80` - don't change for CapRover)
- `LOG_LEVEL`: Logging verbosity (default: `INFO`)

## Testing the Deployment

### 1. Check Health
```bash
curl https://your-app.your-domain.com/health
```

### 2. Test Web Interface
Open `https://your-app.your-domain.com/` in your browser

### 3. Test MCP Endpoint
```bash
npx @modelcontextprotocol/inspector --url https://your-app.your-domain.com/mcp
```

### 4. Configure Claude Desktop
```json
{
  "mcpServers": {
    "remote-mcp-notes": {
      "url": "https://your-app.your-domain.com/mcp",
      "transport": {
        "type": "http",
        "config": {
          "url": "https://your-app.your-domain.com/mcp"
        }
      }
    }
  }
}
```

## Troubleshooting

### App Won't Start
1. Check CapRover logs for errors
2. Verify the Dockerfile is in `deploy/caprover/Dockerfile`
3. Ensure all required files are committed to git

### Web Interface Not Loading
1. Check that unified server is running (check logs)
2. Verify routes are configured correctly
3. Test health endpoint first

### MCP Not Working
1. Ensure the path is `/mcp` (not just `/`)
2. Check CORS settings if accessing from different domain
3. Verify with MCP Inspector first

## Benefits of Unified Deployment

1. **Single App**: Only need one CapRover app
2. **Single Port**: Works with CapRover's single-port limitation
3. **Shared Database**: MCP and Web share the same notes
4. **Simplified Management**: One app to monitor and scale
5. **Cost Effective**: Uses less resources than two separate apps

## Updating the App

When you push changes to GitHub:
1. CapRover will automatically rebuild
2. New container will be deployed with zero downtime
3. Both MCP and Web interface will be updated together

## Scaling

To scale the app:
1. Go to app settings
2. Adjust the instance count
3. CapRover will load balance between instances

Note: Since we use in-memory storage, notes won't be shared between instances. For production, consider adding a database.
