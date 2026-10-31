# Spec Delta

## Purpose

Provide Russian app-owned server notification text that agrees with the interface while preserving user templates, message content, and existing fallback behavior.

## ADDED Requirements

### Requirement: Russian standard notification text

Server-generated notification headings and app-owned messages SHALL support Russian for the complete existing set of notification text fields. Canonical and regional Russian locale tags SHALL resolve to Russian; unknown locales SHALL retain the existing English fallback.

#### Scenario: Russian default notification
- **WHEN** a notification uses standard text with `ru`, `ru-RU`, or `ru_RU` as its locale
- **THEN** its app-owned heading and standard message use Russian

#### Scenario: Unknown locale
- **WHEN** a notification locale is unsupported
- **THEN** the existing English fallback is used

### Requirement: UI and server default consistency

The Russian notification defaults shown as settings placeholders SHALL match the defaults rendered by the server. Variable names and substitutions SHALL remain compatible with the existing template format.

#### Scenario: Default placeholders match delivery
- **WHEN** Russian notification fields are empty and settings display their default placeholders
- **THEN** the server uses the same default text when sending those notifications
- **AND** agent and model variables remain substitutable by their existing names

### Requirement: Custom and generated content is preserved

Nonempty user template fields SHALL remain unchanged. Empty fields SHALL inherit the corresponding Russian default independently. Error and question messages that use the last-message substitution SHALL continue displaying the original message without automatic translation.

#### Scenario: Partially customized notification
- **WHEN** the user supplies a custom title and leaves the message empty with Russian selected
- **THEN** the custom title is preserved and the message inherits the Russian default

#### Scenario: Last message content
- **WHEN** an error or question notification displays the last assistant message
- **THEN** the app-owned title is localized and the original message is substituted unchanged
