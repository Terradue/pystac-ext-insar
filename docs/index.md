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

# InSAR PySTAC extension

`pystac-ext-insar` reads and writes interferometric synthetic aperture radar (InSAR) metadata on PySTAC objects using the `insar:` prefix. Import the API from `pystac.extensions.insar`.

The implementation declares the schema identifier `https://stac-extensions.github.io/insar/v1.0.0/schema.json`. The Python distribution version is independent of the extension specification version.

```bash
python -m pip install pystac-ext-insar
```

- [Create an InSAR Item](tutorials/first-steps.md) and round-trip its metadata.
- [Work with InSAR metadata](how-to/use-extension.md) on Items, Assets, item asset definitions, and Collection summaries.
- [Look up fields](reference/fields.md), units, and validation behavior.
- [Browse the Python API](reference/api.md).
- [Understand the architecture](explanation/architecture.md) and validation boundaries.
