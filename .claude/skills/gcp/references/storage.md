# gcloud storage — Cloud Storage Reference

## Overview

Cloud Storage provides object storage for unstructured data. The `gcloud storage` command group (replacing legacy `gsutil`) manages buckets and objects. Bucket names are globally unique.

## Buckets

### Create

```bash
gcloud storage buckets create gs://BUCKET_NAME \
  --location=LOCATION \
  --default-storage-class=STANDARD

# With uniform bucket-level access (recommended)
gcloud storage buckets create gs://BUCKET_NAME \
  --location=US \
  --uniform-bucket-level-access

# Regional bucket
gcloud storage buckets create gs://BUCKET_NAME \
  --location=us-central1

# Dual-region
gcloud storage buckets create gs://BUCKET_NAME \
  --location=US \
  --placement=us-central1,us-east1
```

**Storage classes:** `STANDARD`, `NEARLINE` (30-day min), `COLDLINE` (90-day min), `ARCHIVE` (365-day min)

### Manage buckets

```bash
# List buckets
gcloud storage buckets list
gcloud storage buckets list --format="table(name,location,storageClass)"

# Describe
gcloud storage buckets describe gs://BUCKET_NAME

# Update settings
gcloud storage buckets update gs://BUCKET_NAME \
  --default-storage-class=NEARLINE

# Enable versioning
gcloud storage buckets update gs://BUCKET_NAME --versioning

# Disable versioning
gcloud storage buckets update gs://BUCKET_NAME --no-versioning

# Set lifecycle rules (from JSON file)
gcloud storage buckets update gs://BUCKET_NAME \
  --lifecycle-file=lifecycle.json

# Delete (must be empty)
gcloud storage buckets delete gs://BUCKET_NAME

# Delete bucket and all contents
gcloud storage rm --recursive gs://BUCKET_NAME
```

## Objects

### Upload and download

```bash
# Upload a file
gcloud storage cp LOCAL_FILE gs://BUCKET/PATH

# Upload a directory
gcloud storage cp --recursive LOCAL_DIR gs://BUCKET/PATH

# Download a file
gcloud storage cp gs://BUCKET/PATH LOCAL_FILE

# Download a directory
gcloud storage cp --recursive gs://BUCKET/PATH LOCAL_DIR

# Copy between buckets
gcloud storage cp gs://SRC_BUCKET/FILE gs://DST_BUCKET/FILE

# Sync a directory (upload only changed files)
gcloud storage rsync LOCAL_DIR gs://BUCKET/PATH
gcloud storage rsync --recursive --delete-unmatched-destination-objects \
  LOCAL_DIR gs://BUCKET/PATH
```

### List and manage objects

```bash
# List objects
gcloud storage ls gs://BUCKET
gcloud storage ls gs://BUCKET/PREFIX/
gcloud storage ls --recursive gs://BUCKET
gcloud storage ls --long gs://BUCKET    # Includes size and date

# Get object metadata
gcloud storage objects describe gs://BUCKET/OBJECT

# Move / rename
gcloud storage mv gs://BUCKET/OLD_NAME gs://BUCKET/NEW_NAME

# Delete objects
gcloud storage rm gs://BUCKET/OBJECT
gcloud storage rm --recursive gs://BUCKET/PREFIX/

# Set metadata
gcloud storage objects update gs://BUCKET/OBJECT \
  --content-type=application/json \
  --custom-metadata=key=value
```

### Signed URLs

```bash
# Generate a signed URL (requires service account key or impersonation)
gcloud storage sign-url gs://BUCKET/OBJECT \
  --duration=1h \
  --private-key-file=KEY.json

# Using impersonation (no key file needed)
gcloud storage sign-url gs://BUCKET/OBJECT \
  --duration=1h \
  --impersonate-service-account=SA_EMAIL
```

## IAM and access control

```bash
# Get bucket IAM policy
gcloud storage buckets get-iam-policy gs://BUCKET

# Grant access
gcloud storage buckets add-iam-policy-binding gs://BUCKET \
  --member="user:email@example.com" \
  --role="roles/storage.objectViewer"

gcloud storage buckets add-iam-policy-binding gs://BUCKET \
  --member="allUsers" \
  --role="roles/storage.objectViewer"   # Make bucket public

# Remove access
gcloud storage buckets remove-iam-policy-binding gs://BUCKET \
  --member="user:email@example.com" \
  --role="roles/storage.objectViewer"
```

## Notifications (Pub/Sub)

```bash
# Create a notification for object changes
gcloud storage buckets notifications create gs://BUCKET \
  --topic=TOPIC_NAME \
  --event-types=OBJECT_FINALIZE

# List notifications
gcloud storage buckets notifications list gs://BUCKET

# Delete
gcloud storage buckets notifications delete gs://BUCKET --notification-id=ID
```

## gsutil (legacy CLI)

`gsutil` is still available and has feature parity. Common differences:

```bash
# gsutil equivalents
gsutil cp FILE gs://BUCKET/
gsutil ls gs://BUCKET
gsutil rsync -r LOCAL_DIR gs://BUCKET/PATH
gsutil mb gs://BUCKET_NAME            # Make bucket
gsutil rb gs://BUCKET_NAME            # Remove bucket
gsutil acl ch -u user@example.com:R gs://BUCKET/OBJECT  # ACL change
```

## Common patterns

### Host a static website

```bash
gcloud storage buckets create gs://www.example.com --location=US
gcloud storage buckets update gs://www.example.com \
  --web-main-page-suffix=index.html \
  --web-not-found-page=404.html
gcloud storage buckets add-iam-policy-binding gs://www.example.com \
  --member=allUsers --role=roles/storage.objectViewer
gcloud storage cp --recursive ./site/* gs://www.example.com/
```

### Lifecycle policy (lifecycle.json)

```json
{
  "rule": [
    {
      "action": {"type": "Delete"},
      "condition": {"age": 365}
    },
    {
      "action": {"type": "SetStorageClass", "storageClass": "COLDLINE"},
      "condition": {"age": 90}
    }
  ]
}
```

## Troubleshooting

- **"Bucket name already exists"** — bucket names are globally unique; choose a different name
- **"Access denied"** — check IAM bindings with `buckets get-iam-policy`; ensure uniform bucket-level access if using IAM
- **Slow uploads** — use `gcloud storage cp` (parallel by default) or `rsync` for incremental syncs
- **Cost management** — use lifecycle rules to auto-delete or transition old objects to cheaper storage classes
- **CORS issues** — set CORS config with `gcloud storage buckets update gs://BUCKET --cors-file=cors.json`
