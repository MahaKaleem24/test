# DeX Source Documentation Repository

A repository of source documentation used to populate Device Extension (DeX) records. All documents are stored in AsciiDoc (`.adoc`) format to support AI-assisted DeX record creation and tooling integrations.

---

## Purpose

This repository centralizes source documents for DeX record population, including:

- **GUDID documentation** — Global Unique Device Identification Database records
- **FDA 510(k) decision summaries** — FDA clearance decision summary documents
- **Package Inserts / Instructions for Use (IFU)** — manufacturer-supplied documentation
- **Linked devices** — relationships to related instruments, reagents, and other devices

The repository currently covers in-vitro diagnostic (IVD) devices and their approved instruments and reagents, with planned expansion to non-IVD devices as sibling top-level folders.

---

## Repository Structure

Devices are organized in a four-level hierarchy: top-level device group → category → GMDN term → device. Each device (IVD, instrument, reagent, etc.) has its own folder named by its **Primary DI number**, and that folder holds the source documents.

```
/
└── IVDs/                        # Top-level device group
    └── {category}/              # Category folder
        └── {gmdn-term}/         # GMDN term folder
            └── {primary-DI}/    # One folder per device
                ├── gudid.adoc
                ├── 510k-decision-summary.adoc
                ├── package-insert.adoc
                └── linked-devices.adoc
```

Example layout:

```
/
└── IVDs/
    ├── genomics/
    │   ├── {gmdn-term-1}/
    │   │   ├── {primary-DI-1}/        # e.g., an assay / reagent
    │   │   │   ├── gudid.adoc
    │   │   │   ├── 510k-decision-summary.adoc
    │   │   │   ├── package-insert.adoc
    │   │   │   └── linked-devices.adoc
    │   │   └── {primary-DI-2}/
    │   │       └── ...
    │   └── {gmdn-term-2}/
    │       └── {primary-DI-3}/        # e.g., an instrument
    │           └── ...
    ├── chemistries/
    │   └── ...
    └── at-home-tests/
        └── ...
```

> Not all document types will be present for every device. Include only what is available and applicable.

### Folder Levels

| Level | Folder | What it represents |
|---|---|---|
| 1 | `IVDs/` | Top-level device group. Future non-IVD groups sit alongside it at the repository root. |
| 2 | `{category}/` | Device category within the group (e.g., `at-home-tests`, `genomics`, `chemistries`, etc). Lowercase, hyphenated. |
| 3 | `{gmdn-term}/` | The GMDN (Global Medical Device Nomenclature) term name for the device type. See naming conventions below. |
| 4 | `{primary-DI}/` | The device itself, named by its Primary DI number. Applies equally to IVDs, instruments, reagents, calibrators, and controls. |

---

## Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Top-level group | `IVDs` | `IVDs/` |
| Category folder | Lowercase, hyphens, no spaces | `genomics/`, `chemistries/`, `at-home-tests/`, etc |
| GMDN folder | GMDN term name, lowercase, hyphens, no spaces | `{gmdn-term-name}/` |
| Device folder | Primary DI number | `00884728123456` |
| GUDID document | `gudid.adoc` | `gudid.adoc` |
| 510(k) decision summary | `510k-decision-summary.adoc` | `510k-decision-summary.adoc` |
| Package Insert / IFU | `package-insert.adoc` | `package-insert.adoc` |
| Linked devices file | `linked-devices.adoc` | `linked-devices.adoc` |

All folder and file names are lowercase with hyphens. No spaces. The one exception is the top-level `IVDs` folder, which keeps its capitalization for readability.

---

## Linking Devices

Devices that work together (an IVD reagent and its approved instruments, calibrators, controls, and so on) each live in their own device folder, and may sit under different categories or GMDN folders. Relationships are documented in a `linked-devices.adoc` file inside each device folder.

Because linked devices can live anywhere in the hierarchy, each entry records the relationship type, the Primary DI, the device name, and the relative path to that device's folder.

```asciidoc
= Linked Devices: {Primary DI}

== Approved Instruments

* {Instrument Primary DI} — {Instrument name}
  (`../../{gmdn-term}/{Instrument Primary DI}/`)

== Reagents

* {Reagent Primary DI} — {Reagent name}
  (`../../{gmdn-term}/{Reagent Primary DI}/`)
```

Use whichever section headings apply to the device — for example Approved Instruments, Reagents, Calibrators, Controls, Accessories. Omit sections that do not apply.

Link in both directions: if an IVD lists an instrument, the instrument's `linked-devices.adoc` should list that IVD (under a heading such as Approved IVDs / Assays).

---

## AsciiDoc Standards

All source documents are stored as AsciiDoc (`.adoc`) files. We are currently testing a set of ingestion standards to define the `.adoc` format of source documents. These standards are a work in progress and may change as testing continues.

So far, the standards below have been used to define the format for 510(k) decision summaries:

- [ike-build-standards/src/main/standards](https://github.com/triplehamburger/ike-tooling/tree/generated-topic-registry/ike-build-standards/src/main/standards) — standards directory
- [IKE-TOPIC-REGISTRY.md](https://github.com/triplehamburger/ike-tooling/blob/generated-topic-registry/ike-build-standards/src/main/standards/IKE-TOPIC-REGISTRY.md) — topic registry
- [IKE-INGEST.md](https://github.com/triplehamburger/ike-tooling/blob/generated-topic-registry/ike-build-standards/src/main/standards/IKE-INGEST.md) — general ingestion standard
- [IKE-DEX-INGEST.md](https://github.com/triplehamburger/ike-tooling/blob/generated-topic-registry/ike-build-standards/src/main/standards/IKE-DEX-INGEST.md) — DeX ingestion standard

### Converting a Source Document to .adoc

Conversion is run through Claude using the ike-docs tooling.

**Preliminary:** make sure Maven 4.0 and Java 25 or later are installed on your computer.

1. **Get the branch:** clone the `ingest-header-attributes` branch.

   ```
   git clone -b ingest-header-attributes https://github.com/triplehamburger/ike-docs
   ```

2. **Build and install:** from the cloned folder, run Maven.

   ```
   cd ike-docs
   mvn install
   ```

3. **Ingest:** open Claude and ask it to ingest the document into the target folder.

   ```
   Ingest [doc name] to [file path]
   ```

4. **Read and approve the header:** review the generated header before accepting the converted file.

---

## Adding a New Device

1. Identify the device's group (e.g., `IVDs`), category, and GMDN term.
2. Navigate to `IVDs/{category}/{gmdn-term}/`, creating the category and GMDN folders if they do not exist yet.
3. Create a new device folder named after the device's Primary DI number.
4. Add available source documents using the standard file names above.
5. Create a `linked-devices.adoc` file referencing any related instruments, reagents, or other devices, and update the `linked-devices.adoc` in each related device's folder to point back.
6. Convert each source document to `.adoc` following the ingestion process in the AsciiDoc Standards section.

---

## Tooling Integrations

This repository is designed for integration with:

- **DeX Record Creation Tool** — accesses `.adoc` files programmatically to assist with record population
- **Komet** — planned future integration for authoring of DeX records

Tools can locate a device by its Primary DI by searching for a folder of that name at depth four beneath the repository root, or can browse by category and GMDN term to find all devices of a given type.

---

## Summary of Changes from Previous Structure

| Item | Before | After |
|---|---|---|
| Device location | `/{primary-DI}/` at repository root | `/IVDs/{category}/{gmdn-term}/{primary-DI}/` |
| 510(k) file | `510k.adoc` | `510k-decision-summary.adoc` |
| Relationships file | `device-links.adoc` | `linked-devices.adoc` |
| Link scope | One way link between devices | Any device ↔ instruments, reagents, calibrators, controls, etc. (two way link) |
