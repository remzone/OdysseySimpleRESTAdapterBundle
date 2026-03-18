# Пример Docker setup

## Обзор

Ниже пример практической установки Odyssey fork в Docker-окружении Pimcore.

## Установка пакетов

```bash
docker compose exec php-fpm composer require pimcore/data-hub
docker compose exec php-fpm composer require odyssey/simple-rest-adapter-bundle
```

Если проект требует ручного включения bundle:

```bash
docker compose exec php-fpm php bin/console pimcore:bundle:enable PimcoreDataHubBundle
docker compose exec php-fpm php bin/console pimcore:bundle:enable SimpleRESTAdapterBundle
```

## Добавление search backend

### Пример OpenSearch

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

### Пример Elasticsearch

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

## Пример конфигурации bundle

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

### Legacy-конфигурация для старых установок

```yaml
simple_rest_adapter:
    engine: opensearch
    es_hosts:
        - opensearch:9200
```

## Запуск воркера

Добавьте в supervisor или контейнерную команду что-то вроде:

```bash
php /var/www/html/bin/console messenger:consume datahub_es_index_queue --memory-limit=250M --time-limit=3600
```

## Чеклист проверки

- убедитесь, что воркер запущен
- создайте или обновите DataHub Simple REST configuration
- проверьте, что индексы появились в OpenSearch
- откройте Swagger UI по адресу `/pimcore-datahub-webservices/simplerest/swagger`
- протестируйте `search`, `tree-items` и `get-element`

## Замечания

- для `engine: opensearch` установите `opensearch-project/opensearch-php`
- для `engine: elasticsearch` установите `elastic/elasticsearch`
- `hosts` это предпочтительный новый ключ конфигурации
- `es_hosts` остаётся доступным для совместимости со старыми инсталляциями
