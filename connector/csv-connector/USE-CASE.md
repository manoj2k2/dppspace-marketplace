# Use case: Import catalog data from CSV or Excel

## Goal

Import product and related supply-chain records from a hosted CSV, XLS, or XLSX file into the selected DPPSpace workspace.

## Actor

Workspace administrator configuring a source connection.

## Preconditions

- The source file is available at a public HTTPS URL. The CSV / Excel connector does not offer source authentication.
- The file contains a header row, unless the connection is explicitly configured otherwise.
- A stable, non-empty source ID column is available to identify records across imports.
- The workspace administrator knows the intended target entities and source-to-target field mappings.

## Sample file

[`sample-products.csv`](./sample-products.csv) contains fictional product rows for a safe development import. It uses a pipe delimiter, `ProductNumber` as the stable source ID, and `UpdatedAt` as the optional incremental-sync watermark. The published source is available at:

<https://raw.githubusercontent.com/manoj2k2/dppspace-marketplace/main/connector/csv-connector/sample-products.csv>

Map the sample fields to the workspace's product fields before importing. The sample intentionally contains no production company or supplier data.

[`sample-material.csv`](./sample-material.csv) contains two fictional material rows for each unique `ProductNumber` in the product sample. Use `MaterialNumber` as its stable source ID and `ProductNumber` to cross-reference the source product. The [`Integration API OpenAPI specification`](https://portal.dppspace.com/swagger/integration-v1/swagger.json) defines material writes through `POST /api/materials` with `CreateMaterialMasterDto`; product-scoped connector imports require `ProductId` to contain the actual DPP product UUID. Populate that column after importing the products—`ProductNumber` is not a valid target UUID.

## Main flow

1. Open **Connector marketplace** and select **CSV / Excel** (`csv-excel`).
2. Create a connection with a descriptive name, environment, and public HTTPS file URL.
3. Choose the time zone and, if needed, a cron schedule. Leaving the schedule blank keeps runs manual.
4. Select the entity types to import: Product, Material, BOM, Supplier, Document, Evidence, or Product Carbon Footprint.
5. Configure the worksheet for Excel files, header-row handling, CSV delimiter, required stable record ID column, and optional modified timestamp column. For the sample file, use a pipe (`|`) delimiter, `ProductNumber`, and `UpdatedAt`.
6. Review and create the connection.
7. Configure source-to-target mappings, run an initial import, and inspect execution results for validation errors.

## Success outcome

The connector imports the selected records, retains their source identity for later synchronization, and exposes run status and errors in the connector execution history.

## Exception paths

- **File cannot be fetched:** confirm that the URL is publicly reachable over HTTPS and that the file is in a supported format.
- **Rows do not have stable identifiers:** add a unique ID column before configuring the connector.
- **Incorrect delimiter or worksheet:** correct the connection configuration and retry the import.
- **Records fail validation or map incorrectly:** adjust the field mappings and review the next execution before scheduling recurring imports.

## Portal review notes

- The connector marketplace lists CSV / Excel as available (`csv-excel`, version `1.0.0`) and documents support for public HTTPS file URLs.
- The connection wizard has four stages: connector, connection details, configuration, and review.
- The wizard defaults the environment to Production. This use case uses Development, manual-only scheduling, and the sample file's pipe delimiter, `ProductNumber` record ID, and `UpdatedAt` watermark.
- The published sample file responds over HTTPS. `DPP sample products (CSV)` is configured in Development with manual-only scheduling, tested successfully, and activated.
- The Product v1 mapping is published with seven fields. A manual DryRun succeeded for both sample rows with zero created, updated, or failed records; no live product import has been run.
- Initial source-field discovery failed because the snapshot volume was root-owned while the API and worker run as UID 1654. The existing volume ownership was corrected, and both container images now create the snapshot directory with the application user's ownership.
- The mapping form previously sent source paths for Direct mappings, which the API rejects. The UI payload was corrected, and mapping preview now displays the API's mapped-record response.
- The review step does not display the delimiter, record ID, or watermark configuration, although those settings are visible on the saved connection details.
- The portal Document Library rejects `.md` uploads (`File type '.md' is not allowed`), so this use-case file is stored in the marketplace repository only.
