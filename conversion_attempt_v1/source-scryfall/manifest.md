# Overview

This document provides a configuration example for integrating with a third-party card game API. The API allows access to various resources such as cards, sets, and symbols, each with its own schema and pagination strategy. The integration is designed to be vendor-neutral and follows the dltHub REST API source format.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "none"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "cards",
            "endpoint": {
                "path": "/v1/cards/search",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('next_page') }}",
                    "stop_condition": "{{ not response.get('has_more', False) }}"
                }
            }
        },
        {
            "name": "sets",
            "endpoint": {
                "path": "/v1/sets",
                "data_selector": "data"
            }
        },
        {
            "name": "symbols",
            "endpoint": {
                "path": "/v1/symbology",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('next_page') }}",
                    "stop_condition": "{{ not response.get('has_more', False) }}"
                }
            }
        }
    ]
})
```

## Authentication

- Type: None
- No authentication is required for accessing the API.

## Resources

### Cards
- **Endpoint Path**: `/v1/cards/search`
- **Pagination Strategy**: Cursor-based
  - **Cursor Value**: `{{ response.get('next_page') }}`
  - **Stop Condition**: `{{ not response.get('has_more', False) }}`
- **Data Selector**: `data`

### Sets
- **Endpoint Path**: `/v1/sets`
- **Data Selector**: `data`

### Symbols
- **Endpoint Path**: `/v1/symbology`
- **Pagination Strategy**: Cursor-based
  - **Cursor Value**: `{{ response.get('next_page') }}`
  - **Stop Condition**: `{{ not response.get('has_more', False) }}`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Not specified in the configuration.
- **HTTP Status Codes**: Not specified in the configuration.
- **Fallback Behavior**: Not specified in the configuration.

## Schema

### Cards
- **Primary Key**: `id`
- **Fields**: Includes properties such as `all_parts`, `arena_id`, `artist`, `booster`, `border_color`, `card_back_id`, `card_faces`, `cardmarket_id`, `cmc`, `collector_number`, `color_identity`, `colors`, `digital`, `edhrec_rank`, `finishes`, `flavor_text`, `foil`, `frame`, `frame_effects`, `full_art`, `games`, `highres_image`, `id`, `illustration_id`, `image_status`, `image_uris`, `keywords`, `lang`, `layout`, `legalities`, `loyalty`, `mana_cost`, `mtgo_foil_id`, `mtgo_id`, `multiverse_ids`, `name`, `nonfoil`, `object`, `oracle_id`, `oracle_text`, `oversized`, `penny_rank`, `power`, `preview`, `prices`, `prints_search_uri`, `produced_mana`, `promo`, `promo_types`, `purchase_uris`, `rarity`, `related_uris`, `released_at`, `reprint`, `reserved`, `rulings_uri`, `scryfall_set_uri`, `scryfall_uri`, `security_stamp`, `set`, `set_id`, `set_name`, `set_search_uri`, `set_type`, `set_uri`, `story_spotlight`, `tcgplayer_etched_id`, `tcgplayer_id`, `textless`, `toughness`, `type_line`, `uri`, `variation`, `watermark`.

### Sets
- **Primary Key**: `id`
- **Fields**: Includes properties such as `arena_code`, `block`, `block_code`, `card_count`, `code`, `digital`, `foil_only`, `icon_svg_uri`, `id`, `mtgo_code`, `name`, `nonfoil_only`, `object`, `parent_set_code`, `printed_size`, `released_at`, `scryfall_uri`, `search_uri`, `set_type`, `tcgplayer_id`, `uri`.

### Symbols
- **Primary Key**: `symbol`
- **Fields**: Includes properties such as `appears_in_mana_costs`, `cmc`, `colors`, `english`, `funny`, `gatherer_alternates`, `hybrid`, `loose_variant`, `mana_value`, `object`, `phyrexian`, `represents_mana`, `svg_uri`, `symbol`, `transposable`.