# Docker Setup Example

## Overview

This example shows a practical setup for running the Odyssey fork in a Docker-based Pimcore environment.

## Install Required Packages

```bash
docker compose exec php-fpm composer require pimcore/data-hub
docker compose exec php-fpm composer require odyssey/simple-rest-adapter-bundle
```

If manual bundle enablement is needed in your project:

```bash
docker compose exec php-fpm php bin/console pimcore:bundle:enable PimcoreDataHubBundle
docker compose exec php-fpm php bin/console pimcore:bundle:enable SimpleRESTAdapterBundle
```

## Add Search Backend

### OpenSearch example

```yaml
services:
    opensearch:
        image: opensearchproject/opensearch:2
        container_name: opensearch
        environment:
            - discovery.type=single-node
            - plugins.security.disabled=true
            - OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m
        ulimits:
            memlock:
                soft: -1
                hard: -1
            nofile:
                soft: 65536
                hard: 65536
        volumes:
            - opensearch-data:/usr/share/opensearch/data
        ports:
            - "9200:9200"
            - "9600:9600"

volumes:
    opensearch-data:
```

### Elasticsearch example

```yaml
services:
    elasticsearch:
        image: elasticsearch:8
        container_name: elasticsearch
        environment:
            - xpack.security.enabled=false
            - discovery.type=single-node
        volumes:
            - elasticsearch-data:/usr/share/elasticsearch/data
        ports:
            - "9200:9200"

volumes:
    elasticsearch-data:
```

## Bundle Configuration Example

### OpenSearch

```yaml
simple_rest_adapter:
    engine: opensearch
    index_name_prefix: datahub_restindex
    hosts:
        - opensearch:9200
```

### Elasticsearch

```yaml
simple_rest_adapter:
    engine: elasticsearch
    index_name_prefix: datahub_restindex
    hosts:
        - elasticsearch:9200
```

### Backward-compatible legacy config

```yaml
simple_rest_adapter:
    engine: opensearch
    es_hosts:
        - opensearch:9200
```

## Run the Worker

Add a supervisor job or container command like:

```bash
php /var/www/html/bin/console messenger:consume datahub_es_index_queue --memory-limit=250M --time-limit=3600
```

## Verification Checklist

- ensure the worker is running
- create or update a DataHub Simple REST configuration
- confirm indices appear in your OpenSearch cluster
- open Swagger UI at `/pimcore-datahub-webservices/simplerest/swagger`
- test `search`, `tree-items`, and `get-element`

## Notes

- install `opensearch-project/opensearch-php` for `engine: opensearch`
- install `elastic/elasticsearch` for `engine: elasticsearch`
- `hosts` is the preferred new config key
- `es_hosts` remains supported for compatibility with older installations
