# Azure-MCP Q&A Document

This document provides detailed answers to common questions about using Azure-MCP servers with GitHub Copilot.

## Table of Contents

- [Getting Started](#getting-started)
- [Installation & Setup](#installation--setup)
- [Configuration](#configuration)
- [Azure Authentication](#azure-authentication)
- [Usage & Best Practices](#usage--best-practices)
- [Troubleshooting](#troubleshooting)
- [Advanced Topics](#advanced-topics)

---

## Getting Started

### Q: I'm new to MCP. Where should I start?

**A:** Follow these steps to get started:

1. **Understand MCP Basics:**
   - Read the [MCP specification](https://modelcontextprotocol.io/)
   - MCP allows AI assistants to access external tools and data
   - Servers expose capabilities, clients (like Copilot) consume them

2. **Set Up Your Environment:**
   - Install Node.js (v18+)
   - Have an Azure subscription ready
   - Install GitHub Copilot in VS Code

3. **Start Simple:**
   - Begin with read-only operations (listing resources)
   - Progress to more complex operations (creating resources)
   - Always test in a non-production environment first

### Q: What skills do I need to use Azure-MCP effectively?

**A:** You should have:

- **Basic JavaScript/Node.js knowledge** - MCP servers are typically written in JavaScript
- **Familiarity with Azure services** - Understanding of basic Azure concepts
- **Experience with GitHub Copilot** - Know how to use Copilot Chat and inline suggestions
- **Understanding of REST APIs** - Helpful for debugging and extending functionality

---

## Installation & Setup

### Q: What are the exact system requirements?

**A:** Minimum requirements:

- **Operating System:** Windows 10+, macOS 10.15+, or Linux (Ubuntu 20.04+)
- **Node.js:** Version 18.0.0 or higher
- **npm:** Version 9.0.0 or higher
- **Memory:** At least 4GB RAM
- **Disk Space:** 500MB free space for dependencies
- **Network:** Internet connection for Azure API access

### Q: I'm getting "node not found" errors. What should I do?

**A:** This means Node.js is not installed or not in your PATH.

**Solution for macOS:**
```bash
# Install using Homebrew
brew install node

# Verify installation
node --version
npm --version
```

**Solution for Windows:**
```powershell
# Download from nodejs.org and install
# Or use Chocolatey:
choco install nodejs

# Verify installation
node --version
npm --version
```

**Solution for Linux (Ubuntu/Debian):**
```bash
# Install using NodeSource
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

### Q: Can I install Azure-MCP globally?

**A:** Yes, but it's not recommended for development. Local installation per project is better:

```bash
# Global installation (not recommended)
npm install -g azure-mcp

# Local installation (recommended)
cd your-project
npm install azure-mcp
```

**Why local is better:**
- Version control per project
- Avoid dependency conflicts
- Easier team collaboration
- Matches package.json specifications

### Q: Do I need to install Azure CLI?

**A:** Not required, but highly recommended:

**Benefits of Azure CLI:**
- Easy authentication (`az login`)
- Quick resource inspection (`az resource list`)
- Scripting and automation
- Debugging authentication issues

**Installation:**
```bash
# macOS
brew install azure-cli

# Windows
winget install Microsoft.AzureCLI

# Linux
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

---

## Configuration

### Q: Where should I store my Azure credentials?

**A:** Multiple options, from most to least secure:

**Option 1: Managed Identity (Most Secure - for Azure-hosted apps)**
```javascript
// No credentials needed when running on Azure
const { DefaultAzureCredential } = require("@azure/identity");
const credential = new DefaultAzureCredential();
```

**Option 2: Azure CLI Authentication (Good for Development)**
```bash
# Login once
az login

# Credentials cached by Azure CLI
# Your app uses DefaultAzureCredential
```

**Option 3: Environment Variables (Good for CI/CD)**
```bash
# Set in your shell profile or CI/CD system
export AZURE_TENANT_ID="..."
export AZURE_CLIENT_ID="..."
export AZURE_CLIENT_SECRET="..."
```

**Option 4: .env File (Only for Local Development)**
```bash
# Create .env file (never commit to git!)
AZURE_TENANT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
AZURE_CLIENT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
AZURE_CLIENT_SECRET=your-secret-here

# Load in your code
require('dotenv').config();
```

**Option 5: Configuration File (Least Secure - Avoid)**
```javascript
// NOT RECOMMENDED - Don't hardcode credentials!
// Bad example:
const config = {
  clientId: "hardcoded-client-id",  // Never do this!
  clientSecret: "hardcoded-secret"   // Never do this!
};
```

### Q: How do I configure multiple Azure subscriptions?

**A:** Configure different MCP server instances:

```json
{
  "github.copilot.mcp.servers": {
    "azure-mcp-production": {
      "command": "node",
      "args": ["/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "prod-subscription-id",
        "MCP_SERVER_NAME": "production"
      }
    },
    "azure-mcp-development": {
      "command": "node",
      "args": ["/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "dev-subscription-id",
        "MCP_SERVER_NAME": "development"
      }
    }
  }
}
```

Then specify which one to use in Copilot Chat:
```
@copilot using azure-mcp-production, list my resources
```

### Q: What configuration options are available for the MCP server?

**A:** Common configuration environment variables:

```bash
# Required
AZURE_SUBSCRIPTION_ID=<your-subscription-id>

# Authentication (choose one method)
AZURE_TENANT_ID=<tenant-id>
AZURE_CLIENT_ID=<client-id>
AZURE_CLIENT_SECRET=<client-secret>

# Optional - Server Configuration
MCP_SERVER_PORT=3000                    # Server port
MCP_LOG_LEVEL=info                      # debug, info, warn, error
MCP_TIMEOUT=30000                       # Request timeout in ms
MCP_MAX_RETRIES=3                       # Number of retries for failed requests

# Optional - Azure Configuration
AZURE_RESOURCE_GROUP=<resource-group>   # Default resource group
AZURE_LOCATION=eastus                   # Default Azure region
AZURE_ENVIRONMENT=AzureCloud           # or AzureGovernment, AzureChina

# Optional - Caching
MCP_CACHE_ENABLED=true                  # Enable response caching
MCP_CACHE_TTL=300                       # Cache time-to-live in seconds
```

---

## Azure Authentication

### Q: What authentication methods are supported?

**A:** Azure-MCP supports all Azure Identity authentication methods:

1. **DefaultAzureCredential (Recommended):**
   - Tries multiple authentication methods automatically
   - Works locally (Azure CLI) and in cloud (Managed Identity)
   ```javascript
   const { DefaultAzureCredential } = require("@azure/identity");
   const credential = new DefaultAzureCredential();
   ```

2. **Service Principal (for Automation):**
   - Uses Client ID and Secret
   ```javascript
   const { ClientSecretCredential } = require("@azure/identity");
   const credential = new ClientSecretCredential(
     tenantId,
     clientId,
     clientSecret
   );
   ```

3. **Interactive Browser (for Desktop Apps):**
   ```javascript
   const { InteractiveBrowserCredential } = require("@azure/identity");
   const credential = new InteractiveBrowserCredential();
   ```

4. **Managed Identity (for Azure-hosted Apps):**
   ```javascript
   const { ManagedIdentityCredential } = require("@azure/identity");
   const credential = new ManagedIdentityCredential();
   ```

### Q: How do I create a service principal for Azure-MCP?

**A:** Use Azure CLI to create a service principal:

```bash
# Create service principal
az ad sp create-for-rbac \
  --name "azure-mcp-sp" \
  --role Contributor \
  --scopes /subscriptions/YOUR_SUBSCRIPTION_ID

# Output will contain:
# {
#   "appId": "CLIENT_ID",
#   "displayName": "azure-mcp-sp",
#   "password": "CLIENT_SECRET",
#   "tenant": "TENANT_ID"
# }

# Set environment variables
export AZURE_TENANT_ID="TENANT_ID"
export AZURE_CLIENT_ID="CLIENT_ID"
export AZURE_CLIENT_SECRET="CLIENT_SECRET"
export AZURE_SUBSCRIPTION_ID="YOUR_SUBSCRIPTION_ID"
```

**Security Best Practices:**
- Use least privilege (assign only necessary roles)
- Rotate secrets regularly
- Use certificate-based authentication when possible
- Enable conditional access policies

### Q: Authentication is failing with "AADSTS700016" error. What does this mean?

**A:** This error means the application (service principal) is not found in the tenant.

**Solutions:**

1. **Verify Tenant ID:**
   ```bash
   az account show --query tenantId -o tsv
   ```

2. **Check Service Principal:**
   ```bash
   az ad sp list --display-name "azure-mcp-sp"
   ```

3. **Recreate if Necessary:**
   ```bash
   az ad sp delete --id CLIENT_ID
   az ad sp create-for-rbac --name "azure-mcp-sp" --role Contributor
   ```

4. **Ensure Correct Tenant:**
   ```bash
   az login --tenant TENANT_ID
   ```

---

## Usage & Best Practices

### Q: What are the best practices for using Azure-MCP in production?

**A:** Follow these guidelines:

**1. Security:**
```javascript
// Use Managed Identity in production
const { DefaultAzureCredential } = require("@azure/identity");

// Enable HTTPS only
const serverConfig = {
  protocol: 'https',
  cert: fs.readFileSync('cert.pem'),
  key: fs.readFileSync('key.pem')
};

// Implement rate limiting
const rateLimit = {
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100  // Limit each IP to 100 requests per windowMs
};
```

**2. Error Handling:**
```javascript
// Always handle errors gracefully
async function safeAzureOperation() {
  try {
    const result = await azureClient.operation();
    return { success: true, data: result };
  } catch (error) {
    // Log error details
    console.error('Azure operation failed:', {
      message: error.message,
      code: error.code,
      statusCode: error.statusCode
    });
    
    // Return user-friendly error
    return { 
      success: false, 
      error: 'Operation failed. Please try again.' 
    };
  }
}
```

**3. Resource Management:**
```javascript
// Always clean up resources
async function processWithCleanup() {
  const client = createClient();
  try {
    await client.process();
  } finally {
    // Ensure cleanup happens even if processing fails
    await client.close();
  }
}
```

**4. Monitoring:**
```javascript
// Add logging and metrics
const { ApplicationInsights } = require('applicationinsights');
appInsights.setup(process.env.APPINSIGHTS_INSTRUMENTATIONKEY);

// Track custom metrics
appInsights.defaultClient.trackMetric({
  name: 'MCPRequestDuration',
  value: duration
});
```

### Q: How can I optimize performance?

**A:** Several strategies:

**1. Implement Caching:**
```javascript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300 }); // 5 minutes

async function getCachedResources() {
  const cacheKey = 'azure-resources';
  
  // Check cache first
  let resources = cache.get(cacheKey);
  if (resources) {
    return resources;
  }
  
  // Fetch from Azure if not cached
  resources = await fetchFromAzure();
  cache.set(cacheKey, resources);
  return resources;
}
```

**2. Use Batch Operations:**
```javascript
// Instead of individual requests
// Bad:
for (const resource of resources) {
  await client.update(resource);
}

// Good: Batch operations
await client.batchUpdate(resources);
```

**3. Parallel Requests:**
```javascript
// Execute independent operations in parallel
const [vms, storage, databases] = await Promise.all([
  client.virtualMachines.list(),
  client.storageAccounts.list(),
  client.databases.list()
]);
```

**4. Connection Pooling:**
```javascript
// Reuse connections
const client = new AzureClient({
  keepAlive: true,
  maxSockets: 50
});
```

### Q: How do I handle rate limiting from Azure APIs?

**A:** Implement exponential backoff:

```javascript
async function retryWithBackoff(operation, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await operation();
    } catch (error) {
      // Check if it's a rate limit error
      if (error.statusCode === 429 && i < maxRetries - 1) {
        // Get retry-after header or use exponential backoff
        const retryAfter = error.headers['retry-after'] || Math.pow(2, i);
        console.log(`Rate limited. Retrying after ${retryAfter}s...`);
        await sleep(retryAfter * 1000);
      } else {
        throw error;
      }
    }
  }
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Usage
const resources = await retryWithBackoff(() => 
  client.resources.list()
);
```

---

## Troubleshooting

### Q: The MCP server starts but Copilot can't connect. What should I check?

**A:** Systematic debugging steps:

**1. Verify Server is Running:**
```bash
# Check if process is running
ps aux | grep "node.*server.js"

# Check server logs
tail -f logs/mcp-server.log

# Test server manually
curl http://localhost:3000/health
```

**2. Check VS Code Configuration:**
```json
// Verify in settings.json
{
  "github.copilot.mcp.servers": {
    "azure-mcp": {
      "command": "node",  // Absolute path may be needed: "/usr/local/bin/node"
      "args": ["/absolute/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "your-id"
      }
    }
  }
}
```

**3. Enable Debug Logging:**
```bash
# Add to environment
export DEBUG=mcp:*
export MCP_LOG_LEVEL=debug

# Restart server and check logs
```

**4. Test with Simple Command:**
```javascript
// Create a test script: test-mcp.js
const { MCPClient } = require('./client');

async function test() {
  const client = new MCPClient('http://localhost:3000');
  const result = await client.ping();
  console.log('MCP Server Response:', result);
}

test().catch(console.error);
```

### Q: I'm getting "Cannot find module '@azure/identity'" errors. How do I fix this?

**A:** Install missing Azure SDK dependencies:

```bash
# Install core Azure packages
npm install @azure/identity @azure/core-client

# Install specific service SDKs as needed
npm install @azure/arm-resources      # Resource management
npm install @azure/storage-blob       # Blob storage
npm install @azure/cosmos            # Cosmos DB
npm install @azure/keyvault-secrets  # Key Vault

# Or install all at once
npm install @azure/identity \
  @azure/arm-resources \
  @azure/storage-blob \
  @azure/cosmos \
  @azure/keyvault-secrets

# Verify installation
npm list @azure/identity
```

### Q: Requests to Azure are timing out. What can I do?

**A:** Several potential solutions:

**1. Increase Timeout:**
```javascript
const client = new ResourceManagementClient(credential, subscriptionId, {
  requestOptions: {
    timeout: 60000  // 60 seconds
  }
});
```

**2. Check Network Connectivity:**
```bash
# Test Azure connectivity
curl -I https://management.azure.com/

# Check DNS resolution
nslookup management.azure.com

# Test with Azure CLI
az account show
```

**3. Check Firewall/Proxy:**
```bash
# Set proxy if needed
export HTTP_PROXY=http://proxy:port
export HTTPS_PROXY=http://proxy:port
export NO_PROXY=localhost,127.0.0.1

# Restart server
```

**4. Use Retry Policy:**
```javascript
const { ExponentialRetryPolicy } = require("@azure/core-rest-pipeline");

const retryPolicy = new ExponentialRetryPolicy({
  maxRetries: 3,
  retryDelayInMs: 1000,
  maxRetryDelayInMs: 10000
});
```

---

## Advanced Topics

### Q: Can I extend Azure-MCP with custom tools?

**A:** Yes! Create custom MCP tools:

```javascript
// custom-tool.js
class CustomAzureTool {
  constructor(client) {
    this.client = client;
  }
  
  // Define tool metadata
  getToolDefinition() {
    return {
      name: "get-cost-analysis",
      description: "Analyze Azure costs for a resource group",
      parameters: {
        type: "object",
        properties: {
          resourceGroup: {
            type: "string",
            description: "The resource group name"
          },
          timeframe: {
            type: "string",
            enum: ["7days", "30days", "90days"],
            description: "Analysis timeframe"
          }
        },
        required: ["resourceGroup"]
      }
    };
  }
  
  // Implement tool logic
  async execute(params) {
    const { resourceGroup, timeframe = "30days" } = params;
    
    // Your custom logic here
    const costs = await this.analyzeCosts(resourceGroup, timeframe);
    
    return {
      resourceGroup,
      timeframe,
      totalCost: costs.total,
      breakdown: costs.breakdown,
      currency: "USD"
    };
  }
  
  async analyzeCosts(resourceGroup, timeframe) {
    // Implement cost analysis logic
    // This could use Azure Cost Management APIs
    // ...
    return {
      total: 1234.56,
      breakdown: [
        { service: "Virtual Machines", cost: 800.00 },
        { service: "Storage", cost: 234.56 },
        { service: "Networking", cost: 200.00 }
      ]
    };
  }
}

// Register the tool
module.exports = CustomAzureTool;
```

### Q: How do I implement custom caching strategies?

**A:** Example with Redis:

```javascript
const redis = require('redis');
const client = redis.createClient();

class CachedAzureClient {
  constructor(azureClient) {
    this.azure = azureClient;
    this.cache = client;
  }
  
  async getResourceWithCache(resourceId) {
    const cacheKey = `resource:${resourceId}`;
    
    // Try cache first
    const cached = await this.cache.get(cacheKey);
    if (cached) {
      return JSON.parse(cached);
    }
    
    // Fetch from Azure
    const resource = await this.azure.resources.get(resourceId);
    
    // Cache for 5 minutes
    await this.cache.setEx(
      cacheKey, 
      300, 
      JSON.stringify(resource)
    );
    
    return resource;
  }
  
  async invalidateCache(resourceId) {
    const cacheKey = `resource:${resourceId}`;
    await this.cache.del(cacheKey);
  }
}
```

### Q: Can I use Azure-MCP with TypeScript?

**A:** Absolutely! Here's a TypeScript example:

```typescript
// azure-mcp.ts
import { DefaultAzureCredential } from "@azure/identity";
import { ResourceManagementClient } from "@azure/arm-resources";
import { MCPServer } from "./mcp-server";

interface AzureMCPConfig {
  subscriptionId: string;
  resourceGroup?: string;
  location?: string;
}

class AzureMCPServer {
  private client: ResourceManagementClient;
  private config: AzureMCPConfig;
  
  constructor(config: AzureMCPConfig) {
    this.config = config;
    const credential = new DefaultAzureCredential();
    this.client = new ResourceManagementClient(
      credential, 
      config.subscriptionId
    );
  }
  
  async listResources(): Promise<Resource[]> {
    const resources: Resource[] = [];
    for await (const resource of this.client.resources.list()) {
      resources.push({
        id: resource.id!,
        name: resource.name!,
        type: resource.type!,
        location: resource.location!
      });
    }
    return resources;
  }
}

interface Resource {
  id: string;
  name: string;
  type: string;
  location: string;
}

export { AzureMCPServer, AzureMCPConfig, Resource };
```

### Q: How do I monitor and log MCP server activity?

**A:** Implement comprehensive logging:

```javascript
const winston = require('winston');
const { ApplicationInsights } = require('applicationinsights');

// Configure Winston logger
const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ 
      filename: 'logs/error.log', 
      level: 'error' 
    }),
    new winston.transports.File({ 
      filename: 'logs/combined.log' 
    }),
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});

// Configure Application Insights
if (process.env.APPINSIGHTS_INSTRUMENTATIONKEY) {
  ApplicationInsights.setup()
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .start();
}

// Middleware for request logging
function logRequest(req, res, next) {
  const startTime = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - startTime;
    logger.info('Request completed', {
      method: req.method,
      path: req.path,
      statusCode: res.statusCode,
      duration,
      userAgent: req.get('user-agent')
    });
  });
  
  next();
}

module.exports = { logger, logRequest };
```

---

## Need More Help?

If your question isn't answered here:

1. **Check the main README:** [README.md](./README.md)
2. **Search existing issues:** [GitHub Issues](https://github.com/Jason-Azure/Azure-MCP/issues)
3. **Ask in discussions:** [GitHub Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)
4. **Review Azure documentation:** [Azure SDK for JavaScript](https://docs.microsoft.com/azure/developer/javascript/)
5. **Read MCP specification:** [Model Context Protocol](https://modelcontextprotocol.io/)

---

*Last updated: 2025-11-17*
