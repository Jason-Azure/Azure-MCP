# Contributing to Azure-MCP

Thank you for your interest in contributing to Azure-MCP! This document provides guidelines and instructions for contributing to the project.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [How to Contribute](#how-to-contribute)
- [Development Guidelines](#development-guidelines)
- [Testing](#testing)
- [Submitting Changes](#submitting-changes)
- [Reporting Issues](#reporting-issues)

## Code of Conduct

This project adheres to a code of conduct that all contributors are expected to follow:

- Be respectful and inclusive
- Welcome newcomers and help them get started
- Focus on what is best for the community
- Show empathy towards other community members

## Getting Started

### Prerequisites

- Node.js 18.x or higher
- npm 9.x or higher
- Git
- Azure subscription (for testing)
- GitHub account

### Fork and Clone

1. Fork the repository on GitHub
2. Clone your fork locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/Azure-MCP.git
   cd Azure-MCP
   ```
3. Add the upstream repository:
   ```bash
   git remote add upstream https://github.com/Jason-Azure/Azure-MCP.git
   ```

### Set Up Development Environment

1. Install dependencies:
   ```bash
   npm install
   ```

2. Create a `.env` file for local testing:
   ```bash
   cp .env.example .env
   # Edit .env with your Azure credentials
   ```

3. Verify your setup:
   ```bash
   npm test
   ```

## How to Contribute

### Types of Contributions

We welcome several types of contributions:

- **Bug fixes**: Fix issues reported in GitHub Issues
- **New features**: Add new MCP servers or enhance existing ones
- **Documentation**: Improve or add documentation
- **Examples**: Add new examples or improve existing ones
- **Tests**: Add or improve test coverage

### Finding Work

- Check the [Issues](https://github.com/Jason-Azure/Azure-MCP/issues) page for open issues
- Look for issues labeled `good first issue` for beginner-friendly tasks
- Look for issues labeled `help wanted` for tasks that need contributors
- Feel free to propose new features or improvements

## Development Guidelines

### Code Style

- Follow JavaScript Standard Style
- Use meaningful variable and function names
- Add comments for complex logic
- Keep functions small and focused

**Example:**
```javascript
// Good: Clear, documented function
/**
 * Fetches resources from Azure subscription
 * @param {string} subscriptionId - Azure subscription ID
 * @returns {Promise<Array>} Array of resources
 */
async function fetchResources(subscriptionId) {
  // Implementation
}

// Bad: Unclear, undocumented function
async function fr(s) {
  // Implementation
}
```

### File Organization

```
Azure-MCP/
├── src/
│   ├── auth/          # Authentication modules
│   ├── resources/     # Resource management
│   ├── storage/       # Storage operations
│   ├── compute/       # Compute operations
│   └── utils/         # Utility functions
├── tests/             # Test files
├── examples/          # Example scripts
├── docs/              # Additional documentation
└── README.md
```

### Naming Conventions

- **Files**: Use kebab-case: `create-resource-group.js`
- **Classes**: Use PascalCase: `class ResourceManager {}`
- **Functions**: Use camelCase: `function listResources() {}`
- **Constants**: Use UPPER_SNAKE_CASE: `const MAX_RETRIES = 3`

### Error Handling

Always handle errors gracefully:

```javascript
async function safeOperation() {
  try {
    // Your operation
    const result = await riskyOperation();
    return { success: true, data: result };
  } catch (error) {
    // Log the error
    console.error('Operation failed:', error.message);
    
    // Return user-friendly error
    return {
      success: false,
      error: 'Operation failed. Please try again.',
      details: error.message
    };
  }
}
```

### Security Best Practices

1. **Never commit secrets**: Use environment variables
2. **Validate input**: Always validate and sanitize user input
3. **Use parameterized queries**: Prevent injection attacks
4. **Follow least privilege**: Request minimal Azure permissions
5. **Keep dependencies updated**: Regularly update npm packages

**Example:**
```javascript
// Good: Using environment variables
const apiKey = process.env.AZURE_API_KEY;

// Bad: Hardcoded secrets
const apiKey = 'hardcoded-secret-key'; // Never do this!
```

### Documentation

All new features and significant changes should include documentation:

1. **Code Comments**: Add JSDoc comments for functions
   ```javascript
   /**
    * Creates a new Azure resource group
    * @param {string} name - Resource group name
    * @param {string} location - Azure region
    * @returns {Promise<Object>} Created resource group
    */
   ```

2. **README Updates**: Update README.md if adding new features

3. **Examples**: Add examples to EXAMPLES.md for new functionality

4. **Q&A**: Add common questions to QA.md

## Testing

### Running Tests

```bash
# Run all tests
npm test

# Run specific test file
npm test -- tests/auth.test.js

# Run with coverage
npm run test:coverage
```

### Writing Tests

Use descriptive test names and follow the AAA pattern (Arrange, Act, Assert):

```javascript
describe('ResourceManager', () => {
  describe('listResources', () => {
    it('should return an array of resources', async () => {
      // Arrange
      const manager = new ResourceManager(mockCredential, mockSubId);
      
      // Act
      const resources = await manager.listResources();
      
      // Assert
      expect(Array.isArray(resources)).toBe(true);
      expect(resources.length).toBeGreaterThan(0);
    });
    
    it('should handle errors gracefully', async () => {
      // Arrange
      const manager = new ResourceManager(invalidCredential, mockSubId);
      
      // Act & Assert
      await expect(manager.listResources()).rejects.toThrow();
    });
  });
});
```

### Test Coverage

- Aim for at least 80% code coverage
- Test both success and error cases
- Test edge cases and boundary conditions
- Mock external dependencies (Azure API calls)

## Submitting Changes

### Before Submitting

1. **Update your fork:**
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Run tests:**
   ```bash
   npm test
   ```

3. **Lint your code:**
   ```bash
   npm run lint
   ```

4. **Update documentation:**
   - Update README.md if needed
   - Add examples if applicable
   - Update CHANGELOG.md

### Commit Messages

Write clear, descriptive commit messages:

**Format:**
```
<type>: <short summary>

<detailed description>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**
```bash
# Good commit messages
git commit -m "feat: add support for Azure Cosmos DB operations"
git commit -m "fix: handle authentication timeout errors"
git commit -m "docs: add examples for blob storage operations"

# Bad commit messages
git commit -m "updates"
git commit -m "fix stuff"
git commit -m "WIP"
```

### Creating a Pull Request

1. **Push to your fork:**
   ```bash
   git push origin feature/your-feature-name
   ```

2. **Create Pull Request on GitHub:**
   - Go to the original repository
   - Click "New Pull Request"
   - Select your fork and branch
   - Fill in the PR template

3. **PR Description should include:**
   - What changes were made
   - Why the changes were necessary
   - How to test the changes
   - Screenshots (if UI changes)
   - Related issues (use "Fixes #123")

**PR Template:**
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Code refactoring

## Testing
How to test these changes

## Checklist
- [ ] Tests pass
- [ ] Documentation updated
- [ ] Code follows style guidelines
- [ ] Commit messages are clear
```

### Code Review Process

1. A maintainer will review your PR
2. Address any feedback or requested changes
3. Push updates to the same branch
4. Once approved, a maintainer will merge your PR

## Reporting Issues

### Bug Reports

When reporting bugs, include:

1. **Description**: Clear description of the bug
2. **Steps to Reproduce**: Step-by-step instructions
3. **Expected Behavior**: What should happen
4. **Actual Behavior**: What actually happens
5. **Environment**: Node.js version, OS, Azure details
6. **Error Messages**: Full error messages and stack traces
7. **Screenshots**: If applicable

**Bug Report Template:**
```markdown
**Describe the bug**
A clear description of what the bug is.

**To Reproduce**
1. Configure MCP with '...'
2. Call function '...'
3. See error

**Expected behavior**
Should successfully list resources

**Actual behavior**
Throws authentication error

**Environment:**
- Node.js version: 18.17.0
- OS: macOS 13.4
- Azure-MCP version: 1.0.0

**Error message:**
```
Error: Authentication failed
    at ...
```

**Additional context**
Add any other context about the problem here.
```

### Feature Requests

When requesting features, include:

1. **Problem**: What problem does this solve?
2. **Proposed Solution**: How should it work?
3. **Alternatives**: Other solutions you've considered
4. **Additional Context**: Any other relevant information

## Questions?

If you have questions:

- Check the [FAQ](./QA.md)
- Search [existing issues](https://github.com/Jason-Azure/Azure-MCP/issues)
- Ask in [GitHub Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)

## Thank You!

Thank you for contributing to Azure-MCP! Your efforts help make this project better for everyone.
