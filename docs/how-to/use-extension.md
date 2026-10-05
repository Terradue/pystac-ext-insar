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

# Work with InSAR metadata

The first examples continue from the Item created in the [tutorial](../tutorials/first-steps.md).

## Update or remove one field

```python
insar = InsarExtension.ext(item)
insar.temporal_baseline = 24
insar.processing_dem = None
assert "insar:processing_dem" not in item.properties
```

`ext(item)` requires the extension to be declared already. Use `add_if_missing=True` when first attaching it. Assigning `None` removes a field.

`apply()` sets every supported field. Omitted arguments become `None` and remove existing values. Use individual setters to preserve other fields.

## Attach asset fields

```python
asset = pystac.Asset(href="https://example.org/interferogram.tif", media_type="image/tiff")
item.add_asset("interferogram", asset)
asset_insar = InsarExtension.ext(asset, add_if_missing=True)
assert asset_insar.temporal_baseline is None
asset_insar.temporal_baseline = 12
assert asset.extra_fields["insar:temporal_baseline"] == 12.0
assert insar.temporal_baseline == 24.0
```

Attach an asset to its owner before wrapping it so extension membership can be checked or added on the owner. The asset wrapper reads and writes `asset.extra_fields`; it does not inherit missing values from Item properties. Collection assets use the same wrapper.

## Manage Collection summaries

Use `InsarExtension.summaries()` for Collections. Passing a Collection to `InsarExtension.ext()` raises `pystac.ExtensionTypeError`.

```python
collection = pystac.Collection(
    id="example-insar-collection",
    description="Synthetic InSAR products",
    extent=pystac.Extent(
        pystac.SpatialExtent([[-180.0, -90.0, 180.0, 90.0]]),
        pystac.TemporalExtent([[reference_datetime, secondary_datetime]]),
    ),
    license="proprietary",
)
summaries = InsarExtension.summaries(collection, add_if_missing=True)
summaries.apply(
    reference_datetime=reference_datetime,
    processing_dem="COP-DEM GLO-30",
    geocoding_dem="COP-DEM GLO-30",
)
assert collection.summaries.lists["insar:processing_dem"] == ["COP-DEM GLO-30"]
assert summaries.reference_datetime == reference_datetime

summaries.processing_dem = None
assert "insar:processing_dem" not in collection.summaries.lists
```

The summary helper supports reference datetime, processing DEM, and geocoding DEM. Each setter replaces the summary with a single-element list; each getter reads only the first element. Assigning `None` removes the summary. Its `apply()` method clears omitted values, just like the Item helper.

## Use Collection item asset definitions

```python
collection.item_assets = {
    "interferogram": pystac.ItemAssetDefinition(
        {"type": "image/tiff", "roles": ["data"]}
    ),
}
definition = collection.item_assets["interferogram"]
definition_insar = InsarExtension.ext(definition, add_if_missing=True)
definition_insar.processing_dem = "COP-DEM GLO-30"
assert definition.properties["insar:processing_dem"] == "COP-DEM GLO-30"
```

Retrieve definitions through `collection.item_assets` so they have an owner. Fields are written to the definition's `properties` dictionary, and the extension is declared on its Collection.
