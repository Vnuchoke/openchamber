# Design

## Context

See [proposal.md](proposal.md) for motivation, confirmed scope refinements, and the personal-fork policy exceptions. The original `.openspec.yaml` goal remains unchanged; the proposal and these artifacts record the later user decisions about source-only documentation and PR/MR generation.

This is a cross-module change, so this design is required. The current source is OpenChamber 2.5.3. Its merged English dictionary has 8941 keys, including 2990 settings keys, and 51 shared `*.i18n.ts` modules. There are 17 server notification text fields and 59 English documentation pages. These are planning snapshots; acceptance compares current source sets rather than hardcoding those counts.

Relevant existing boundaries:

- `packages/ui/src/lib/i18n/runtime.ts` owns locale identity and normalization; `store.ts`, `intl.ts`, and `bootstrap.ts` own dictionary loading, formatting, and startup text.
- `packages/web/server/lib/opencode/settings-helpers.js` filters persisted `locale` through `UI_LOCALE_VALUES`. UI registration alone would leave server persistence incomplete.
- `packages/web/server/lib/walkthrough/languages.js` must agree with UI locales; `languages.test.js` already enforces that contract. Cache keys already include language.
- `packages/web/server/lib/notifications/template-locales.test.js` compares server defaults to literal UI settings defaults. A Russian settings dictionary must preserve that correspondence.
- `packages/ui/src/components/views/git/PullRequestSection.tsx` calls shared `gitApi.ts` directly. Its small-model and session-fallback transports reuse the same prepared prompts. The separate VS Code bridge PR-description contract is not the path this shared action calls.
- `packages/docs` owns sources and sidebar data. `openchamber-website` owns rendering and publication. The current source validator checks frontmatter and sidebar routes, not translation completeness or every local reference.

## Goals / Non-Goals

**Goals:** implement the five capability contracts through existing ownership boundaries; retain persisted settings and prompt formats; make translation completeness observable; and keep language selection stable throughout one generation operation.

**Non-Goals:** upstream submission, changing maintainer policy or agent instructions, a translation framework replacement, ICU/pluralization infrastructure, new dependencies, a PR/MR language picker or setting, native Electron or VS Code host-chrome translation, enabling unavailable runtime features, website rendering or publication, voice changes, and rewriting existing user prompts.

## Decisions

### 1. Extend the existing locale model as one coherent UI change

Use canonical `ru`, normalize regional tags through the existing boundary, retain left-to-right layout, and map formatting to `ru-RU`. Extend `Locale`, `LOCALES`, `LOCALE_LABEL_KEYS`, the lazy dictionary loader, startup messages, and `UI_LOCALE_VALUES` together. Keep current default and host-language selection semantics unchanged.

Add `messages/ru.ts`, `messages/ru.settings.ts`, and a Russian block to every shared translation module. Add the Russian language-name key to all existing dictionaries using each dictionary's conventions. Reuse current composition and `Record<I18nKey, string>` checking; do not hide untranslated values behind English spreads or casts.

Extend current dictionary tests to include Russian, compare the complete key set, and compare substitution-token sets for each English/Russian pair. Review equal strings explicitly: product names and technical terms can legitimately remain identical, whereas explanatory English placeholders cannot. Use neutral count phrasing where current singular/plural keys cannot express every Russian form; do not introduce a new grammar engine.

Alternative rejected: partial Russian dictionaries with English fallback. They make a locale appear available before it is usable and violate the existing translation discipline. The UI locale and its required server consumers ship together, not as independently released partial phases.

### 2. Keep walkthrough and notification ownership intact

Add `ru: 'Russian'` to the walkthrough server language map. Existing normalization then accepts regional forms. Preserve current explicit-choice, displayed-result-language, and UI-language precedence. Do not redesign cache storage or job ownership: a source still has one active generation, and switching language while it runs does not create an independent parallel job. Tests must distinguish the requested language from the language actually displayed.

Add Russian notification text to the existing server locale map and use identical default wording in `ru.settings.ts`. Keep variable names, nonempty custom fields, per-field inheritance, and `{last_message}` behavior. No notification schema or saved-template conversion is needed.

Alternative rejected: sharing imports between web server JavaScript and UI dictionaries. Existing package boundaries deliberately keep these definitions separate and use parity tests.

### 3. Apply the PR/MR language default at operation assembly

Read the selected UI locale once at the start of shared `generatePullRequestDescription`, alongside its existing operation state. Build an operation-level language instruction independently of saved Magic Prompt templates: the default output language is that locale, and an explicit different language in the effective user instructions wins. Include this rule in the prepared request even when a saved prompt is empty or lacks new placeholders.

Keep `loadMagicPrompts(directory)` as one snapshot per operation. Its effective override resolution remains project, then global, then built-in, separately for each prompt key; a shadowed global prompt is not merged back. Do not parse arbitrary natural-language instructions, detect their language with regular expressions, or rewrite stored overrides.

Update the built-in instructions to ask for section headings in the effective output language, not always the UI language. This permits an English override in Russian UI without mixed-language headings. The language rule must describe a conditional default, not contradict explicit customization with an unconditional system instruction. Keep JSON keys literal.

The data flow remains acyclic:

```text
UI locale snapshot ----+
                       v
Effective Magic Prompts --> Shared PR/MR prompt assembly --> Small model
Repository template ------>              |
                                         +--> Existing session fallback
```

Preserve current template discovery, provider-specific ordering, first-nonempty selection, HTML-comment removal, and the 8000-character template-input limit. Fidelity applies to the template actually supplied to the model; this change does not claim full preservation of larger templates. New filling text uses the effective output language while template headings, order, wording, and checklists stay literal.

The shared UI action receives the new behavior wherever it is already available. Do not rewrite the separate `api:git/pr-description` legacy VS Code bridge, HTTP adapter, or public runtime API contract merely because similarly named methods exist; the inspected shared UI generator does not dispatch to those methods. Keep their existing behavior and test contracts outside this change.

Alternative rejected: a new language placeholder as the only mechanism. Existing custom templates would silently omit it. A separate picker and project language setting were also rejected by the user in favor of the UI default with existing explicit overrides.

### 4. Deliver a complete source translation without website work

Mirror every English documentation page under `packages/docs/content/docs/ru/`, preserving relative paths. The baseline is 53 root pages, 3 `sdk/` pages, and 3 nested `troubleshooting/` pages. Include translated frontmatter and all prose; preserve working technical examples and canonical routes. Add `ru` translations to each section and item in `packages/docs/sidebar.config.json` without adding duplicate entries or locale-prefixed links.

Update the documentation locale inventory in `packages/docs/CONTRIBUTING.md` and document the personal-fork source-only exception there. Do not modify `.agents`, `.claude`, root agent instructions, website configuration, deployment workflows, or secrets. Explain in Russian Git and Magic Prompts documentation how the accepted PR/MR language policy works.

Extend the existing source validator with focused English/Russian relative-path-set parity and nonempty Russian sidebar-label checks. Classify English sources by excluding documentation locale directories, using their existing lowercase directory spelling; do not assume the UI and documentation locale sets are identical. Keep the validator dependency-free because the docs packaging workflow invokes it without installing application dependencies.

Use an import-safe validator entry point and focused fixture tests in `scripts/docs/validate-docs.test.mjs`, keeping the existing `bun run docs:validate` command. A valid fixture must pass; removing a page or label, adding an unmatched path, or deleting required metadata must fail with the affected source location. Check Russian local page, fragment, and asset references in a recorded source review of all translated pages, including anchors changed by translated headings. This review complements the structural validator; do not claim it compiles MDX or validates external URLs.

Translate one representative nested page first and verify its metadata, links, examples, and navigation. Establish the fixture-based structural checks before extending the same pattern to the whole corpus. No custom translation service or bulk source-rewriting generator is needed.

Alternative rejected: website publication. It needs another repository and hosting authorization, and the user explicitly excluded it. A six-page initial subset was also rejected in favor of the complete corpus.

### 5. Preserve runtime boundaries and local-fork exceptions

Use the existing English fallback in `packages/electron/menu-locales.mjs`; add a focused assertion that `ru` falls back without changing menu dictionaries. This is the user's explicit personal-fork exception to the usual new-locale menu rule. VS Code localization covers the shared webview, not its host menus, commands, or extension metadata.

Walkthrough remains unavailable in VS Code. Hosted mobile uses the existing mobile surface; Capacitor uses its existing server and shell. Adding a locale must not introduce remounts, new language stores, automatic generation, or new runtime capabilities.

## Risks / Trade-offs

- **Upstream policy differs.** Keep this work local to the user's fork and record the exceptions; do not represent the plan as upstream-approved.
- **Large translation footprint and source drift.** Freeze each translation batch against its English source, compare current sets, and review terminology and screenshots. Counts alone are not acceptance evidence.
- **Prompt construction does not prove model compliance.** Deterministic tests assert language instructions, override precedence, and both transports; real-model manual checks verify prose, headings, template fidelity, and technical literals.
- **Long templates are already truncated.** Retain the existing 8000-character limit and test the supplied-template boundary; widening that limit is a separate task.
- **Native or extension tooling may be unavailable.** Record the exact missing prerequisite and leave affected implementation verification unchecked; a browser screenshot is not proof of a native shell or extension-host scenario.

## Migration Plan

No data migration, new credentials, external writes, or dependency installation is required. Implementation is one coherent locale delivery with test-first work groups, not a sequence of partially supported releases. Keep all implementation checkboxes unchecked during planning.

Before implementation, read current module documentation and recheck the English source sets for concurrent changes. Add failing behavioral checks before changing each relevant code path. Preserve all existing user files and saved settings while integrating translations.

For rollback, switch the UI to an existing locale before running an older build. Current normalization already handles unknown locales through its existing fallback. Withdraw code and translation changes without deleting user settings, prompts, caches, or message history; no destructive rollback command is part of this plan.

## Verification and Handoff

Run focused files individually using their owning package runners. Existing targets include UI `i18n/runtime.test.ts`, `i18n/store.test.ts`, `i18n/messages.test.ts`, `magicPrompts.test.ts`, and walkthrough store tests; web `opencode/settings-helpers.test.js`, walkthrough `languages.test.js` and `language.test.js`, and notification `template-locales.test.js`; plus Electron `menu-locales.test.mjs`. Add shared PR/MR generation tests that exercise the operation, not a duplicated prompt string.

Commands from repository scripts and their underlying runners:

```text
bun test packages/ui/src/lib/i18n/runtime.test.ts
bun test packages/ui/src/lib/i18n/store.test.ts
bun test packages/ui/src/lib/i18n/messages.test.ts
bun test packages/ui/src/lib/magicPrompts.test.ts
bun run --cwd packages/web test server/lib/opencode/settings-helpers.test.js
bun run --cwd packages/web test server/lib/walkthrough/languages.test.js server/lib/walkthrough/language.test.js
bun run --cwd packages/web test server/lib/notifications/template-locales.test.js
node --test packages/electron/menu-locales.test.mjs
node --test scripts/docs/validate-docs.test.mjs
bun run docs:validate
```

After integration, the coordinator runs `bun run type-check`, `bun run lint`, and affected builds serially, not in parallel with translators. Use `bun run build:ui`, `bun run build:web`, `bun run vscode:build`, and `bun run mobile:build` for the affected consumers. Run `bun run check:changed` only if a commit is separately requested, immediately before it. Use available CRAP, duplication, and mutation tools without installing them; report unavailable checks.

Manual acceptance must cover locale switch and reload with an active draft; startup/formatting/settings/dialog accessibility; Russian notification defaults and a partially custom template; Russian walkthrough generation and cached language switching; PR/MR with no template, an English repository template, and an explicit English Magic Prompt override; desktop shared UI and English native-menu fallback; VS Code shared UI; hosted mobile; and Capacitor mobile. For runtime proof, use the repository's isolated development routes from `openchamber-change-discipline`; do not attach to or stop unrelated user instances.

For each runtime and real-model scenario, record environment, action, expected result, observed result, and any blocker. Do not mark implementation ready while a material required check remains blocked. MDX rendering, website build, deployment, and changes to the separate website repository are explicitly not acceptance gates for source-only delivery.
