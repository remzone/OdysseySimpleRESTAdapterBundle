# Установка и конфигурация

## Обзор

`odyssey/simple-rest-adapter-bundle` это публичный fork Simple REST Adapter bundle, который изначально был создан `CI HUB`, а сейчас поддерживается и обновляется `Odyssey`.

Этот fork рассчитан на повторное использование в других Pimcore-проектах и поддерживается для современных окружений, включая:

- `Pimcore 11`
- актуальную Swagger-интеграцию этого форка
- runtime на базе OpenSearch client
- дальнейшую поддержку совместимости для современных версий Pimcore и Symfony

## Авторство и сопровождение

- исходная кодовая база принадлежит `CI HUB`
- публичный fork поддерживается `Odyssey`
- bundle распространяется по лицензии `GPL-3.0-or-later`, что позволяет доработки и развитие в рамках условий лицензии

## Требования

- PHP `>= 8.1`
- Pimcore `^11.0`
- Pimcore DataHub
- Symfony Messenger
- OpenSearch или Elasticsearch

## Установка через Composer

```bash
composer require odyssey/simple-rest-adapter-bundle
```

## Регистрация bundle

Во многих проектах Pimcore bundle подхватится автоматически. Если нужна ручная регистрация, добавьте:

```php
CIHub\Bundle\SimpleRESTAdapterBundle\SimpleRESTAdapterBundle::class => ['all' => true],
```

## Конфигурация bundle

Корень конфигурации:

```yaml
simple_rest_adapter:
```

Доступные параметры:

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

## Переключение движка

Теперь bundle поддерживает явное переключение search engine:

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

Установите соответствующий PHP client:

```bash
composer require opensearch-project/opensearch-php
```

или

```bash
composer require elastic/elasticsearch
```

## Важное замечание про `hosts` и `es_hosts`

Новый универсальный ключ это `hosts`. Старый ключ `es_hosts` тоже поддерживается для обратной совместимости.

- в новых проектах лучше использовать `simple_rest_adapter.hosts`
- в старых проектах можно оставить `simple_rest_adapter.es_hosts`
- если `hosts` пустой, bundle автоматически использует `es_hosts`
- если backend требует стиль Elasticsearch 8, используйте `ngram` вместо `nGram`

## Очередь индексации

Bundle регистрирует messenger transport:

```text
datahub_es_index_queue
```

Типовая команда воркера:

```bash
php bin/console messenger:consume datahub_es_index_queue --limit=20 --time-limit=240
```

Её можно запускать через cron, supervisor или в контейнерной инфраструктуре.

## Маршруты

Bundle использует следующие группы маршрутов:

- API endpoints: `/pimcore-datahub-webservices/simplerest/{config}`
- Swagger UI: `/pimcore-datahub-webservices/simplerest/swagger`
- admin configuration routes: `/admin/simple-rest-adapter/config`

## Замечание по Swagger

Этот fork поддерживает текущую Swagger-интеграцию для `Pimcore 11` и подходит для проектов, которые готовятся к сценариям совместимости и dependency alignment уровня `Pimcore 12`.

## Что читать дальше

- [Настройка endpoint'ов](01-endpoint-configuration.md)
- [Индексирование](02-indexing.md)
- [Пример Docker setup](03-docker-setup-example.md)
