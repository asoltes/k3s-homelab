# Agent Configuration

## GitHub Copilot CLI Model Configuration

This document outlines the configuration for GitHub Copilot CLI to ensure it uses only Claude Sonnet models.

### Model Selection

GitHub Copilot CLI is configured to use **Claude Sonnet 4.5** exclusively for all interactions.

### Configuration

To ensure the Copilot CLI uses only Claude Sonnet models, set the following in your configuration:

```bash
# Set model preference for GitHub Copilot CLI
gh copilot config set model claude-sonnet-4.5
```

Alternatively, you can set the model via environment variable:

```bash
export GITHUB_COPILOT_MODEL=claude-sonnet-4.5
```

### Verification

To verify the current model configuration:

```bash
gh copilot config get model
```

### Available Claude Sonnet Models

- **claude-sonnet-4.5** (Recommended) - Latest and most capable Sonnet model
- **claude-sonnet-3.5** - Previous generation Sonnet model

### Model Capabilities

Claude Sonnet models provide:
- Advanced reasoning and problem-solving
- Comprehensive code understanding and generation
- Support for multiple programming languages
- Kubernetes and infrastructure-as-code expertise
- Balanced performance and cost efficiency

### Best Practices

1. Always use the latest Sonnet version for optimal performance
2. Keep your GitHub CLI updated to access new model versions
3. Monitor model usage and performance metrics
4. Report any issues or unexpected behavior to your admin

---

*Last updated: March 21, 2026*
