# Spec Delta

## Purpose

Provide a complete Russian shared interface in the personal OpenChamber build while preserving existing locale choices, user state, and runtime feature boundaries.

## ADDED Requirements

### Requirement: Russian locale selection and normalization

The application SHALL offer Russian as `Русский`, use `ru` as its canonical locale, and normalize regional or separator variants to `ru`. Adding Russian SHALL preserve the existing default locale and other language choices.

#### Scenario: Select Russian
- **WHEN** the user selects `Русский` in the interface language control
- **THEN** the interface uses the Russian dictionary and left-to-right layout
- **AND** Russian remains selectable when another supported language is active

#### Scenario: Regional Russian locale
- **WHEN** a supported locale input receives `ru`, `ru-RU`, or `ru_RU`
- **THEN** it resolves to `ru` without falling back to English

#### Scenario: No Russian preference
- **WHEN** no Russian preference is supplied or stored
- **THEN** existing default-language and host-language selection behavior remains unchanged

### Requirement: Complete Russian interface text

The Russian dictionary SHALL cover the complete current English key set with real translations and matching substitution tokens. Startup messages and locale-sensitive formatting SHALL support Russian. Product names, technical identifiers, paths, commands, and user-authored content SHALL retain their intended literal values.

#### Scenario: Complete dictionary
- **WHEN** the Russian interface renders settings, navigation, dialogs, tooltips, accessibility labels, empty states, errors, or shared feature text
- **THEN** all corresponding English dictionary keys have nonempty Russian values
- **AND** substitution tokens are preserved without English placeholder translations

#### Scenario: Formatting and startup
- **WHEN** the application starts with Russian selected and displays locale-sensitive numbers or dates
- **THEN** app-owned startup messages are Russian and formatting uses the Russian locale
- **AND** code, paths, product names, and user-authored content are not automatically translated

### Requirement: Persisted locale and state-preserving switching

The application SHALL save and restore the canonical Russian locale through its existing settings path. Switching between Russian and another language SHALL preserve the current session, draft, and navigation state without remounting the application. Existing settings failure behavior SHALL remain intact.

#### Scenario: Save and reload
- **WHEN** the user saves Russian as the interface language and reloads or restarts the application
- **THEN** server settings retain `ru` and the interface restores Russian

#### Scenario: Switch with an active draft
- **WHEN** a user with an open session and an unsent draft switches between English and Russian
- **THEN** interface text changes while the draft, current session, and navigation state are preserved

### Requirement: Existing runtime boundaries

Russian SHALL be available on existing shared-UI surfaces in web, desktop, VS Code, hosted mobile, and Capacitor mobile. The change SHALL preserve each runtime's existing feature availability. Native Electron menus SHALL remain usable through their existing English fallback when Russian labels are absent.

#### Scenario: Shared interface in each runtime
- **WHEN** a supported runtime displays an existing shared-UI feature with Russian selected
- **THEN** that feature displays its Russian interface text
- **AND** unsupported features do not become available solely because Russian was added

#### Scenario: Electron native menus
- **WHEN** the desktop shared interface uses Russian and no Russian native menu dictionary exists
- **THEN** native menus and context menus remain usable in English
- **AND** the shared interface remains Russian
