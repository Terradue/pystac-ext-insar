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

# InSAR fields

The following properties are exposed by `InsarExtension` for Items, Assets, and item asset definitions. Values are stored in Item `properties`, Asset `extra_fields`, or the item asset definition's `properties` dictionary.

| STAC field | Python property | Meaning and setter behavior |
| --- | --- | --- |
| `insar:perpendicular_baseline` | `perpendicular_baseline` | Perpendicular baseline in meters; accepts integers and floats and stores a float. |
| `insar:temporal_baseline` | `temporal_baseline` | Temporal baseline in days; accepts integers and floats and stores a float. |
| `insar:height_of_ambiguity` | `height_of_ambiguity` | Height of ambiguity in meters; accepts integers and floats and stores a float. |
| `insar:reference_datetime` | `reference_datetime` | Reference acquisition datetime; accepts a Python `datetime` and stores a string. |
| `insar:secondary_datetime` | `secondary_datetime` | Secondary acquisition datetime; accepts a Python `datetime` and stores a string. |
| `insar:processing_dem` | `processing_dem` | DEM used during interferogram processing; accepts a string. |
| `insar:geocoding_dem` | `geocoding_dem` | DEM used during geocoding; accepts a string. |

Every getter can return `None` for an absent field. Every setter accepts `None` to remove its field. `apply()` defaults all arguments to `None`, so omitted values are removed rather than preserved.

## Runtime checks

Numeric setters reject booleans and nonnumeric values with `ValueError`. They do not enforce ranges, finiteness, or consistency with acquisition dates. DEM setters reject non-string values with `ValueError`; they do not check whether a DEM identifier exists or a URL is reachable, and they accept empty strings.

Datetime getters parse stored strings using PySTAC's datetime utilities. Use timezone-aware Python datetimes when writing acquisition times. Reading existing metadata does not run the setter checks again.

## Collection summaries

`InsarExtension.summaries(collection)` exposes these properties:

| Python property | Stored summary |
| --- | --- |
| `reference_datetime` | Single-element list containing a datetime string. |
| `processing_dem` | Single-element list containing a DEM string. |
| `geocoding_dem` | Single-element list containing a DEM string. |

Summary getters return the first element, or `None` when no list is available or the list is empty. Reference datetimes are parsed to Python datetimes. A non-string first element raises `ValueError`, except that a first element of `None` is returned as `None`.

Setters replace the entire list with a single-element list. Assigning `None` removes the summary. The helper does not expose baseline, height-of-ambiguity, or secondary-datetime summaries, and does not aggregate Item values automatically.
