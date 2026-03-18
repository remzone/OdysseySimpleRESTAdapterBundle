# Настройка endpoint'ов

## Обзор

Каждая конфигурация DataHub создаёт собственный Simple REST endpoint со своей схемой, рабочими областями, labels, токеном и набором индексов.

Административные маршруты конфигурации доступны под:

```text
/admin/simple-rest-adapter/config
```

## Основные блоки конфигурации

### General

В этом разделе задаются:

- имя конфигурации
- описание
- активность endpoint'а
- общие метаданные

![General settings](../images/general.png "General settings")

### Schema Definition

Схема определяет, какие данные endpoint может отдавать.

Здесь задаются:

- классы DataObject, входящие в endpoint
- поля, доступные для выдачи
- участие assets в search и tree responses
- выдача originals и thumbnails для assets

![Schema settings](../images/schema.png "Schema settings")

### Workspaces

Workspaces определяют, какая часть Pimcore реально видна через endpoint.

Можно:

- включать папки
- явно исключать папки
- ограничивать видимое дерево assets и objects

Эти настройки также влияют на построение виртуального индексного дерева.

![Workspace settings](../images/workspaces.png "Workspace settings")

### Label Settings

В этом разделе задаются человекочитаемые labels для полей и языков. Эти labels возвращаются в API-ответах и могут напрямую использоваться клиентским приложением.

Там же задаются поля для агрегаций и фасетной навигации.

![Label settings](../images/label_settings.png "Label settings")

Важно: список полей строится по индексированным данным. После изменений схемы сохраните конфигурацию и дайте очереди индексации обработать обновления.

### Delivery Settings

Раздел delivery используется для безопасности endpoint'а и публичного доступа к API.

Здесь можно:

- сгенерировать или задать API token
- посмотреть Swagger URL
- настроить delivery-параметры endpoint'а

![Delivery settings](../images/delivery_settings.png "Delivery settings")

## Публичные endpoint paths

Для заданного `{config}` bundle предоставляет:

- `/pimcore-datahub-webservices/simplerest/{config}/tree-items`
- `/pimcore-datahub-webservices/simplerest/{config}/search`
- `/pimcore-datahub-webservices/simplerest/{config}/get-element`
- `/pimcore-datahub-webservices/simplerest/{config}/download-asset`

Swagger UI доступен по адресу:

- `/pimcore-datahub-webservices/simplerest/swagger`

## Вспомогательные admin endpoints

Admin UI также использует вспомогательные endpoints:

- `/admin/simple-rest-adapter/config/label-list`
- `/admin/simple-rest-adapter/config/thumbnails`

Это полезно знать при расширении UI или написании кастомных admin-интеграций.
