# Spec Delta

## Purpose

Generate PR/MR titles and new description text in the user's interface language by default while respecting repository templates and explicit user language preferences.

## ADDED Requirements

### Requirement: Interface-language default per operation

PR/MR generation from the existing shared interface SHALL default the title and newly authored body text to the interface language selected when generation starts. The same language intent SHALL reach the existing primary and fallback generation transports. A later interface-language change SHALL NOT alter an in-flight request.

#### Scenario: Russian interface
- **WHEN** the user starts PR/MR generation with Russian selected and no explicit prompt language override
- **THEN** the request asks for a Russian title and newly authored description text

#### Scenario: Different language and mid-generation switch
- **WHEN** generation begins in another supported interface language and the user switches the interface before it completes
- **THEN** the in-flight request retains its starting language
- **AND** a subsequent generation uses the newly selected language

#### Scenario: Primary generation unavailable
- **WHEN** the existing generation workflow uses its session fallback
- **THEN** the fallback receives the same language intent as the primary request

### Requirement: Localized built-in section headings

When no repository template is supplied, built-in PR/MR sections SHALL preserve their existing purpose and ordering while their headings follow the effective output language. An explicit language override SHALL affect headings and prose consistently.

#### Scenario: Russian built-in description
- **WHEN** a description is generated in Russian without a repository template
- **THEN** its built-in Summary, Why, and Testing sections have Russian headings in their existing order

#### Scenario: English override in Russian UI
- **WHEN** the interface is Russian but an effective Magic Prompt explicitly requests English
- **THEN** built-in headings and generated prose are requested in English

### Requirement: Repository-template fidelity

For the repository template supplied by the existing discovery and size-limit behavior, generation SHALL preserve headings, ordering, wording, and checklists while writing new filling text in the effective output language. Existing HTML-comment removal SHALL remain unchanged. The change SHALL NOT expand template discovery or size limits.

#### Scenario: English template with Russian generated text
- **WHEN** the supplied repository template has English headings and the effective output language is Russian
- **THEN** the template's headings, order, wording, and checklists remain unchanged
- **AND** newly authored filling text is requested in Russian

### Requirement: Existing prompt overrides remain authoritative

Explicit language instructions in effective user Magic Prompts SHALL override the interface-language default without rewriting saved prompts. Existing project-over-global precedence, per-prompt resolution, and empty-override semantics SHALL remain unchanged. Overrides without a new language placeholder SHALL still receive the interface-language default.

#### Scenario: Existing custom prompt without language placeholder
- **WHEN** an existing custom prompt has no language instruction or language placeholder
- **THEN** the request still includes the interface-language default
- **AND** the stored custom prompt remains unchanged

#### Scenario: Project override supersedes global override
- **WHEN** a project override explicitly requests a different language from the global override
- **THEN** the effective project instruction controls that prompt's language preference
- **AND** a shadowed global instruction is not reapplied

#### Scenario: Empty override
- **WHEN** an effective prompt override is an empty string
- **THEN** it remains an explicit empty override without restoring the built-in prompt
- **AND** the operation-level interface-language default is still supplied

### Requirement: Prompt-read failures remain failures

A failed or malformed Magic Prompts read SHALL retain the existing failure behavior. Adding language defaults SHALL NOT replace an unreadable override with a built-in prompt, silently generate in another language, or modify already entered PR/MR text after a failed generation.

#### Scenario: Unreadable project prompts
- **WHEN** the current project's Magic Prompts cannot be read
- **THEN** generation reports the existing failure instead of generating from built-in defaults
- **AND** the user's current title and description remain intact
