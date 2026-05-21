# Sample Dell Boomi Processes

Three realistic Boomi process exports used to demo the Boomi → Python/Prefect
conversion path of `talend-to-python`. Each XML follows the parser's expected
shape (see `src/parser/boomi_parser.py`): a root `<process>` element with
`<shape>` children and `<connection>` edges.

| File | Use case | Pattern |
|------|----------|---------|
| `JsonToXml_OrderIntake.xml` | Receive JSON purchase orders via Web Services Listener, transform to cXML, POST to SAP ERP. | Real-time API → Map → HTTP Connector |
| `XmlToJson_PartnerCatalogSync.xml` | Nightly SFTP pull of supplier XML catalog, transform to JSON, POST to storefront API. | Scheduled SFTP → XML profile → Map → JSON REST |
| `OAuthApi_CustomerSync.xml` | Hourly OAuth2 `client_credentials` token, paged GET of CRM customers, upsert into Postgres warehouse. | OAuth → Authenticated REST → Map → DB upsert |

## Try locally

```bash
talend2py convert samples/boomi/JsonToXml_OrderIntake.xml -o output/
talend2py convert samples/boomi/XmlToJson_PartnerCatalogSync.xml -o output/
talend2py convert samples/boomi/OAuthApi_CustomerSync.xml -o output/
```

Or via the Web UI: choose **Boomi** as the source platform in **Quick Convert**
and upload any of the three files.
