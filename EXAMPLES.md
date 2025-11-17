# Azure-MCP Examples

This document provides comprehensive examples of using Azure-MCP servers with GitHub Copilot to interact with Azure services.

## Table of Contents

- [Setup Examples](#setup-examples)
- [Authentication Examples](#authentication-examples)
- [Resource Management Examples](#resource-management-examples)
- [Storage Examples](#storage-examples)
- [Compute Examples](#compute-examples)
- [Database Examples](#database-examples)
- [Monitoring and Logging Examples](#monitoring-and-logging-examples)
- [Security Examples](#security-examples)

---

## Setup Examples

### Example 1: Basic Project Setup

```javascript
// package.json - Define your project dependencies
{
  "name": "azure-mcp-project",
  "version": "1.0.0",
  "description": "Azure MCP integration project",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  },
  "dependencies": {
    // Core Azure authentication library
    "@azure/identity": "^4.0.0",
    
    // Azure SDK for resource management
    "@azure/arm-resources": "^5.2.0",
    
    // Azure Storage services
    "@azure/storage-blob": "^12.17.0",
    
    // Environment variable management
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.7.0"
  }
}
```

### Example 2: Environment Configuration

```javascript
// config.js - Centralized configuration management
require('dotenv').config();

/**
 * Configuration object for Azure-MCP
 * Loads settings from environment variables with sensible defaults
 */
const config = {
  // Azure Authentication Configuration
  azure: {
    // Required: Your Azure subscription ID
    subscriptionId: process.env.AZURE_SUBSCRIPTION_ID,
    
    // Required for service principal auth
    tenantId: process.env.AZURE_TENANT_ID,
    clientId: process.env.AZURE_CLIENT_ID,
    clientSecret: process.env.AZURE_CLIENT_SECRET,
    
    // Optional: Default resource group for operations
    defaultResourceGroup: process.env.AZURE_RESOURCE_GROUP || 'default-rg',
    
    // Optional: Default Azure region
    defaultLocation: process.env.AZURE_LOCATION || 'eastus'
  },
  
  // MCP Server Configuration
  mcp: {
    // Server listening port
    port: parseInt(process.env.MCP_SERVER_PORT) || 3000,
    
    // Log level: 'debug', 'info', 'warn', 'error'
    logLevel: process.env.MCP_LOG_LEVEL || 'info',
    
    // Request timeout in milliseconds
    timeout: parseInt(process.env.MCP_TIMEOUT) || 30000,
    
    // Maximum number of retry attempts
    maxRetries: parseInt(process.env.MCP_MAX_RETRIES) || 3
  },
  
  // Feature Flags
  features: {
    // Enable response caching
    cachingEnabled: process.env.MCP_CACHE_ENABLED === 'true',
    
    // Cache time-to-live in seconds
    cacheTTL: parseInt(process.env.MCP_CACHE_TTL) || 300
  }
};

/**
 * Validates that all required configuration is present
 * @throws {Error} If required configuration is missing
 */
function validateConfig() {
  const required = ['azure.subscriptionId'];
  
  for (const key of required) {
    const value = key.split('.').reduce((obj, k) => obj?.[k], config);
    if (!value) {
      throw new Error(`Missing required configuration: ${key}`);
    }
  }
}

// Validate on module load
validateConfig();

module.exports = config;
```

---

## Authentication Examples

### Example 3: Using DefaultAzureCredential (Recommended)

```javascript
// auth/default-credential.js
const { DefaultAzureCredential } = require("@azure/identity");

/**
 * DefaultAzureCredential automatically tries multiple authentication methods:
 * 1. Environment variables (AZURE_TENANT_ID, AZURE_CLIENT_ID, AZURE_CLIENT_SECRET)
 * 2. Managed Identity (when running on Azure)
 * 3. Azure CLI (az login)
 * 4. Azure PowerShell
 * 5. Interactive browser (as last resort)
 * 
 * This is the recommended approach as it works in both development and production.
 */
async function createDefaultCredential() {
  try {
    // Create credential with optional configuration
    const credential = new DefaultAzureCredential({
      // Optional: Exclude specific authentication methods
      excludeEnvironmentCredential: false,
      excludeManagedIdentityCredential: false,
      excludeAzureCliCredential: false,
      
      // Optional: Retry configuration
      retryOptions: {
        maxRetries: 3,
        retryDelayInMs: 1000
      }
    });
    
    // Test the credential by getting a token
    console.log('Testing Azure authentication...');
    const token = await credential.getToken('https://management.azure.com/.default');
    console.log('✓ Authentication successful!');
    console.log(`Token expires at: ${new Date(token.expiresOnTimestamp)}`);
    
    return credential;
  } catch (error) {
    console.error('❌ Authentication failed:', error.message);
    console.error('\nTroubleshooting tips:');
    console.error('1. Run "az login" if using Azure CLI');
    console.error('2. Set environment variables: AZURE_TENANT_ID, AZURE_CLIENT_ID, AZURE_CLIENT_SECRET');
    console.error('3. Check that your service principal has correct permissions');
    throw error;
  }
}

module.exports = { createDefaultCredential };
```

### Example 4: Service Principal Authentication

```javascript
// auth/service-principal.js
const { ClientSecretCredential } = require("@azure/identity");

/**
 * Service Principal authentication is ideal for:
 * - Automated scripts and CI/CD pipelines
 * - Production applications
 * - Scenarios where interactive login is not possible
 * 
 * Prerequisites:
 * 1. Create a service principal: az ad sp create-for-rbac --name "my-sp"
 * 2. Assign appropriate roles: az role assignment create --assignee <appId> --role Contributor
 */
class ServicePrincipalAuth {
  constructor(tenantId, clientId, clientSecret) {
    // Validate required parameters
    if (!tenantId || !clientId || !clientSecret) {
      throw new Error('Missing required authentication parameters');
    }
    
    this.credential = new ClientSecretCredential(
      tenantId,
      clientId,
      clientSecret,
      {
        // Optional: Configure authentication authority
        authorityHost: 'https://login.microsoftonline.com',
        
        // Optional: Enable logging for debugging
        loggingOptions: {
          allowLoggingAccountIdentifiers: true
        }
      }
    );
  }
  
  /**
   * Get the credential object for use with Azure SDKs
   */
  getCredential() {
    return this.credential;
  }
  
  /**
   * Verify that authentication is working
   */
  async verify() {
    try {
      // Try to get a token for Azure Resource Manager
      const token = await this.credential.getToken(
        'https://management.azure.com/.default'
      );
      
      return {
        success: true,
        expiresOn: new Date(token.expiresOnTimestamp),
        message: 'Service principal authentication verified successfully'
      };
    } catch (error) {
      return {
        success: false,
        error: error.message,
        message: 'Service principal authentication failed'
      };
    }
  }
}

module.exports = ServicePrincipalAuth;
```

---

## Resource Management Examples

### Example 5: List All Azure Resources

```javascript
// resources/list-resources.js
const { ResourceManagementClient } = require("@azure/arm-resources");
const { createDefaultCredential } = require("../auth/default-credential");

/**
 * Lists all resources in an Azure subscription
 * Demonstrates pagination and filtering
 */
async function listAllResources(subscriptionId) {
  // Create authenticated client
  const credential = await createDefaultCredential();
  const client = new ResourceManagementClient(credential, subscriptionId);
  
  console.log('Fetching Azure resources...\n');
  
  // Collection to store all resources
  const resources = [];
  
  try {
    // Iterate through all resources (handles pagination automatically)
    for await (const resource of client.resources.list()) {
      resources.push({
        // Resource identifier
        id: resource.id,
        
        // Resource name
        name: resource.name,
        
        // Resource type (e.g., Microsoft.Compute/virtualMachines)
        type: resource.type,
        
        // Azure region where resource is deployed
        location: resource.location,
        
        // Resource group name
        resourceGroup: resource.id.split('/')[4],
        
        // Tags associated with the resource
        tags: resource.tags || {}
      });
    }
    
    // Display summary
    console.log(`Found ${resources.length} resources\n`);
    
    // Group resources by type
    const resourcesByType = resources.reduce((acc, resource) => {
      acc[resource.type] = (acc[resource.type] || 0) + 1;
      return acc;
    }, {});
    
    console.log('Resources by type:');
    Object.entries(resourcesByType)
      .sort(([, a], [, b]) => b - a) // Sort by count descending
      .forEach(([type, count]) => {
        console.log(`  ${type}: ${count}`);
      });
    
    return resources;
  } catch (error) {
    console.error('Failed to list resources:', error.message);
    throw error;
  }
}

// Example usage
if (require.main === module) {
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID;
  listAllResources(subscriptionId)
    .then(resources => {
      // Export to JSON for further processing
      console.log('\nExporting to resources.json...');
      require('fs').writeFileSync(
        'resources.json',
        JSON.stringify(resources, null, 2)
      );
    })
    .catch(console.error);
}

module.exports = { listAllResources };
```

### Example 6: Create a Resource Group

```javascript
// resources/create-resource-group.js
const { ResourceManagementClient } = require("@azure/arm-resources");

/**
 * Creates a new resource group in Azure
 * Resource groups are logical containers for Azure resources
 */
async function createResourceGroup(credential, subscriptionId, options) {
  const client = new ResourceManagementClient(credential, subscriptionId);
  
  // Default options
  const config = {
    name: options.name || 'my-resource-group',
    location: options.location || 'eastus',
    tags: options.tags || {
      environment: 'development',
      project: 'azure-mcp',
      createdBy: 'azure-mcp-server'
    }
  };
  
  console.log(`Creating resource group: ${config.name}`);
  console.log(`Location: ${config.location}`);
  
  try {
    // Check if resource group already exists
    const exists = await client.resourceGroups.checkExistence(config.name);
    
    if (exists) {
      console.log('⚠️  Resource group already exists');
      
      // Get existing resource group details
      const existing = await client.resourceGroups.get(config.name);
      return {
        created: false,
        resourceGroup: existing,
        message: 'Resource group already exists'
      };
    }
    
    // Create the resource group
    const result = await client.resourceGroups.createOrUpdate(
      config.name,
      {
        location: config.location,
        tags: config.tags
      }
    );
    
    console.log('✓ Resource group created successfully');
    console.log(`  ID: ${result.id}`);
    console.log(`  Location: ${result.location}`);
    console.log(`  Provisioning State: ${result.properties.provisioningState}`);
    
    return {
      created: true,
      resourceGroup: result,
      message: 'Resource group created successfully'
    };
  } catch (error) {
    console.error('❌ Failed to create resource group:', error.message);
    throw error;
  }
}

module.exports = { createResourceGroup };
```

---

## Storage Examples

### Example 7: Upload Files to Azure Blob Storage

```javascript
// storage/upload-blob.js
const { BlobServiceClient } = require("@azure/storage-blob");
const fs = require('fs');
const path = require('path');

/**
 * Uploads a file to Azure Blob Storage
 * Demonstrates blob upload with progress tracking and metadata
 */
class BlobUploader {
  constructor(connectionString) {
    // Create BlobServiceClient from connection string
    this.blobServiceClient = BlobServiceClient.fromConnectionString(connectionString);
  }
  
  /**
   * Upload a single file to a container
   * @param {string} containerName - Name of the container
   * @param {string} filePath - Local file path to upload
   * @param {string} blobName - Name for the blob (optional, defaults to filename)
   * @param {object} options - Additional upload options
   */
  async uploadFile(containerName, filePath, blobName = null, options = {}) {
    try {
      // Use filename as blob name if not specified
      blobName = blobName || path.basename(filePath);
      
      console.log(`Uploading ${filePath} to ${containerName}/${blobName}...`);
      
      // Get container client (creates container if it doesn't exist)
      const containerClient = this.blobServiceClient.getContainerClient(containerName);
      await containerClient.createIfNotExists({
        access: options.publicAccess || 'container' // 'blob', 'container', or undefined for private
      });
      
      // Get blob client for the specific blob
      const blockBlobClient = containerClient.getBlockBlobClient(blobName);
      
      // Get file stats for metadata
      const stats = fs.statSync(filePath);
      
      // Upload the file with metadata and options
      const uploadResponse = await blockBlobClient.uploadFile(filePath, {
        // Metadata to attach to the blob
        metadata: {
          originalName: path.basename(filePath),
          uploadedAt: new Date().toISOString(),
          fileSize: stats.size.toString(),
          ...options.metadata
        },
        
        // HTTP headers for the blob
        blobHTTPHeaders: {
          blobContentType: options.contentType || this.getContentType(filePath),
          blobCacheControl: options.cacheControl || 'public, max-age=3600'
        },
        
        // Progress callback
        onProgress: (progress) => {
          const percent = ((progress.loadedBytes / stats.size) * 100).toFixed(2);
          process.stdout.write(`\rProgress: ${percent}%`);
        }
      });
      
      console.log(`\n✓ Upload successful!`);
      console.log(`  Request ID: ${uploadResponse.requestId}`);
      console.log(`  Blob URL: ${blockBlobClient.url}`);
      console.log(`  ETag: ${uploadResponse.etag}`);
      
      return {
        success: true,
        url: blockBlobClient.url,
        etag: uploadResponse.etag,
        requestId: uploadResponse.requestId
      };
    } catch (error) {
      console.error(`\n❌ Upload failed: ${error.message}`);
      throw error;
    }
  }
  
  /**
   * Upload multiple files to a container
   */
  async uploadMultipleFiles(containerName, filePaths) {
    const results = [];
    
    for (const filePath of filePaths) {
      try {
        const result = await this.uploadFile(containerName, filePath);
        results.push({ filePath, ...result });
      } catch (error) {
        results.push({ 
          filePath, 
          success: false, 
          error: error.message 
        });
      }
    }
    
    // Summary
    const successful = results.filter(r => r.success).length;
    console.log(`\nUpload summary: ${successful}/${filePaths.length} files uploaded successfully`);
    
    return results;
  }
  
  /**
   * Determine content type based on file extension
   */
  getContentType(filePath) {
    const ext = path.extname(filePath).toLowerCase();
    const contentTypes = {
      '.txt': 'text/plain',
      '.html': 'text/html',
      '.css': 'text/css',
      '.js': 'application/javascript',
      '.json': 'application/json',
      '.png': 'image/png',
      '.jpg': 'image/jpeg',
      '.jpeg': 'image/jpeg',
      '.gif': 'image/gif',
      '.pdf': 'application/pdf',
      '.zip': 'application/zip'
    };
    return contentTypes[ext] || 'application/octet-stream';
  }
}

module.exports = BlobUploader;
```

### Example 8: Download and List Blobs

```javascript
// storage/download-blob.js
const { BlobServiceClient } = require("@azure/storage-blob");
const fs = require('fs');
const path = require('path');

/**
 * Download blobs from Azure Blob Storage
 */
class BlobDownloader {
  constructor(connectionString) {
    this.blobServiceClient = BlobServiceClient.fromConnectionString(connectionString);
  }
  
  /**
   * List all blobs in a container
   */
  async listBlobs(containerName, options = {}) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    
    console.log(`Listing blobs in container: ${containerName}`);
    const blobs = [];
    
    // List blobs with optional prefix filter
    for await (const blob of containerClient.listBlobsFlat({
      prefix: options.prefix // Filter by prefix (e.g., 'images/')
    })) {
      blobs.push({
        name: blob.name,
        size: blob.properties.contentLength,
        contentType: blob.properties.contentType,
        lastModified: blob.properties.lastModified,
        // Metadata attached to the blob
        metadata: blob.metadata
      });
      
      console.log(`  - ${blob.name} (${this.formatBytes(blob.properties.contentLength)})`);
    }
    
    console.log(`\nTotal: ${blobs.length} blobs`);
    return blobs;
  }
  
  /**
   * Download a blob to local file
   */
  async downloadBlob(containerName, blobName, downloadPath) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    const blobClient = containerClient.getBlobClient(blobName);
    
    console.log(`Downloading ${blobName}...`);
    
    try {
      // Ensure download directory exists
      const dir = path.dirname(downloadPath);
      if (!fs.existsSync(dir)) {
        fs.mkdirSync(dir, { recursive: true });
      }
      
      // Download blob to file
      await blobClient.downloadToFile(downloadPath);
      
      // Get file stats
      const stats = fs.statSync(downloadPath);
      
      console.log(`✓ Downloaded successfully`);
      console.log(`  Saved to: ${downloadPath}`);
      console.log(`  Size: ${this.formatBytes(stats.size)}`);
      
      return {
        success: true,
        path: downloadPath,
        size: stats.size
      };
    } catch (error) {
      console.error(`❌ Download failed: ${error.message}`);
      throw error;
    }
  }
  
  /**
   * Format bytes to human-readable string
   */
  formatBytes(bytes) {
    if (bytes === 0) return '0 Bytes';
    const k = 1024;
    const sizes = ['Bytes', 'KB', 'MB', 'GB', 'TB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return Math.round(bytes / Math.pow(k, i) * 100) / 100 + ' ' + sizes[i];
  }
}

module.exports = BlobDownloader;
```

---

## Compute Examples

### Example 9: List Virtual Machines

```javascript
// compute/list-vms.js
const { ComputeManagementClient } = require("@azure/arm-compute");

/**
 * List all virtual machines in a subscription
 * Provides detailed VM information including status
 */
async function listVirtualMachines(credential, subscriptionId) {
  const client = new ComputeManagementClient(credential, subscriptionId);
  
  console.log('Fetching virtual machines...\n');
  
  const vms = [];
  
  try {
    // List all VMs across all resource groups
    for await (const vm of client.virtualMachines.listAll()) {
      // Get instance view for detailed status
      const resourceGroup = vm.id.split('/')[4];
      const instanceView = await client.virtualMachines.instanceView(
        resourceGroup,
        vm.name
      );
      
      // Extract power state
      const powerState = instanceView.statuses
        .find(status => status.code.startsWith('PowerState/'))
        ?.code.split('/')[1] || 'unknown';
      
      vms.push({
        // Basic information
        name: vm.name,
        id: vm.id,
        location: vm.location,
        resourceGroup: resourceGroup,
        
        // Hardware configuration
        vmSize: vm.hardwareProfile?.vmSize,
        
        // OS information
        osType: vm.storageProfile?.osDisk?.osType,
        imageReference: vm.storageProfile?.imageReference,
        
        // Network information
        networkInterfaces: vm.networkProfile?.networkInterfaces?.map(ni => ni.id),
        
        // Status
        powerState: powerState,
        provisioningState: vm.provisioningState,
        
        // Tags
        tags: vm.tags || {}
      });
      
      // Display VM info
      console.log(`VM: ${vm.name}`);
      console.log(`  Resource Group: ${resourceGroup}`);
      console.log(`  Location: ${vm.location}`);
      console.log(`  Size: ${vm.hardwareProfile?.vmSize}`);
      console.log(`  Power State: ${powerState}`);
      console.log(`  OS: ${vm.storageProfile?.osDisk?.osType}`);
      console.log('');
    }
    
    console.log(`Total VMs: ${vms.length}\n`);
    
    // Group by power state
    const byPowerState = vms.reduce((acc, vm) => {
      acc[vm.powerState] = (acc[vm.powerState] || 0) + 1;
      return acc;
    }, {});
    
    console.log('VMs by power state:');
    Object.entries(byPowerState).forEach(([state, count]) => {
      console.log(`  ${state}: ${count}`);
    });
    
    return vms;
  } catch (error) {
    console.error('Failed to list VMs:', error.message);
    throw error;
  }
}

module.exports = { listVirtualMachines };
```

---

## Database Examples

### Example 10: Query Azure Cosmos DB

```javascript
// database/cosmos-query.js
const { CosmosClient } = require("@azure/cosmos");

/**
 * Query documents from Azure Cosmos DB
 * Demonstrates various query patterns and best practices
 */
class CosmosDBHelper {
  constructor(endpoint, key) {
    // Create Cosmos DB client
    this.client = new CosmosClient({ endpoint, key });
  }
  
  /**
   * Query documents with filters and pagination
   */
  async queryDocuments(databaseId, containerId, query, parameters = []) {
    const container = this.client
      .database(databaseId)
      .container(containerId);
    
    console.log(`Querying container: ${containerId}`);
    console.log(`Query: ${query}`);
    
    try {
      // Execute query with parameters to prevent injection
      const { resources: items } = await container.items
        .query({
          query: query,
          parameters: parameters
        })
        .fetchAll();
      
      console.log(`Found ${items.length} documents\n`);
      return items;
    } catch (error) {
      console.error('Query failed:', error.message);
      throw error;
    }
  }
  
  /**
   * Example: Find documents by category
   */
  async findByCategory(databaseId, containerId, category) {
    const query = 'SELECT * FROM c WHERE c.category = @category';
    const parameters = [{ name: '@category', value: category }];
    
    return this.queryDocuments(databaseId, containerId, query, parameters);
  }
  
  /**
   * Example: Find documents created after a date
   */
  async findRecentDocuments(databaseId, containerId, afterDate) {
    const query = `
      SELECT * FROM c 
      WHERE c._ts > @timestamp 
      ORDER BY c._ts DESC
    `;
    const parameters = [
      { name: '@timestamp', value: Math.floor(afterDate.getTime() / 1000) }
    ];
    
    return this.queryDocuments(databaseId, containerId, query, parameters);
  }
  
  /**
   * Insert or update a document
   */
  async upsertDocument(databaseId, containerId, document) {
    const container = this.client
      .database(databaseId)
      .container(containerId);
    
    try {
      // Upsert will create if doesn't exist, update if it does
      const { resource: item } = await container.items.upsert(document);
      
      console.log(`✓ Document upserted: ${item.id}`);
      return item;
    } catch (error) {
      console.error('Upsert failed:', error.message);
      throw error;
    }
  }
}

module.exports = CosmosDBHelper;
```

---

## Monitoring and Logging Examples

### Example 11: Application Insights Integration

```javascript
// monitoring/app-insights.js
const appInsights = require('applicationinsights');

/**
 * Configure Application Insights for monitoring and logging
 * Tracks requests, dependencies, exceptions, and custom events
 */
class AppInsightsMonitor {
  constructor(instrumentationKey) {
    // Setup Application Insights
    appInsights.setup(instrumentationKey)
      .setAutoDependencyCorrelation(true)
      .setAutoCollectRequests(true)
      .setAutoCollectPerformance(true)
      .setAutoCollectExceptions(true)
      .setAutoCollectDependencies(true)
      .setAutoCollectConsole(true)
      .setUseDiskRetryCaching(true)
      .setSendLiveMetrics(false)
      .start();
    
    this.client = appInsights.defaultClient;
    console.log('✓ Application Insights configured');
  }
  
  /**
   * Track a custom event
   */
  trackEvent(name, properties = {}, measurements = {}) {
    this.client.trackEvent({
      name: name,
      properties: properties,
      measurements: measurements
    });
  }
  
  /**
   * Track a custom metric
   */
  trackMetric(name, value, properties = {}) {
    this.client.trackMetric({
      name: name,
      value: value,
      properties: properties
    });
  }
  
  /**
   * Track an exception
   */
  trackException(error, properties = {}) {
    this.client.trackException({
      exception: error,
      properties: properties
    });
  }
  
  /**
   * Track a dependency call (e.g., Azure API call)
   */
  trackDependency(name, data, duration, success) {
    this.client.trackDependency({
      name: name,
      data: data,
      duration: duration,
      success: success,
      dependencyTypeName: 'Azure API'
    });
  }
  
  /**
   * Wrapper to track execution time of async functions
   */
  async trackExecutionTime(operationName, operation) {
    const startTime = Date.now();
    let success = true;
    
    try {
      const result = await operation();
      return result;
    } catch (error) {
      success = false;
      this.trackException(error, { operation: operationName });
      throw error;
    } finally {
      const duration = Date.now() - startTime;
      this.trackMetric(`${operationName}_duration`, duration);
      this.trackEvent(operationName, { success: success.toString() });
    }
  }
}

module.exports = AppInsightsMonitor;
```

---

## Security Examples

### Example 12: Azure Key Vault Integration

```javascript
// security/key-vault.js
const { SecretClient } = require("@azure/keyvault-secrets");
const { DefaultAzureCredential } = require("@azure/identity");

/**
 * Manage secrets using Azure Key Vault
 * Best practice for storing sensitive configuration
 */
class KeyVaultHelper {
  constructor(vaultUrl) {
    // Create credential and client
    const credential = new DefaultAzureCredential();
    this.client = new SecretClient(vaultUrl, credential);
    this.vaultUrl = vaultUrl;
  }
  
  /**
   * Get a secret from Key Vault
   */
  async getSecret(secretName) {
    try {
      console.log(`Retrieving secret: ${secretName}`);
      
      // Get the secret
      const secret = await this.client.getSecret(secretName);
      
      console.log(`✓ Secret retrieved successfully`);
      console.log(`  Version: ${secret.properties.version}`);
      console.log(`  Created: ${secret.properties.createdOn}`);
      
      // Return the secret value
      return secret.value;
    } catch (error) {
      console.error(`Failed to get secret '${secretName}':`, error.message);
      throw error;
    }
  }
  
  /**
   * Set a secret in Key Vault
   */
  async setSecret(secretName, secretValue, options = {}) {
    try {
      console.log(`Setting secret: ${secretName}`);
      
      // Set the secret with optional metadata
      const result = await this.client.setSecret(secretName, secretValue, {
        // Content type (e.g., 'text/plain', 'application/json')
        contentType: options.contentType,
        
        // Tags for categorization
        tags: options.tags,
        
        // Enable/disable the secret
        enabled: options.enabled !== false,
        
        // Expiration date
        expiresOn: options.expiresOn,
        
        // Not valid before date
        notBefore: options.notBefore
      });
      
      console.log(`✓ Secret set successfully`);
      console.log(`  Version: ${result.properties.version}`);
      
      return result;
    } catch (error) {
      console.error(`Failed to set secret '${secretName}':`, error.message);
      throw error;
    }
  }
  
  /**
   * List all secrets in the vault
   */
  async listSecrets() {
    console.log(`Listing secrets in vault: ${this.vaultUrl}`);
    const secrets = [];
    
    try {
      // List secret properties (not the values!)
      for await (const secretProperties of this.client.listPropertiesOfSecrets()) {
        secrets.push({
          name: secretProperties.name,
          enabled: secretProperties.enabled,
          createdOn: secretProperties.createdOn,
          updatedOn: secretProperties.updatedOn,
          expiresOn: secretProperties.expiresOn
        });
        
        console.log(`  - ${secretProperties.name}`);
      }
      
      console.log(`\nTotal secrets: ${secrets.length}`);
      return secrets;
    } catch (error) {
      console.error('Failed to list secrets:', error.message);
      throw error;
    }
  }
  
  /**
   * Delete a secret
   */
  async deleteSecret(secretName) {
    try {
      console.log(`Deleting secret: ${secretName}`);
      
      // Begin delete operation (soft delete)
      const poller = await this.client.beginDeleteSecret(secretName);
      
      // Wait for deletion to complete
      await poller.pollUntilDone();
      
      console.log(`✓ Secret deleted successfully`);
      console.log(`  Note: Secret is soft-deleted and can be recovered`);
      
      return true;
    } catch (error) {
      console.error(`Failed to delete secret '${secretName}':`, error.message);
      throw error;
    }
  }
}

module.exports = KeyVaultHelper;
```

---

## Complete Example: Full Application

### Example 13: Azure Resource Manager with MCP Integration

```javascript
// app.js - Complete example application
require('dotenv').config();
const { createDefaultCredential } = require('./auth/default-credential');
const { listAllResources } = require('./resources/list-resources');
const BlobUploader = require('./storage/upload-blob');
const { listVirtualMachines } = require('./compute/list-vms');

/**
 * Main application demonstrating Azure-MCP integration
 */
class AzureMCPApp {
  constructor() {
    this.subscriptionId = process.env.AZURE_SUBSCRIPTION_ID;
    this.storageConnectionString = process.env.AZURE_STORAGE_CONNECTION_STRING;
  }
  
  /**
   * Initialize the application
   */
  async initialize() {
    try {
      console.log('Initializing Azure-MCP Application...\n');
      
      // Authenticate with Azure
      this.credential = await createDefaultCredential();
      console.log('✓ Authentication successful\n');
      
      return true;
    } catch (error) {
      console.error('❌ Initialization failed:', error.message);
      return false;
    }
  }
  
  /**
   * Run all examples
   */
  async runExamples() {
    console.log('='.repeat(60));
    console.log('Azure-MCP Examples');
    console.log('='.repeat(60) + '\n');
    
    try {
      // Example 1: List all resources
      console.log('\n--- Example 1: List All Resources ---');
      const resources = await listAllResources(this.subscriptionId);
      console.log(`Found ${resources.length} resources`);
      
      // Example 2: List virtual machines
      console.log('\n--- Example 2: List Virtual Machines ---');
      const vms = await listVirtualMachines(this.credential, this.subscriptionId);
      console.log(`Found ${vms.length} virtual machines`);
      
      // Example 3: Upload to blob storage (if connection string available)
      if (this.storageConnectionString) {
        console.log('\n--- Example 3: Upload to Blob Storage ---');
        const uploader = new BlobUploader(this.storageConnectionString);
        // Note: You would need actual files to upload
        console.log('Blob uploader ready');
      }
      
      console.log('\n' + '='.repeat(60));
      console.log('All examples completed successfully!');
      console.log('='.repeat(60));
      
    } catch (error) {
      console.error('\n❌ Error running examples:', error.message);
      throw error;
    }
  }
}

// Run the application if executed directly
if (require.main === module) {
  const app = new AzureMCPApp();
  
  app.initialize()
    .then(success => {
      if (success) {
        return app.runExamples();
      } else {
        process.exit(1);
      }
    })
    .catch(error => {
      console.error('Application error:', error);
      process.exit(1);
    });
}

module.exports = AzureMCPApp;
```

---

## Usage with GitHub Copilot

When using these examples with GitHub Copilot:

1. **Copy the examples** you need into your project
2. **Configure authentication** using environment variables or Azure CLI
3. **Ask Copilot** natural language questions like:
   - "How do I list all my storage accounts?"
   - "Show me how to create a new virtual machine"
   - "Help me upload multiple files to blob storage"
4. **Let MCP provide context** - The server will provide Azure-specific information
5. **Copilot will suggest** code based on these examples and your specific needs

---

*For more information, see [README.md](./README.md) and [QA.md](./QA.md)*
