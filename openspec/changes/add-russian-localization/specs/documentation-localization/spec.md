# Spec Delta

## Purpose

Make the full OpenChamber documentation source available in Russian in the personal repository, with complete navigation and verifiable structural consistency independent of website publication.

## ADDED Requirements

### Requirement: Complete Russian documentation source

The documentation source SHALL provide a Russian counterpart for every current English documentation page, preserving its relative path beneath a `ru` locale directory. The implementation SHALL cover the complete corpus, including nested SDK and troubleshooting pages; the planning baseline is 59 English pages.

#### Scenario: Complete source tree
- **WHEN** the Russian documentation is considered complete
- **THEN** its relative page-path set matches the English source set
- **AND** it includes Walkthrough and all nested SDK and troubleshooting pages

### Requirement: Translated content and preserved technical material

Russian pages SHALL contain translated titles, descriptions, headings, and explanatory prose. Brand names, filenames, route identities, configuration keys, command invocations, and technical code examples SHALL retain their intended literal meaning and validity.

#### Scenario: Walkthrough documentation
- **WHEN** the user reads the Russian Walkthrough source page
- **THEN** the explanation of language selection is Russian and describes the supported Russian walkthrough behavior
- **AND** technical references and examples remain usable

#### Scenario: PR/MR language documentation
- **WHEN** the user reads the Russian Git and Magic Prompts documentation
- **THEN** it explains the UI-language default, localized built-in headings, preserved repository templates, and explicit prompt language overrides

### Requirement: Complete Russian navigation and valid local references

Every app-owned documentation navigation section and item SHALL have a Russian label without duplicating navigation entries or changing their canonical routes. Russian pages SHALL have the required metadata and valid local page, fragment, and asset references under the existing documentation conventions.

#### Scenario: Russian sidebar labels
- **WHEN** the Russian navigation translations are inspected
- **THEN** each existing section and item has a nonempty Russian label and retains its canonical link

#### Scenario: Invalid translated source
- **WHEN** a Russian page, required metadata field, navigation translation, or local reference is missing or invalid
- **THEN** focused documentation checks report the affected source path or navigation entry
- **AND** the documentation is not reported as complete solely because its total page count appears correct

### Requirement: Source-only delivery

Completion SHALL require translated source and navigation in this repository, not rendering or publication on a website. The change SHALL NOT modify another repository, deployment configuration, hosting, or upstream locale policy to deliver the documentation.

#### Scenario: Handoff without website changes
- **WHEN** all Russian sources and navigation pass their source checks
- **THEN** documentation source delivery can be accepted without a running Russian website
- **AND** the handoff explicitly states that website registration, rendering, and deployment were not performed
