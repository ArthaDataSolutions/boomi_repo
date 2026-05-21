# Sample Dell Boomi Processes (Full Export Layout)

Three realistic Dell Boomi process exports used to demo the
Boomi → Python/Prefect conversion path of `talend-to-python`.
Each process is laid out the way Boomi's AtomSphere "Export" produces
a process archive: a `process.xml` plus referenced **components**
(connections, operations, profiles, maps, process properties) under
`components/`, deployment-time `extensions.xml`, and a `manifest.xml`
listing every component GUID.

```
JsonToXml_OrderIntake/
├── process.xml
├── manifest.xml
├── extensions.xml
└── components/
    ├── connections/
    ├── operations/
    ├── profiles/
    ├── maps/
    └── processproperties/
```

## Processes

| Folder | Use case | Pattern |
|--------|----------|---------|
| `JsonToXml_OrderIntake/` | Receive JSON purchase orders via Web Services Listener, transform to cXML, POST to SAP ERP. | Real-time API → Map → HTTP Connector |
| `XmlToJson_PartnerCatalogSync/` | Nightly SFTP pull of supplier XML catalog, transform to JSON, POST to storefront API. | Scheduled SFTP → XML profile → Map → JSON REST |
| `OAuthApi_CustomerSync/` | Hourly OAuth2 `client_credentials` token, paged GET of CRM customers, upsert into Postgres warehouse. | OAuth → Authenticated REST → Map → DB upsert |

## Component types covered

- **Connection** — endpoint config (HTTP / SFTP / Database / Web Services Listener)
- **Operation** — connector action (GET, POST, UPSERT) with profiles, headers, pagination, retry policy
- **Profile** — JSON / XML / Database schema definitions
- **Map** — field-to-field mappings with map functions (`DateNow`, `LowerCase`, `DefaultIfNull`, …)
- **Process Property** — dynamic properties (correlation IDs, watermarks, OAuth state)
- **Extensions** — per-environment overrides referencing `${PLACEHOLDERS}`

## Environment overrides

`extensions.xml` in each process references `${VARIABLE}` placeholders
that map to the entries in [`.env.example`](.env.example). Copy
`.env.example` → `.env`, fill in real values, and the converted Python
flows can load them via `python-dotenv` / `os.environ`.

## Convert with talend2py

```bash
# Convert one process (point at the process.xml)
talend2py convert samples/boomi/JsonToXml_OrderIntake/process.xml -o output/

# Or upload the same file via the Web UI's Quick Convert tab
# with source_platform = "boomi"
```

The current parser (`src/parser/boomi_parser.py`) keys off `<shape>` +
`<connection>` in `process.xml`. Component XMLs and `extensions.xml`
are provided here for fidelity and for future parser work that can
follow `componentRef` GUIDs into the full Boomi component graph.
