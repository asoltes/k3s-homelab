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

## Git Workflow

### Branch Management

**IMPORTANT**: The Copilot CLI should **NEVER** create branches. All changes must be committed directly to the `main` branch.

### Commit Workflow

When making changes, follow this workflow:

1. **Make changes** to the required files
2. **Stage changes**: `git add .` or `git add <specific-files>`
3. **Commit changes**: `git commit -m "descriptive message"`
4. **Request approval** from the user before pushing
5. **Push to main**: `git push origin main` (only after user approval)

### Example Workflow

```bash
# After making changes
git add .
git commit -m "feat: add new application configuration"

# Ask user: "Ready to push to main?"
# Wait for approval

git push origin main
```

### Critical Rules

- ❌ **NEVER** create a new branch
- ❌ **NEVER** use `git checkout -b` or `git branch`
- ✅ **ALWAYS** work directly on main
- ✅ **ALWAYS** request approval before pushing
- ✅ **ALWAYS** use clear, descriptive commit messages

---

## Kubernetes Operations

### Force Delete a Stuck Namespace

When a namespace is stuck in `Terminating` state due to finalizers, follow these steps:

**1. Remove the namespace finalizer:**
```bash
kubectl get namespace <ns> -o json | jq '.spec.finalizers = []' | kubectl replace --raw "/api/v1/namespaces/<ns>/finalize" -f -
```

**2. If CRD resources (e.g., ArgoCD Applications) still have finalizers, patch each one:**
```bash
for app in $(kubectl get <crd-resource> -n <ns> --no-headers -o custom-columns=":metadata.name"); do
  kubectl patch <crd-resource> "$app" -n <ns> -p '{"metadata":{"finalizers":[]}}' --type=merge
done
```

**Example — ArgoCD namespace cleanup:**
```bash
# Step 1: Remove namespace finalizer
kubectl get namespace argocd -o json | jq '.spec.finalizers = []' | kubectl replace --raw "/api/v1/namespaces/argocd/finalize" -f -

# Step 2: Remove finalizers from remaining ArgoCD Application resources
for app in $(kubectl get applications.argoproj.io -n argocd --no-headers -o custom-columns=":metadata.name"); do
  kubectl patch application.argoproj.io "$app" -n argocd -p '{"metadata":{"finalizers":[]}}' --type=merge
done
```

> **Note:** After removing the namespace finalizer, CRD-owned resources with their own finalizers (`resources-finalizer.argocd.argoproj.io`) may also need to be patched before the namespace fully terminates.

---

*Last updated: March 21, 2026*
