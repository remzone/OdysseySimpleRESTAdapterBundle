# Endpoint Configuration

## Overview

Each DataHub configuration creates its own Simple REST endpoint with its own schema, workspaces, labels, token, and index set.

The admin-side routes for configuration are exposed under:

```text
/admin/simple-rest-adapter/config
```

## Main Configuration Areas

### General

Use the general section to define:

- configuration name
- description
- active state
- endpoint-level metadata

![General settings](../images/general.png "General settings")

### Schema Definition

The schema controls what the endpoint can expose.

Here you define:

- DataObject classes included in the endpoint
- fields available for delivery
- whether assets are included in search and tree responses
- whether originals and thumbnails are delivered for assets

![Schema settings](../images/schema.png "Schema settings")

### Workspaces

Workspaces define what parts of Pimcore are actually visible through the endpoint.

You can:

- include folders
- explicitly exclude folders
- limit visible asset and object trees

This workspace information also influences how the indexed virtual tree is built.

![Workspace settings](../images/workspaces.png "Workspace settings")

### Label Settings

Label settings are used to define human-readable labels for fields and languages. These labels are returned in API responses and can be used directly by frontend consumers.

The same section also controls fields used for aggregations and filter navigation.

![Label settings](../images/label_settings.png "Label settings")

Note: the field list is based on indexed data. After changing schema-related settings, save the configuration and let the index queue process updates before expecting the field list to fully refresh.

### Delivery Settings

Delivery settings are used for endpoint security and public API access.

Here you can:

- generate or define the API token
- inspect the Swagger URL
- configure delivery-related endpoint options

![Delivery settings](../images/delivery_settings.png "Delivery settings")

## Public Endpoint Paths

For a given `{config}` the bundle provides:

- `/pimcore-datahub-webservices/simplerest/{config}/tree-items`
- `/pimcore-datahub-webservices/simplerest/{config}/search`
- `/pimcore-datahub-webservices/simplerest/{config}/get-element`
- `/pimcore-datahub-webservices/simplerest/{config}/download-asset`

Swagger UI is available at:

- `/pimcore-datahub-webservices/simplerest/swagger`

## Configuration Helper Endpoints

The admin UI also uses helper endpoints such as:

- `/admin/simple-rest-adapter/config/label-list`
- `/admin/simple-rest-adapter/config/thumbnails`

These are relevant if you extend the UI or integrate custom admin tooling.
