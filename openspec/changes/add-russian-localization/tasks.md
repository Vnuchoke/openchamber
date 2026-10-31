# Tasks

These are implementation tasks, not evidence that implementation has started. All boxes remain unchecked during planning. Follow the personal-fork scope and policy exceptions in [proposal.md](proposal.md); preserve the recorded goal and user data. The five groups are work-order dependencies within one coherent delivery, not partial releases.

## 1. Russian shared UI and locale persistence

- [ ] 1.1 Add behavioral cases to UI locale/store tests and web `opencode/settings-helpers.test.js` for Russian normalization, server settings round-trip, reload, and state-preserving switching; run each owning test file and record the expected pre-change failures for missing Russian support.
- [ ] 1.2 Extend locale identity/normalization, `intl.ts`, `bootstrap.ts`, and server `UI_LOCALE_VALUES` for canonical `ru`, regional variants, Russian startup text and `ru-RU` formatting; verify normalization and server round-trip tests pass without changing default-language behavior. Dictionary-loading/state tests remain red until the complete dictionaries are connected in 1.8; do not release this intermediate state.
- [ ] 1.3 Create the fully translated `messages/ru.ts` using the current English dictionary composition; verify its non-settings literal keys, token substitutions, and legitimate unchanged technical names against English without placeholder values or English fallback spreads.
- [ ] 1.4 Create the fully translated `messages/ru.settings.ts`; verify complete settings-key and substitution parity, including notification placeholders, and manually review security, destructive-action, and error wording.
- [ ] 1.5 Translate Russian blocks in shared modules `autocomplete-scope` through `heap-snapshot` in filename order; verify every block has its English counterpart's exact key and placeholder sets and real Russian text.
- [ ] 1.6 Translate Russian blocks in shared modules `html-render` through `routing` in filename order; verify every block has its English counterpart's exact key and placeholder sets and real Russian text.
- [ ] 1.7 Translate Russian blocks in shared modules `secret-request` through `worktree-archive` in filename order; verify every block has its English counterpart's exact key and placeholder sets and real Russian text.
- [ ] 1.8 Connect the completed dictionaries through the `store.ts` lazy loader, add the Russian language-name key to every existing locale, include Russian in dictionary and feature-module test matrices, and add whole-dictionary placeholder checks; run affected test files individually and verify complete key/token parity plus the load/switch/state tests from 1.1.
- [ ] 1.9 Add an explicit `ru` English-fallback assertion in `packages/electron/menu-locales.test.mjs` without adding native menu translations; run `node --test packages/electron/menu-locales.test.mjs` and verify shared-UI Russian remains independent from native-menu fallback.

## 2. Walkthrough and notification language support

- [ ] 2.1 Add failing Russian cases to walkthrough `languages.test.js` and `language.test.js` for regional normalization, Russian prose instructions, literal structured values, and language-separated cached reads; record expected failures before extending the language map.
- [ ] 2.2 Add Russian to the existing walkthrough server language map, retain current job/cache/selection semantics, and update the owning walkthrough `DOCUMENTATION.md`; run the focused walkthrough tests and verify cached English/Russian switches and the reported language of an already-running job.
- [ ] 2.3 Add failing Russian notification cases for regional tags, all text fields, matching UI defaults, substitutions, and partially customized templates; run `template-locales.test.js` and record the expected missing-Russian failures.
- [ ] 2.4 Add the Russian server notification text and align literal defaults in `ru.settings.ts`; run notification tests and verify nonempty custom fields and original `{last_message}` content remain unchanged.

## 3. PR/MR output language

- [ ] 3.1 Add `packages/ui/src/lib/gitApi.pr-language.test.ts` cases exercising the shared generation operation with Russian and another UI locale, a mid-request locale switch, no repository template, an English template, a legacy override without placeholders, an explicit English override, project/global precedence, an empty override, a prompt-read failure, and both small-model/session-fallback transports; verify language-specific cases fail for the expected absent-default behavior before changing production code.
- [ ] 3.2 Capture UI locale once at shared PR/MR generation start and add the conditional language-default instruction independently of Magic Prompt template content; update built-in instructions for effective-language headings and preserve template discovery, the 8000-character input limit, JSON structure, and existing error paths; run the new operation tests and `magicPrompts.test.ts` to verify request contents and unchanged saved overrides.
- [ ] 3.3 Update the existing English Git and Magic Prompts documentation and corresponding passages in every existing documentation translation for the new default, template fidelity, and explicit override precedence; verify language-policy statements agree with the spec and `bun run docs:validate` retains its existing structural checks. Russian counterparts are delivered in group 4.
- [ ] 3.4 Confirm the shared UI continues calling the common generator directly without adding a PR/MR language picker, setting, or routing through legacy adapters; verify source references and operation tests cover both supported transports while the separate VS Code bridge contract is unchanged.

## 4. Complete Russian documentation sources

- [ ] 4.1 Translate `sdk/example.mdx` to `ru/sdk/example.mdx` as the representative nested page; verify Russian frontmatter/prose, unchanged technical examples, relative source placement, and all page, fragment, and asset references by source review before bulk translation.
- [ ] 4.2 Add fixture-based `scripts/docs/validate-docs.test.mjs` cases for complete Russian coverage, a missing or unmatched page, missing metadata, and missing Russian section/item labels; record expected failures before extending the dependency-free source validator, then verify focused fixture tests pass after implementation without requiring a website or package installation.
- [ ] 4.3 Translate `index`, `install`, `quickstart`, `opencode-server`, `connect-devices`, `mobile`, `projects`, and `chats` into corresponding Russian MDX paths; verify translated metadata/prose and record a source-reference review for each page.
- [ ] 4.4 Translate `chat-input`, `context`, `permissions`, `sessions-in-work`, `session-goals`, `scheduled-tasks`, `notes-todos-plans`, `sidebar`, and `notifications`; verify translated metadata/prose and record a source-reference review for each page.
- [ ] 4.5 Translate `git`, `github`, `git-identities`, `issues-and-prs`, `repository-config`, `walkthrough`, `magic-prompts`, and `worktrees`; verify the updated language behavior, literal commands/config keys, and a source-reference review for each page.
- [ ] 4.6 Translate `providers`, `classification-providers`, `auto-model-routing`, `mcp`, `skills`, `skills-catalog`, `integrations`, `extensions`, `agent-control-tool`, and `multi-run`; verify translated metadata/prose and record a source-reference review for each page.
- [ ] 4.7 Translate `desktop-browser`, `desktop-tunnels`, `preview`, `project-actions`, `project-icons`, `themes`, `environment`, `usage`, and `voice`; verify translated metadata/prose, unchanged runtime feature boundaries, and a source-reference review for each page.
- [ ] 4.8 Translate `remote-instances`, `private-relay`, `reverse-proxy`, `tunnels`, `ssh-hosts-proxying`, `security`, `updates`, `troubleshooting`, and the 3 nested troubleshooting pages; verify translated metadata/prose, literal technical/security guidance, and a source-reference review for each page.
- [ ] 4.9 Translate `sdk.mdx`, `sdk/host.mdx`, and `sdk/ui.mdx`, retaining the reviewed `sdk/example.mdx` from 4.1; verify all 4 SDK pages and their local references against current English sources.
- [ ] 4.10 Add nonempty Russian translations to every sidebar section/item and add the Russian locale plus the source-only personal-fork exception to `packages/docs/CONTRIBUTING.md`; verify canonical routes and entry counts are unchanged and no website, deployment, or agent-rule file was modified.
- [ ] 4.11 Run `node --test scripts/docs/validate-docs.test.mjs` and `bun run docs:validate` over the completed corpus; verify exact English/Russian path-set parity, currently 59 pages including 6 nested pages, Russian navigation coverage, and required frontmatter, and complete the recorded local-page/fragment/asset review without claiming MDX rendering or external-URL validation.

## 5. Cross-surface integration and acceptance

- [ ] 5.1 Run the focused tests listed in `design.md` and every new/modified test file using its owning runner, followed serially by root `type-check`, `lint`, and affected UI/web/VS Code/mobile build scripts; record actual exit codes and distinguish pre-existing failures from regressions without weakening checks.
- [ ] 5.2 Exercise Russian selection, reload, startup text, formatting, accessibility, errors, and an unsent draft in an isolated web instance; verify state is preserved, Russian text fits, existing languages remain usable, and failures do not masquerade as empty success.
- [ ] 5.3 Exercise desktop shared UI with English native menus, VS Code shared UI, hosted mobile, and Capacitor mobile through the repository's supported runtime routes; record the environment and observed behavior for each, or leave this task incomplete with the exact unavailable prerequisite rather than substituting browser-only proof.
- [ ] 5.4 With an already configured model, manually verify Russian walkthrough prose and cached language switching plus PR/MR generation without a template, with an English template, and with an explicit English prompt override; verify technical literals and template structure remain intact and distinguish model-output evidence from mocked request tests.
- [ ] 5.5 Obtain an independent review of the implementation diff and translation quality, resolve in-scope findings, and record available complexity/duplication/mutation checks or their unavailability; verify no new dependencies, rewritten user prompts, website changes, or unauthorized policy edits, and leave any material failing acceptance check explicitly unresolved.

## Workflow follow-up

- Implementation starts only after a separate apply request; planning does not authorize runtime launches, model usage, commits, pushes, publication, or upstream submissions.
- After implementation and required review, archive only when explicitly requested. Website registration and publication remain outside this change.
