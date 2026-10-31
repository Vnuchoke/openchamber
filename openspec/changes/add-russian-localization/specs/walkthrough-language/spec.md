# Spec Delta

## Purpose

Let Russian-speaking users generate and read Changes Walkthrough in Russian while retaining accurate code references, cache behavior, and existing generation controls.

## ADDED Requirements

### Requirement: Russian walkthrough language

Walkthrough SHALL offer Russian wherever walkthrough generation already exists, default a new walkthrough to the interface language, and accept canonical or regional Russian locale tags without resolving them to English. Existing explicit language selections and the actual language of displayed results SHALL retain their current precedence.

#### Scenario: New walkthrough with Russian interface
- **WHEN** a user opens a new walkthrough with Russian selected as the interface language
- **THEN** Russian is the initial requested walkthrough language
- **AND** generation starts only after an explicit generation action

#### Scenario: Russian request normalization
- **WHEN** the user requests a walkthrough with `ru`, `ru-RU`, or `ru_RU`
- **THEN** the requested output language is Russian

### Requirement: Russian prose and literal technical values

A Russian walkthrough SHALL request Russian titles, focus text, chapter descriptions, step prose, observation notes, and routine-file notes. Identifiers, paths, API names, code anchors, structured field names, and enum values SHALL retain their original technical form.

#### Scenario: Generated Russian walkthrough
- **WHEN** a Russian walkthrough is generated
- **THEN** its human-readable explanations are Russian
- **AND** its technical values remain valid for locating and displaying the original changes

### Requirement: Language-specific results and truthful fallback

Russian and other-language results SHALL remain separately cached for the same diff and model. When a requested result is unavailable, the application SHALL identify the language actually displayed and retain the existing explicit regeneration workflow. The existing single-active-generation behavior SHALL remain unchanged.

#### Scenario: Switch between cached languages
- **WHEN** English and Russian walkthroughs exist for the same diff and model and the user switches between them
- **THEN** the matching cached result is displayed without another model request

#### Scenario: Russian result does not exist
- **WHEN** Russian is selected but only an English result is available
- **THEN** the existing result remains readable with a notice that it differs from the requested language
- **AND** the user can explicitly generate the missing Russian result

#### Scenario: Generation already running in another language
- **WHEN** a generation is active for the same source and another request asks for Russian
- **THEN** the existing single-active-generation policy is retained
- **AND** a result written in another language is not represented as a Russian result

### Requirement: Existing walkthrough failures remain actionable

Adding Russian SHALL preserve existing cancellation, unsupported-model, insufficient-context, empty-diff, and provider-error behavior. A failed generation SHALL NOT be represented as a successful empty Russian walkthrough or overwrite a valid prior result.

#### Scenario: Russian generation fails
- **WHEN** a provider fails while generating a Russian walkthrough over an existing result
- **THEN** the failure remains visible through the existing error handling
- **AND** the valid prior result remains available under its actual language
