<!--
Copyright 2026 Terradue

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Scope, architecture, and validation

## Package structure

The distribution is named `pystac-ext-insar`, while the import path is `pystac.extensions.insar`. The wheel excludes the shared `pystac.extensions` initializer owned by PySTAC.

`InsarExtension.ext()` selects a wrapper for an Item, Asset, or ItemAssetDefinition. These wrappers write directly into the wrapped object's dictionaries, so serialization uses PySTAC's normal `to_dict()` method. The package stores metadata; it does not generate interferograms, calculate baselines, or retrieve DEMs.

Asset wrappers use only the asset's extra fields and do not fall back to Item properties. Extension membership for assets and item asset definitions is managed through their owner.

## Collection summaries

Collections use `InsarExtension.summaries()`. The current helper supports reference datetime and the two DEM properties as single-element lists. It does not compute summaries from Items. Reading a multi-element list returns only the first value; writing through the helper replaces the list. Use PySTAC's Collection summary APIs directly when you need other summary shapes or fields.

## Validation boundaries

Numeric setters reject booleans and nonnumeric values. DEM setters check string types. They do not validate numeric ranges, DEM availability, or relationships among acquisition dates and baselines. Type annotations guide callers but do not enforce every constraint at runtime. Reading an existing document does not revalidate its properties through the setters.

`apply()` mutates fields sequentially. If a later setter fails, earlier assignments remain. It also clears fields whose values are omitted. Validate inputs first if your application requires an all-or-nothing update.

For full STAC and extension validation, install PySTAC's validation extra with `python -m pip install "pystac[validation]"` and call `item.validate()`. Validation may retrieve remote schemas, so the referenced schema URLs must be accessible or supplied through an application-configured validator. Neither `apply()` nor `to_dict()` performs JSON Schema validation.

The [field reference](../reference/fields.md) describes the current wrapper's checks and supported summaries.

## Migration hooks

`INSAR_EXTENSION_HOOKS` declares the schema identifier, the legacy identifier `insar`, and Item/Collection object types. The module exposes this hook object but does not automatically register it with PySTAC.
