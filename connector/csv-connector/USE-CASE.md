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

## Main flow

1. Open **Connector marketplace** and select **CSV / Excel** (`csv-excel`).
2. Create a connection with a descriptive name, environment, and public HTTPS file URL.
3. Choose the time zone and, if needed, a cron schedule. Leaving the schedule blank keeps runs manual.
4. Select the entity types to import: Product, Material, BOM, Supplier, Document, Evidence, or Product Carbon Footprint.
5. Configure the worksheet for Excel files, header-row handling, CSV delimiter, required stable record ID column, and optional modified timestamp column.
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
- The wizard defaults the environment to Production. For a first import, select a non-production environment where available.
- No existing connections were listed in the selected demo workspace during this review. No connection was created because a real source URL and source schema were not provided.
