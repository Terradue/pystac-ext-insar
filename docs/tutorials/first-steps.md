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

# Create an InSAR Item

[Install the package](../how-to/install.md) before running this complete example. It creates a synthetic interferogram Item with acquisition dates, baselines, and DEM identifiers.

```python
from datetime import datetime, timezone

import pystac
from pystac.extensions.insar import InsarExtension

reference_datetime = datetime(2023, 2, 1, tzinfo=timezone.utc)
secondary_datetime = datetime(2023, 2, 13, tzinfo=timezone.utc)
item = pystac.Item(
    id="example-interferogram",
    geometry=None,
    bbox=None,
    datetime=secondary_datetime,
    properties={"title": "Synthetic InSAR example"},
)
insar = InsarExtension.ext(item, add_if_missing=True)
insar.apply(
    perpendicular_baseline=123.4,
    temporal_baseline=12,
    height_of_ambiguity=42,
    reference_datetime=reference_datetime,
    secondary_datetime=secondary_datetime,
    processing_dem="COP-DEM GLO-30",
    geocoding_dem="COP-DEM GLO-30",
)

serialized = item.to_dict()
assert InsarExtension.get_schema_uri() in serialized["stac_extensions"]
assert serialized["properties"]["insar:temporal_baseline"] == 12.0
assert serialized["properties"]["insar:reference_datetime"] == "2023-02-01T00:00:00Z"

restored_item = pystac.Item.from_dict(serialized)
restored = InsarExtension.ext(restored_item)
assert restored.reference_datetime == reference_datetime
assert restored.secondary_datetime == secondary_datetime
assert restored.processing_dem == insar.processing_dem
```

The numeric setters store floats. Datetime setters serialize Python datetimes to strings; getters convert them back to datetimes. Use timezone-aware datetimes to make acquisition times explicit.

This example uses the secondary acquisition as the core Item `datetime`. The wrapper does not choose the core timestamp or calculate a temporal baseline from acquisition dates; callers supply those values.

`to_dict()` serializes the Item without running JSON Schema validation. See [validation boundaries](../explanation/architecture.md#validation-boundaries) for the distinction.
