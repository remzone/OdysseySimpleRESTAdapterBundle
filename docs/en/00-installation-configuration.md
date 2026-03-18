# Installation and Configuration

## Overview

`odyssey/simple-rest-adapter-bundle` is the public Odyssey-maintained fork of the original CI HUB Simple REST Adapter bundle.

This fork is intended for reusable installation in other Pimcore projects and is maintained for modern environments, including:

- `Pimcore 11`
- current Swagger integration used by this fork
- OpenSearch-based client runtime
- ongoing compatibility maintenance for modern Symfony and Pimcore stacks

## Ownership and Maintenance

- The original codebase belongs to `CI HUB`
- This public fork is maintained and updated by `Odyssey`
- The bundle is distributed under `GPL-3.0-or-later`, which allows further customization and extension under the license terms

## Requirements

- PHP `>= 8.1`
- Pimcore `^11.0`
- Pimcore DataHub
- Symfony Messenger
- OpenSearch or Elasticsearch

## Install via Composer

```bash
composer require odyssey/simple-rest-adapter-bundle
```

## Bundle Registration

In most Pimcore projects the bundle should be auto-discovered. If your project requires manual registration, add:

```php
CIHub\Bundle\SimpleRESTAdapterBundle\SimpleRESTAdapterBundle::class => ['all' => true],
```

## Bundle Configuration

The bundle configuration root is:

```yaml
simple_rest_adapter:
```

Available options:

```yaml
simple_rest_adapter:
    engine: opensearch
    index_name_prefix: datahub_restindex
    hosts:
        - localhost:9200
    es_hosts:
        - localhost
    index_settings:
        number_of_shards: 5
        number_of_replicas: 0
        max_ngram_diff: 20
        analysis:
            analyzer:
                datahub_ngram_analyzer:
                    type: custom
                    tokenizer: datahub_ngram_tokenizer
                    filter: [lowercase]
                datahub_whitespace_analyzer:
                    type: custom
                    tokenizer: datahub_whitespace_tokenizer
                    filter: [lowercase]
            normalizer:
                lowercase:
                    type: custom
                    filter: [lowercase]
            tokenizer:
                datahub_ngram_tokenizer:
                    type: nGram
                    min_gram: 2
                    max_gram: 20
                    token_chars: [letter, digit]
                datahub_whitespace_tokenizer:
                    type: whitespace
```

## Engine Switching

The bundle now supports explicit engine switching:

```yaml
simple_rest_adapter:
    engine: opensearch
    hosts:
        - opensearch:9200
```

```yaml
simple_rest_adapter:
    engine: elasticsearch
    hosts:
        - elasticsearch:9200
```

Install the matching PHP client in your project:

```bash
composer require opensearch-project/opensearch-php
```

or

```bash
composer require elastic/elasticsearch
```

## Important Note About `hosts` and `es_hosts`

The new generic key is `hosts`. The old key `es_hosts` is still supported for backward compatibility.

- prefer `simple_rest_adapter.hosts` in new projects
- old projects can keep `simple_rest_adapter.es_hosts`
- if `hosts` is empty, the bundle falls back to `es_hosts`
- if your backend requires Elasticsearch 8 style tokenizer naming, use `ngram` instead of `nGram`

## Messaging and Index Queue

The bundle registers a messenger transport named:

```text
datahub_es_index_queue
```

Typical worker command:

```bash
php bin/console messenger:consume datahub_es_index_queue --limit=20 --time-limit=240
```

You can run this under cron, supervisor, or your container runtime.

## Routing Overview

The bundle exposes the following route groups:

- API endpoints: `/pimcore-datahub-webservices/simplerest/{config}`
- Swagger UI: `/pimcore-datahub-webservices/simplerest/swagger`
- Admin configuration routes: `/admin/simple-rest-adapter/config`

## Swagger Notes

This fork includes maintained Swagger integration for current usage with `Pimcore 11` and supports projects that are preparing for `Pimcore 12` level Swagger workflows and dependency alignment.

## Next Reading

- [Endpoint configuration](01-endpoint-configuration.md)
- [Indexing](02-indexing.md)
- [Docker setup example](03-docker-setup-example.md)
