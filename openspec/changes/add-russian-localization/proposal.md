# Proposal

## Why

The user's personal OpenChamber build has no Russian interface, standard notifications, walkthrough language, or documentation translation. Russian-speaking users need those surfaces to agree on language without losing repository conventions or existing prompt customizations.

## What Changes

- Add a complete Russian locale to the existing shared UI, including startup text, locale formatting, language labels, and server-side settings persistence. Retain the current default locale and existing languages.
- Support Russian walkthrough generation, language normalization, and language-separated caching; keep code identifiers and structured-output fields unchanged.
- Add Russian server notification defaults matching the settings UI, preserving custom templates and substitutions.
- Translate the complete English documentation corpus, currently 59 pages, and sidebar labels into Russian in this repository. Website configuration, rendering, deployment, and the website repository are excluded.
- Default PR/MR generation to the interface language. Localize built-in section headings without a repository template; preserve a repository template's headings, ordering, wording, and checklists. Explicit language instructions in user Magic Prompts may override the interface-language default.

### Confirmed scope refinements

The original goal in `.openspec.yaml` is preserved verbatim. The user subsequently confirmed the following refinements in this session: this is a personal fork, not an upstream contribution; documentation delivery means source files and navigation translations only; all 59 current English pages are included; and the PR/MR language behavior above is agreed rather than deferred. These refinements replace the original goal's website-publication acceptance wording and its open PR/MR questions for this change.

The upstream `locale-ui-patterns` skill prohibits Russian localization and requires native menu translations with new UI languages. This personal-fork plan explicitly records the requested deviations: Russian is supported locally, and Electron native menus may retain the existing English fallback. It does not change upstream policy, edit agent instructions, or claim upstream acceptance.

## Capabilities

### New Capabilities

- `ui-localization`: Russian selection, complete shared-UI translations, formatting, persistence, and runtime coverage.
- `walkthrough-language`: Russian walkthrough prose with preserved technical anchors and language-aware caching.
- `notification-localization`: Russian server notification text consistent with UI defaults and custom templates.
- `documentation-localization`: Complete Russian documentation sources and navigation, with structural completeness checks.
- `change-request-language`: UI-language PR/MR generation with repository-template fidelity and explicit prompt override precedence.

### Modified Capabilities

None. The current OpenSpec capability inventory is empty; these specs establish the affected behavior rather than duplicate existing capability paths.

## Impact

- Shared UI i18n runtime, dictionaries, startup text, formatting, locale tests, and PR/MR prompt assembly. The current merged English dictionary has 8941 keys, including 2990 settings keys, composed with 51 shared translation modules.
- Web server locale allowlist, walkthrough language names and tests, and 17 notification text fields with matching UI placeholders.
- Documentation source tree, sidebar translations, contributor locale inventory, and focused completeness validation. No website work or publication is required for completion.
- Shared behavior is checked on web, Electron-rendered UI, hosted mobile, Capacitor mobile, and VS Code where the feature already exists. This change does not enable walkthroughs in VS Code or translate VS Code host chrome.
- No new dependencies, services, credentials, migrations, generation-language settings, or language picker for PR/MR. Existing Magic Prompts files and user data are not rewritten. More than 8 files will be involved; dictionary and documentation translation dominate the scope.
