# claude-skills-gcp

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that provides comprehensive `gcloud` CLI reference material, giving Claude accurate, detailed knowledge of Google Cloud Platform commands and workflows.

## What is a Claude Code Skill?

Skills are markdown-based knowledge packs that extend Claude Code's capabilities. When a skill is installed, Claude automatically loads the relevant reference material when it detects a matching context (e.g., mentions of `gcloud`, GCP services, or cloud deployments), resulting in more accurate and detailed assistance.

## What's Included

This skill covers 21 `gcloud` command groups with dedicated reference files:

| Category | Services |
|----------|----------|
| **Auth & Config** | Authentication & ADC, CLI configuration, SDK components |
| **Compute** | Compute Engine, GKE, Cloud Run, Cloud Functions, App Engine |
| **Data** | Firestore, Cloud SQL, Cloud Storage, Pub/Sub |
| **DevOps** | Cloud Build, Artifact Registry, Secret Manager |
| **Networking** | Cloud DNS, VPC (via Compute) |
| **Observability** | Cloud Logging, Cloud Monitoring |
| **Security** | IAM & service accounts, Cloud KMS |
| **Management** | Projects, API services, emulators |

Each reference file includes command syntax, common flags, practical examples, and troubleshooting tips.

## Installation

Clone this repo, then add it to your Claude Code project settings:

```bash
git clone git@github.com:nealepetrillo/claude-skills-gcp.git
```

In your project's `.claude/settings.json`, add the skill directory path:

```json
{
  "skills": [
    "/path/to/claude-skills-gcp/.claude/skills"
  ]
}
```

Or copy the `.claude/skills/gcp` directory into your own project's `.claude/skills/` folder.

## Usage

Once installed, the skill activates automatically whenever you work with GCP in Claude Code. No special commands needed -- just ask Claude to help with any `gcloud` task:

- "Deploy this function to Cloud Run"
- "Set up a service account with minimal permissions"
- "Configure the Firestore emulator for local dev"
- "Create a Cloud Build trigger for this repo"

## Structure

```
.claude/skills/gcp/
  SKILL.md                          # Skill definition and top-level reference
  references/
    auth.md                         # Authentication & credentials
    config.md                       # CLI configuration
    projects.md                     # Project management
    iam.md                          # IAM & service accounts
    compute.md                      # Compute Engine
    container.md                    # GKE
    functions-and-run.md            # Cloud Functions & Cloud Run
    app.md                          # App Engine
    builds.md                       # Cloud Build
    storage.md                      # Cloud Storage
    firestore.md                    # Firestore
    pubsub.md                       # Pub/Sub
    sql.md                          # Cloud SQL
    secrets.md                      # Secret Manager
    logging.md                      # Cloud Logging
    monitoring.md                   # Cloud Monitoring
    artifacts.md                    # Artifact Registry
    dns.md                          # Cloud DNS
    kms.md                          # Cloud KMS
    emulators.md                    # Local dev emulators
    services.md                     # API management
    components.md                   # SDK components
```

## License

MIT
