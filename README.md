# CKAN

CKAN is an open-source data management platform for publishing, cataloging, discovering, and sharing datasets through public or private data portals.

It provides dataset metadata management, organizations and groups, search, file storage, structured data APIs, authentication, permissions, activity tracking, background jobs, theming, and a mature extension framework.

## Overview

CKAN is designed for organizations that need a searchable and programmable catalog of datasets and related resources.

A typical CKAN deployment stores catalog metadata in PostgreSQL, indexes searchable content in Apache Solr, uses Redis for shared state and background-job queues, and optionally enables FileStore and DataStore services for uploaded and queryable data.

The platform is suitable for:

- Government open-data portals
- Research data catalogs
- Humanitarian data platforms
- Enterprise data catalogs
- Public transparency portals
- Institutional data publishing
- API-driven data discovery
- Internal data inventories

## Features

### Dataset Catalog

- Dataset creation and management
- Metadata fields
- Resource attachments
- External resource URLs
- Tags
- Groups
- Organizations
- Dataset relationships
- Revision and activity history
- Dataset search
- Faceted filtering
- Sorting and pagination

### Organizations and Permissions

- Organization-based publishing
- User membership
- Organization administrators
- Editors and members
- Private datasets
- Dataset ownership
- Fine-grained authorization
- Sysadmin capabilities
- Custom authorization through extensions

### Search

- Apache Solr indexing
- Full-text dataset search
- Faceted search
- Tag filtering
- Organization filtering
- Group filtering
- Resource-format filtering
- Custom searchable metadata
- Search-index rebuild commands

### FileStore

CKAN can store uploaded resources and images through its FileStore system.

Capabilities include:

- Dataset resource uploads
- Group and organization images
- User avatars
- Site branding files
- Configurable upload limits
- MIME type restrictions
- Local filesystem storage
- Pluggable storage adapters

Recent CKAN versions support configurable storage abstractions, allowing extensions to provide alternative storage backends such as cloud object storage.

### DataStore

The optional DataStore extension stores structured resource data in PostgreSQL.

It provides:

- Tabular data storage
- Row-level search
- Filtering
- Sorting
- Incremental inserts
- Updates and deletes
- SQL-based queries
- Data previews
- Data API access
- Resource aliases
- Index support

The DataStore normally uses a separate PostgreSQL database and restricted database users.

### APIs

CKAN exposes the Action API for programmatic access to catalog functionality.

Common API workflows include:

- Listing datasets
- Reading dataset metadata
- Creating datasets
- Updating datasets
- Managing organizations
- Managing groups
- Searching packages
- Uploading resources
- Querying DataStore tables
- Managing users
- Reading activity information

### Background Jobs

CKAN supports asynchronous jobs through Redis-backed RQ queues.

Background jobs can be used for:

- Extension tasks
- Dataset processing
- Notifications
- External synchronization
- Long-running operations
- Data imports
- Maintenance work

Multiple workers can be run for higher-throughput installations.

### Extensions

CKAN provides a mature plugin architecture for extending core behavior.

Extensions can provide:

- Custom dataset schemas
- Authentication
- Authorization
- Search behavior
- Actions
- Validators
- Templates
- Static assets
- Storage adapters
- Routes
- Background tasks
- Data importers
- Harvesters
- Metadata standards
- APIs
- UI changes

The plugin toolkit exposes stable interfaces intended for extension development.

### Theming

CKAN supports custom themes using:

- Jinja templates
- Template inheritance
- Helper functions
- Static assets
- Sass
- JavaScript
- Webassets
- Plugin-provided resources

Custom themes should generally be implemented as extensions rather than by modifying CKAN core templates directly.

## Tech Stack

| Area | Technologies |
| --- | --- |
| Language | Python 3.10 or later |
| Web framework | Flask |
| Database access | SQLAlchemy |
| Primary database | PostgreSQL 12 or later |
| Search | Apache Solr |
| Cache and queues | Redis |
| Background jobs | RQ |
| Templates | Jinja |
| Frontend | HTML, JavaScript, jQuery |
| Styling | Bootstrap, Sass |
| Asset pipeline | Webassets, Gulp |
| API | CKAN Action API |
| Structured data | CKAN DataStore |
| File storage | FileStore and configurable storage adapters |
| Authentication | CKAN user and plugin authentication systems |
| Internationalization | Babel and gettext-based localization |
| Web serving | uWSGI-compatible deployment |
| Testing | Pytest |
| Browser testing | Cypress |
| Quality tooling | Ruff and repository validation tools |

## Installation

CKAN can be installed from operating-system packages, source, or Docker-based environments.

For development and extension work, source installation is the most direct approach.

### Requirements

- Python 3.10 or later
- PostgreSQL 12 or later
- Apache Solr
- Redis
- Git
- Python virtual-environment support
- PostgreSQL development libraries
- libmagic
- Node.js and npm for frontend development

On Debian or Ubuntu, common development dependencies include:

```bash
sudo apt update

sudo apt install -y \
  python3-dev \
  libpq-dev \
  python3-pip \
  python3-venv \
  git-core \
  redis-server \
  libmagic1
```

PostgreSQL and Solr should also be installed and running.

### Create a Virtual Environment

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
```

### Install CKAN from Source

Clone the repository:

```bash
git clone <repository>
cd ckan
```

Install the application and development dependencies:

```bash
pip install -e ".[requirements,dev]"
```

If the current branch uses a different packaging extra layout, install the pinned repository requirements followed by the editable package:

```bash
pip install -r requirements.txt
pip install -r dev-requirements.txt
pip install -e .
```

### Create a Configuration File

Generate a CKAN configuration:

```bash
ckan generate config ckan.ini
```

Edit `ckan.ini` and configure at least:

- Site URL
- PostgreSQL connection
- Solr endpoint
- Redis endpoint
- Secret values
- Enabled plugins
- Storage path
- Email settings when required

### Create the Database

Create a PostgreSQL user and database.

Example:

```sql
CREATE USER ckan_default WITH PASSWORD 'replace_with_secure_password';
CREATE DATABASE ckan_default OWNER ckan_default;
```

Configure the resulting connection in `ckan.ini`.

### Initialize the Database

```bash
ckan -c ckan.ini db init
```

### Create a Sysadmin

```bash
ckan -c ckan.ini sysadmin add admin
```

Follow the prompts to create the account.

### Start the Development Server

```bash
ckan -c ckan.ini run
```

The local development server typically listens on port `5000`.

## Usage

### Create a Dataset

1. Sign in with a user who has publishing permission.
2. Create or select an organization.
3. Create a dataset.
4. Enter the title and description.
5. Add tags and metadata.
6. Configure visibility.
7. Add one or more resources.
8. Upload files or provide external resource URLs.
9. Save the dataset.
10. Confirm that it is searchable.

### Organizations

Organizations provide ownership and permission boundaries for published datasets.

Typical roles include:

- Administrator
- Editor
- Member

Organization permissions control who can create, modify, or manage datasets.

### Groups

Groups provide thematic or curated collections of datasets.

A dataset can belong to one or more groups independently of its organization ownership.

## Action API

The Action API is exposed under:

```text
/api/3/action/
```

### List Datasets

```bash
curl "$CKAN_URL/api/3/action/package_list"
```

### Search Datasets

```bash
curl "$CKAN_URL/api/3/action/package_search?q=transport"
```

### Read a Dataset

```bash
curl "$CKAN_URL/api/3/action/package_show?id=dataset-name"
```

### Authenticated Requests

Write operations require authorization.

A typical request includes an API token:

```bash
curl -X POST \
  -H "Authorization: $CKAN_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"example-dataset"}' \
  "$CKAN_URL/api/3/action/package_create"
```

Store API tokens outside source-controlled scripts.

## DataStore

Enable the DataStore plugin in `ckan.ini`:

```ini
ckan.plugins = datastore
```

The DataStore should use a separate PostgreSQL database with properly restricted read and write users.

Configure the DataStore connections:

```ini
ckan.datastore.write_url = postgresql://datastore_default:password@localhost/datastore_default
ckan.datastore.read_url = postgresql://datastore_default:password@localhost/datastore_default
```

Actual production permissions should follow the least-privilege DataStore setup rather than sharing an unrestricted account.

Apply DataStore database permissions using the CKAN command:

```bash
ckan -c ckan.ini datastore set-permissions
```

### Query DataStore

A DataStore resource can be queried through Action API endpoints such as:

```text
datastore_search
datastore_search_sql
datastore_create
datastore_upsert
datastore_delete
```

DataStore records remain associated with normal CKAN resources.

## File Storage

### Local FileStore

Create a writable storage directory:

```bash
sudo mkdir -p /var/lib/ckan/default
```

Configure:

```ini
ckan.storage_path = /var/lib/ckan/default
```

Recent CKAN versions can use the configurable storage interface:

```ini
ckan.files.storage.default.type = ckan:fs
ckan.files.storage.default.path = /var/lib/ckan/default
```

Configurable storages can separately manage:

- Dataset resources
- User avatars
- Organization images
- Site branding
- Custom extension files

## Search

CKAN uses Apache Solr for dataset indexing and search.

Configure the Solr endpoint in `ckan.ini`.

After changing indexed metadata or recovering a Solr instance, rebuild the search index:

```bash
ckan -c ckan.ini search-index rebuild
```

Index a single dataset when needed:

```bash
ckan -c ckan.ini search-index rebuild <dataset-name>
```

Search availability should be monitored separately from PostgreSQL because a working database does not guarantee a healthy search index.

## Background Jobs

Start a worker:

```bash
ckan -c ckan.ini jobs worker
```

The process listens to the configured Redis-backed job queue.

Run multiple workers when jobs are long-running or queue volume is high.

Production deployments should supervise worker processes using a process manager or container orchestrator.

## Configuration

CKAN primarily reads runtime settings from an INI configuration file.

### Core Settings

| Setting | Purpose |
| --- | --- |
| `ckan.site_url` | Public portal origin |
| `ckan.site_title` | Portal title |
| `ckan.site_description` | Portal description |
| `ckan.plugins` | Enabled plugins |
| `ckan.locale_default` | Default language |
| `ckan.locales_offered` | Available languages |
| `ckan.auth.create_user_via_web` | Controls browser registration |
| `ckan.auth.create_user_via_api` | Controls API registration |
| `ckan.auth.public_user_details` | Controls public user information |

### Database

| Setting | Purpose |
| --- | --- |
| `sqlalchemy.url` | Main PostgreSQL connection |
| `ckan.datastore.write_url` | DataStore write connection |
| `ckan.datastore.read_url` | DataStore restricted read connection |

### Search

| Setting | Purpose |
| --- | --- |
| `solr_url` | Apache Solr endpoint |
| `ckan.search.solr_commit` | Controls index commit behavior |

### Redis and Jobs

| Setting | Purpose |
| --- | --- |
| `ckan.redis.url` | Redis connection |
| `ckan.jobs.timeout` | Default background-job timeout |
| `ckan.jobs.default_queues` | Default worker queues |

### Storage

| Setting | Purpose |
| --- | --- |
| `ckan.storage_path` | Classic local FileStore path |
| `ckan.uploads_enabled` | Enables file uploads |
| `ckan.max_resource_size` | Resource upload limit |
| `ckan.max_image_size` | Image upload limit |
| `ckan.files.storage.*` | Configurable storage definitions |

### Email

Typical email settings include:

```text
smtp.server
smtp.starttls
smtp.user
smtp.password
smtp.mail_from
```

Configure email only with deployment-specific credentials stored outside version control.

## Extensions

CKAN extensions normally use the `ckanext-` package naming convention.

A typical extension can implement plugin interfaces for:

- Actions
- Authentication
- Authorization
- Configuration
- Dataset forms
- Group forms
- Organization forms
- Routes
- Template helpers
- Validators
- Search
- Storage
- Background jobs

Enable an installed extension by adding its plugin name to:

```ini
ckan.plugins =
```

Restart the application after plugin configuration changes.

Keep extension versions aligned with the deployed CKAN version.

## Frontend Development

Install frontend dependencies:

```bash
npm install
```

The frontend includes:

- Jinja templates
- JavaScript modules
- Sass styles
- Bootstrap-based layout
- Webassets bundles
- Gulp-based asset workflows

Use CKAN's template inheritance and extension mechanisms instead of modifying core theme files for deployment-specific branding.

## Testing

### Python Tests

Run the core test suite:

```bash
pytest
```

Run a specific test module:

```bash
pytest ckan/tests/path/to/test_file.py
```

### Browser Tests

The repository includes Cypress configuration for browser-oriented tests.

Run the appropriate Cypress workflow after preparing the required test environment.

### Frontend Assets

Install frontend dependencies:

```bash
npm install
```

Run the repository's frontend asset and lint commands defined in `package.json`.

## Production Deployment

A production CKAN installation generally includes:

- Reverse proxy
- uWSGI or another supported WSGI process
- CKAN application
- PostgreSQL
- Apache Solr
- Redis
- Background workers
- Persistent file storage
- Optional DataStore database
- Optional DataPusher or equivalent data-loading service

Production deployments should:

- Use secure PostgreSQL credentials
- Restrict PostgreSQL, Solr, and Redis from public access
- Use HTTPS
- Store secrets outside source control
- Use persistent FileStore storage
- Back up PostgreSQL databases
- Back up uploaded resources
- Monitor Solr indexing
- Monitor Redis and job workers
- Configure SMTP deliberately
- Run multiple workers when required
- Validate extension compatibility before upgrades
- Test database migrations in staging
- Rebuild search indexes when required

## Common Commands

Initialize the database:

```bash
ckan -c ckan.ini db init
```

Upgrade the database:

```bash
ckan -c ckan.ini db upgrade
```

Create a sysadmin:

```bash
ckan -c ckan.ini sysadmin add admin
```

List sysadmins:

```bash
ckan -c ckan.ini sysadmin list
```

Start a worker:

```bash
ckan -c ckan.ini jobs worker
```

Rebuild the search index:

```bash
ckan -c ckan.ini search-index rebuild
```

Open an interactive shell:

```bash
ckan -c ckan.ini shell
```

Display configuration:

```bash
ckan -c ckan.ini config declaration
```

## Contributing

Create a focused branch from the current development branch and follow CKAN's backend, frontend, API, extension, and testing conventions.

Before submitting changes:

- Add tests for bug fixes and new behavior
- Run targeted Pytest suites
- Run browser tests for affected workflows
- Preserve Action API compatibility
- Keep database migrations upgrade-safe
- Maintain extension interfaces carefully
- Keep user-facing text translatable
- Update configuration documentation for new settings
- Avoid committing credentials or private deployment files
- Test Solr-related changes against a real search service
- Test background jobs with Redis and an active worker
- Keep changes focused and clearly documented
