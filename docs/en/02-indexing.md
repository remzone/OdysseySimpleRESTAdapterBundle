# Indexing

## Overview

All API responses are served from indexed data rather than directly from the Pimcore database.

This is one of the core reasons to use the bundle:

- more predictable response times
- reduced database load
- better suitability for search-heavy and integration-heavy read workloads

## Search Backend

The current Odyssey fork supports both OpenSearch and Elasticsearch through a configurable client factory.

Practical meaning:

- use `simple_rest_adapter.engine` to choose the backend
- use `simple_rest_adapter.hosts` for host definitions
- historic names such as `datahub_es_index_queue` remain in place for backward compatibility
- `es_hosts` still works as a fallback config key

## Index Creation Model

Each DataHub configuration produces its own set of indices.

Typically there are separate indices for:

- DataObject classes
- DataObject folders
- Assets
- Asset folders

## Queue-Based Processing

Indexing is asynchronous and relies on Symfony Messenger.

Configured transport:

```text
datahub_es_index_queue
```

Typical worker command:

```bash
php bin/console messenger:consume datahub_es_index_queue --memory-limit=250M --time-limit=3600
```

## What Triggers Index Changes

Index creation and updates happen when:

- endpoint configurations are created or updated
- indexed elements are created, updated, or deleted
- queue messages are consumed by the messenger worker

This fork also dispatches dedicated configuration lifecycle events:

- `datahub.simple_rest.configuration.pre_save`
- `datahub.simple_rest.configuration.post_save`

These are useful if you extend the bundle and want to react to configuration changes.

## Tree and Virtual Folder Behavior

The indexed tree is not always a raw copy of Pimcore's folder tree.

The bundle may:

- rewrite paths relative to workspace boundaries
- calculate combined parent folders
- create virtual folders to preserve navigable hierarchies when workspace and schema rules omit intermediate nodes

## Tokenizer Note

If your search backend expects Elasticsearch 8 style tokenizer naming, use:

```yaml
type: ngram
```

instead of:

```yaml
type: nGram
```

## Operational Recommendation

For production use:

- keep the messenger worker running continuously
- monitor queue growth
- reindex after major schema changes
- verify index availability in your OpenSearch cluster after endpoint updates
