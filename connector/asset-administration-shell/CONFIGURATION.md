# Configure the Asset Administration Shell connector

This guide uses the fictional AASX package in [`data/sample-product.aasx`](./data/sample-product.aasx) to create a Development connection in the Atlas DPP portal.

## Learn about Atlas DPP AAS integration

See the [Atlas DPP integrations catalog](https://dppspace.com/integrations/) for supported connection options. The [Asset Administration Shell integration guide](https://dppspace.com/blog/asset-administration-shell-aas-integration-atlas-dpp/) covers AAS 3.0 Repository API, AASX, and AAS JSON imports and their current limitations.

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


No. An AASX file does not automatically contain all the information required for a Digital Product Passport (DPP).

An AASX file is a package containing an Asset Administration Shell (AAS) and its submodels, files and related data. It can provide a substantial amount of structured product information, but whether it is sufficient depends on:

1. Which product category the DPP is for (battery, electronics, textiles, etc.).
2. Which regulatory requirements apply.
3. Which submodels and data fields the manufacturer has actually populated.
4. Whether the information is accurate, complete, current and accessible to the intended users.

For DPPspace, I would treat AASX as a potential source of product data—not as a complete, automatically compliant DPP.

## 1. Compare AASX content with battery DPP requirements

For a battery passport, the EU Battery Regulation (EU) 2023/1542, particularly Annex XIII, defines the information categories. These include public, restricted and individual-battery information.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://eur-lex.europa.eu\&sz=32)

EUR-Lex+1



| Battery DPP information                                | Can AASX contain it?                       |
| ------------------------------------------------------ | ------------------------------------------ |
| Manufacturer, battery model and identification         | Yes                                        |
| Battery chemistry and material composition             | Yes                                        |
| Capacity, voltage and power                            | Yes                                        |
| Battery lifetime and performance                       | Yes                                        |
| Carbon footprint                                       | Yes, if modelled and populated             |
| Recycled content and responsible sourcing              | Yes, if modelled and populated             |
| EU Declaration of Conformity and supporting documents  | Yes, as document references or attachments |
| Detailed composition of cathode, anode and electrolyte | Yes                                        |
| Dismantling instructions and diagrams                  | Yes                                        |
| Test reports                                           | Yes, as documents or structured data       |
| Individual battery serial number and status            | Yes                                        |
| State of health, charging cycles and usage history     | Yes, with dynamic data integration         |

The important distinction is that AAS supports the representation of this information, but does not guarantee that it is present.

## 2. What the AASX structure might look like

An AASX battery package could contain submodels such as:

Battery_AASX.aasx

Asset Administration Shell

Digital Nameplate

Technical Data

Battery Characteristics

Material Composition

Carbon Footprint

Performance and Durability

Battery Passport Metadata

Documentation and Certificates

Dismantling Information

Battery Lifecycle / Usage Data

Illustrative structure, not a mandatory list of IDTA submodels.

You would need to select suitable IDTA submodel templates and define additional mappings where required. Not every item above necessarily has a standardised template that directly satisfies the relevant legal requirement.

## 3. Important: EU requirements are evolving

The European Commission's current Battery Passport FAQ clarifies that not every field is mandatory from February 2027. Some information applies only to certain battery categories or at later dates. For example, carbon footprint, recycled content and due diligence information have separate application dates.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://single-market-economy.ec.europa.eu\&sz=32)

Internal Market SME+1



Therefore, DPPspace needs a versioned regulatory validation model—not simply a fixed list of mandatory fields.

## 4. My recommendation for DPPspace

Build an AASX-to-DPP conversion and validation engine.

AASX / AAS JSON Import

Read submodels, semantic IDs, files and references

Semantic Mapping Engine

Map AAS properties to battery DPP data points

Regulatory Validation Engine

Check missing, invalid, conditional and restricted fields

DPPspace Passport

Generate structured DPP JSON, manage access and documents, publish passport

The engine should produce three useful outputs:

- Mapped: Information successfully extracted from AASX.
- Missing: Required information not found in the AASX package.
- Needs verification: Information found but requiring validation of its value, source or evidence.

### 5. A commercial opportunity

I would position this as a specific DPPspace feature:

"Convert your existing AASX digital twin into a regulatory-ready Digital Product Passport."

This gives DPPspace two possible customer entry points:

- Companies already using AAS: import their existing asset information.
- Companies without AAS: create passports directly using DPPspace's native data model.

One important caveat: successful conversion and validation do not themselves certify legal compliance. The responsible economic operator remains accountable for the accuracy and completeness of the passport.

My next recommendation: Build the AASX importer first, then test it against one real battery AASX package and the Commission's battery data-point guidance. That will reveal exactly which regulatory fields are covered and which need additional data sources.