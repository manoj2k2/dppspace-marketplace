# Battery Passport Industry Pack

## Partner configuration guide

This guide is for ERP and PLM consultants, sustainability advisers, manufacturers’ associations, and supply-chain networks that want to help manufacturers organize battery data and configure a repeatable product-passport workflow with [DPPspace](https://dppspace.com/).

It focuses only on batteries. The pack is a configurable starting point for factory data and readiness workflows—not a legal opinion, certification, or guarantee of regulatory compliance.

## 1. Regulatory foundation and scope

The primary source for EU battery-passport obligations is [Regulation (EU) 2023/1542 concerning batteries and waste batteries](https://eur-lex.europa.eu/eli/reg/2023/1542/oj/eng), particularly Article 77 and Annex XIII. Article 13(6) addresses the QR code used to provide access to battery information.

The Regulation provides for a battery passport from **18 February 2027** for each in-scope:

- electric vehicle battery;
- light means of transport (LMT) battery; and
- industrial battery with a capacity of more than 2 kWh,

when placed on the market or put into service. The exact product scope, information requirements, access rights, and operator responsibilities must be checked against the current Regulation and applicable implementing or delegated acts. Do not treat this list as a determination that a particular product is in scope.

Battery-passport obligations arise from the Battery Regulation. The Ecodesign for Sustainable Products Regulation (ESPR) is a broader framework; do not cite ESPR as a substitute for the battery-specific legal basis.

## 2. What partners contribute

| Partner | Contribution to the battery passport workflow |
|---|---|
| ERP and PLM consultants | Map product, model, batch or serial identity, bills of materials, manufacturing data, and supplier records from source systems; document data ownership and synchronization. |
| Sustainability advisers | Help clients define defensible carbon-footprint methods and boundaries, recycled-content data, origin information, evidence, and review responsibilities. |
| Manufacturers’ associations | Convene members to agree on common terminology, product-category conventions, evidence expectations, and reusable guidance—without presenting voluntary practices as legal requirements. |
| Supply-chain networks | Establish supplier-data requests, traceability handoffs, evidence collection, and clear rules for who may access or publish each data item. |

Partners should work with the manufacturer’s legal, quality, sustainability, engineering, procurement, and IT owners. The manufacturer remains responsible for its regulatory determinations and published claims.

## 3. Map requirements before configuring rules

For each product family, create a requirements-to-data map before setting a rule to `MANDATORY` or `Error`.

| Map item | Questions to resolve |
|---|---|
| Product scope | Is it an EV, LMT, or industrial battery? If industrial, what is its capacity? Which market and placing-on-market date are relevant? |
| Legal basis | Which specific Article, Annex, or current implementing/delegated act supports the data point? |
| Data item | What information must be maintained, and at what level (model, individual battery, or other applicable level)? |
| Source and owner | Which ERP, PLM, manufacturing, sustainability, or supplier system is authoritative? Who maintains it? |
| Evidence | What declaration, test report, calculation, verification, or other record supports the information? |
| Applicability | Which product categories, roles, dates, and circumstances make the item applicable? |
| Publication and access | Is the information intended for the public passport, a restricted audience, or internal review only? |

In DPPspace, available rule fields include product identity, materials, suppliers, manufacturing, carbon data, repair/disassembly, circularity, substances, and evidence. Use the field selector in the portal rather than assuming a field exists or that a field alone represents every legal data element.

## 4. Configure the pack in DPPspace

1. Sign in to the [DPPspace portal](https://portal.dppspace.com/) and open **Rules & Industry Packs** at `/rules`.
2. Select the **Batteries and EV components** template. Use **Customise a copy** if the template is already installed; otherwise choose **Use template**.
3. Give the pack a unique key, a clear name, and a description that states the product scope, version, and intended use.
4. Enter product categories using the same category names used by the products that will be validated. A rule only applies to its selected category (or all categories when configured that way).
5. Review the document types, then add or adapt rules using the available fields:
   - **Rule ID** — a stable identifier, such as `BAT-PCF`;
   - **Field to check** — select the DPPspace field that holds the data;
   - **Requirement** — `MANDATORY`, `CONDITIONAL`, or `OPTIONAL`;
   - **Severity** — `Error`, `Warning`, or `Info`;
   - **Regulation** — identify the legal source precisely, for example “Regulation (EU) 2023/1542, Article 77 / Annex XIII” where applicable;
   - **Applies to** — select the relevant product category; and
   - **Explanation** — tell the factory user what is missing and how to resolve it.
6. Save the pack, then test it against representative draft products in the intended product categories. Review the validation findings with the people who own the data before using the pack in production.

The battery template includes example checks for carbon-footprint data, recycled content, origin, suppliers, disassembly information, and evidence documents. These are **starter examples**, not a complete or universally applicable statement of legal requirements. Review each example’s applicability and severity with qualified regulatory and technical owners.

### Important validation limitation

The current portal editor exposes rule fields, requirement, severity, regulation, category, and explanation. It does not expose conditional expressions or effective/expiry dates. In the validation engine, both `MANDATORY` and `CONDITIONAL` are treated as required when no condition has been configured. Therefore:

- do not use `CONDITIONAL` in the editor as if it automatically captures a legal threshold, date, battery role, or other scope condition;
- use category-specific rules only where product category is a sufficient applicability boundary; and
- record unresolved date-, threshold-, or role-dependent applicability for expert review, and do not rely on the validation result to decide it.

Validation reports are data-readiness aids. A passing result means only that the configured checks passed; it is not a compliance determination.

## 5. Turn the pack into a factory workflow

Use the industry pack as part of a controlled workflow rather than as a standalone checklist:

1. **Pilot one product family.** Agree the battery category, product identity conventions, data owners, and publication audience.
2. **Connect source data.** Map the authoritative ERP/PLM and factory records. DPPspace’s integrations catalog describes available SAP S/4HANA OData, Generic REST/JSON, and CSV/Excel options; confirm the chosen connector and mapping in the customer environment.
3. **Assign ownership.** For example, engineering may own model and materials data, sustainability may own carbon-footprint calculations, procurement may own supplier inputs, and quality may own supporting records and approval.
4. **Collect evidence and review.** Attach appropriate supporting documents, check their validity and ownership, and resolve missing or inconsistent data with the responsible factory team.
5. **Validate and approve.** Run configured readiness checks, review errors and warnings, and have authorized staff approve the information and claims before publication.
6. **Publish deliberately.** Review the public passport and QR-linked page to ensure that only intended information is disclosed. Do not assume that publishing a DPPspace page submits information to an EU registry or satisfies any separate registration obligation.
7. **Maintain versions.** Define who updates records after product, supplier, evidence, or regulatory changes, and retain an auditable review process.

For a product-level introduction, see the [DPPspace battery passport page](https://dppspace.com/battery-passport/).

## 6. Pilot acceptance checklist

- The pack states its product categories, version, intended users, and limitations.
- Every rule has a traceable source, data owner, explanation, and reviewed severity.
- Product category names match the records being validated.
- Conditional legal applicability is documented and reviewed separately where the current editor cannot encode it.
- Representative products have been tested, and reported findings are understood by factory data owners.
- Evidence is linked to the data it supports and reviewed by an authorized person.
- Public content and QR-linked views have been checked for accuracy and appropriate disclosure.
- The manufacturer has approved the workflow and remains accountable for its regulatory interpretation.

## 7. Partner onboarding

ERP/PLM consultancies, sustainability advisers, associations, and supply-chain networks can use this guide to develop a battery-specific implementation with a manufacturer. Start at [DPPspace](https://dppspace.com/) or [create a portal account](https://portal.dppspace.com/register), then agree the product scope, data map, and pilot owners before configuring a pack.

## Sources and disclaimer

- [Regulation (EU) 2023/1542 — official EUR-Lex text](https://eur-lex.europa.eu/eli/reg/2023/1542/oj/eng)
- [DPPspace battery passport overview](https://dppspace.com/battery-passport/)
- [DPPspace integrations catalog](https://dppspace.com/integrations/)

This guide is operational guidance, not legal advice. Regulatory requirements and implementation measures may change. Verify the current official legal text and obtain qualified advice for product-specific obligations. DPPspace is not an EU institution and this guide does not imply EU endorsement or certification.
