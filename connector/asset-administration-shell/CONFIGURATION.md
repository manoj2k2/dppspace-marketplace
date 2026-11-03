# Configure the Asset Administration Shell connector

This guide uses the fictional AASX package in [`data/sample-product.aasx`](./data/sample-product.aasx) to create a Development connection in the Atlas DPP portal.

## Before you start

- Sign in to the workspace that should own the connection.
- The AASX file must be available to the connector service at a public HTTPS URL. A local file path such as `C:\...` cannot be fetched by the portal's connector service.
- The sample contains fictional product data and no credentials or production records.

After this file is committed and published to the Marketplace repository's `main` branch, its raw URL will be:

<https://raw.githubusercontent.com/manoj2k2/dppspace-marketplace/main/connector/asset-administration-shell/data/sample-product.aasx>

The URL will not work until the sample is published. If you host the sample elsewhere, use that public HTTPS URL instead. Do not put credentials, query strings, or fragments in the URL.

## Create the connection

1. Open [Create an AAS connection](https://portal.dppspace.com/connectors/connections/new?connectorType=aas) and create the connection in **Development** first.
2. Select **Asset Administration Shell (AAS)** (`aas`, version `1.0.0`).
3. Enter a descriptive connection name, for example `AASX sample product`.
4. For the endpoint, use the public HTTPS URL to `sample-product.aasx`.
5. Select **None** for authentication. AASX file imports do not use connector credentials.
6. Enable the **Product** capability and leave the schedule empty for manual runs.
7. In connector configuration, select **AASX package** (`sourceMode: aasx`). Leave IDTA template recognition set to **Auto** unless you have a specific semantic ID to recognize.
8. Review and save the connection. Run **Test connection** and confirm the AAS 3.0 environment is read successfully before configuring a mapping or running an import.

## Map and test the sample

Each AAS shell is imported as a Product source record. Its stable source record ID is the shell's AAS `id`; the record data contains the shell under `assetAdministrationShell` and its linked submodels under `submodels`. Use the discovered source fields and JSONPath mapping editor to map the sample's fictional product properties to the target Product fields.

Run a **DryRun** first and inspect the execution results. Keep the connection manual until the source, mappings, and target workspace have been verified.

## Supported behavior and limits

- The connector validates the AAS environment against the AAS 3.0 metamodel.
- AASX imports are read-only and support full synchronization only. Incremental synchronization and write-back to the source are not supported.
- The connector imports Product records. Review field mappings; importing an AAS does not automatically make every source property a passport field.
- IDTA 02006 Digital Nameplate and IDTA 02035 parts 1–7 can be recognized by semantic ID. This is not full template-specific validation or a compliance assessment.
- The portal file-import mode requires a public HTTPS URL without embedded credentials, query strings, or fragments.

The AAS Repository API mode is separate from this AASX example. API connections use bearer-token or API-key authentication; configure them only when the workspace has an approved way to provision the required tenant-scoped credential.
