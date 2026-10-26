# Connector adapter developer guide

This guide is for integration developers implementing or configuring a source adapter for DPPSpace Connect.

## Understand the two parts

The Marketplace repository contains adapter descriptions, setup guides, use cases, and safe sample data. It does not load connector code at runtime. Runtime source adapters are implemented in `dpp-connector`, registered in the API and worker, and deployed with those services.

Before writing an adapter, check whether an existing connector is sufficient:

- `csv-excel` reads public HTTPS CSV, XLS, and XLSX files. See the [CSV connector use case](./csv-connector/USE-CASE.md).
- `generic-rest` reads JSON REST APIs and supports configured authentication, JSONPath record selection, pagination, and watermarks.

Prefer configuring an existing connector when it meets the source system's requirements. Create a custom adapter when the source needs protocol-specific behavior that the existing connectors cannot express.

## Implement a source adapter

The connector contracts are in `DPPSpace.Connect.SDK`. Implement `ISourceConnector` and the capability marker interfaces for every entity type the adapter actually supports.

| Contract member | Responsibility |
| --- | --- |
| `Descriptor` | Publish a stable connector type, display name, version, description, authentication methods, capabilities, incremental support, configuration JSON Schema, secret field names, and supported source versions. |
| `TestConnectionAsync` | Check connectivity and basic source readability. Honor cancellation and return a meaningful result; do not write target data. |
| `GetMetadataAsync` | Describe available source fields and source-system versions for mapping setup. |
| `ReadAsync` | Stream source records as `SourceRecord` values. Do not call the DPPSpace target API from a source adapter. |

Each `SourceRecord` must have a stable, non-empty `SourceRecordId`, an optional UTC `SourceModifiedAt`, and a JSON object in `Data` that can be mapped to the selected canonical entity. Use `ConnectorExecutionContext` for the tenant-scoped connection configuration, correlation ID, optional watermark, and optional requested record IDs. Honor cancellation and avoid buffering an entire large source when it can be streamed.

If incremental synchronization is supported, implement `IIncrementalSyncConnector`, set `SupportsIncrementalSync` in the descriptor, and populate `SourceModifiedAt` consistently. The marker interface, descriptor capabilities, and supported entity marker interfaces must agree; the connector registry validates unique connector type values at startup.

Connection configuration belongs in the descriptor's JSON Schema and contains non-secret settings only. List secret names separately in `SecretFields`; the runtime supplies opaque secret references through `ConnectorContext.SecretReferences`. Resolve secrets with `IConnectorSecretResolver`. Never put secret values in configuration, source data, logs, exceptions, or Marketplace files. The current portal does not provision connector credentials; a connector requiring secrets needs an approved tenant-scoped secret provider and credential-provisioning workflow before it can be used there.

For outbound HTTP, follow the existing connectors' `HttpClient` and `SsrfProtectionPolicy` patterns. Restrict destinations to the intended public or explicitly approved hosts, reject unsafe redirects, apply timeouts and response-size limits, and avoid logging credentials or sensitive headers.

## Register and validate the adapter

1. Add the adapter to the existing connector assembly, or create a dedicated connector project if the implementation warrants a separate boundary.
2. Add a dependency-injection extension that registers the implementation as `ISourceConnector` and configures any required services.
3. Call that registration from **both** `DPPSpace.Connect.Api/Program.cs` and `DPPSpace.Connect.Worker/Program.cs`. API-only registration allows discovery and tests but leaves background synchronization unable to resolve the adapter.
4. Add focused xUnit tests in `DPPSpace.Connect.Tests`. Use fake HTTP handlers or other deterministic test doubles; cover descriptor registration, connection test results, metadata, record identity and JSON shape, pagination/watermarks where applicable, cancellation, and failure handling. Do not require production credentials or a live source.
5. From the `dpp-connector` directory, build and run the test project:

   ```powershell
   dotnet build .\DPPSpace.Connect.sln
   dotnet test .\DPPSpace.Connect.Tests\DPPSpace.Connect.Tests.csproj
   ```

6. Rebuild and start both runtime hosts so the API and worker use the same adapter version:

   ```powershell
   docker compose --env-file .env --env-file .env.local -f docker-compose.yml up --build -d connect-api connector-worker
   ```

7. Confirm the adapter appears in the connector discovery endpoint and that both services start without registration or dependency errors.

## Configure the adapter in the portal

1. Create a connection as a **Draft** in Development first. Select the adapter's exact type and version, then enter its endpoint and non-secret configuration.
2. Select only the entity capabilities implemented by the adapter. Provide a stable record ID field and, for incremental sync, a reliable modified timestamp.
3. Keep scheduling manual during setup. Do not add a recurring schedule until the test, mappings, and initial validation have passed.
4. Run **Test connection** and confirm source metadata or fields can be discovered.
5. Create a mapping for each entity type. Map source fields to the target schema, using JSONPath against the `SourceRecord.Data` object (for example, `$.ProductName` or `$["Product Name"]`).
6. Preview a representative, fictional source record. Publish the mapping only after the output matches the target schema.
7. Run a **DryRun** and check the execution record and counts before any write-enabled import. Confirm the target tenant, company, and any required parent IDs before running a live import.
8. Activate the connection and choose either manual runs or an approved schedule. A blank schedule means manual only.

## Publish adapter materials in the Marketplace

Create a folder named for the adapter under `connector/`. Include a `README.md` or use-case document with:

- Connector type, version, supported capabilities, and authentication requirements.
- Prerequisites, endpoint/configuration fields, and source ID/watermark requirements.
- A source-to-target mapping example and a validation or DryRun procedure.
- Supported source versions, pagination/rate limits, file or payload limits, and known limitations.
- Fictional sample data where useful; never include production records, credentials, or secret-bearing URLs.

Update the Marketplace index or repository README when adding a new adapter guide so integration developers can find it.
