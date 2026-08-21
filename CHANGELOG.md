# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

> **How to use this file:** every time a batch of changes is made to this project's
> code, add a new dated version section at the top of the released versions and
> bump the version number (pre-1.0: increment the minor for each feature batch,
> patch for small fixes). This file is the primary cross-chat record of what has
> been done — new sessions read it (via `AGENTS.md`) to understand project history
> without needing git or chat history.

## [0.2.1] - 2026-08-21

### Changed

- index.html: the context info editor (`#contextInfoEdit`) now spans 95% width, left-aligns its content, and its textarea auto-grows to fit its content (no internal scrolling), instead of a fixed 80%-wide, center-aligned, 3-row box.

## [0.2.0] - 2026-08-19

### Added

- Standalone local port of this project (new repo `D:\Development\Perchance\pfacc-local`): the full UI runs offline against a compatibility shim instead of perchance.org.
- index.html: vendored offline dependencies (dexie, dexie-export-import, marked, dompurify, json5) and the local adapters (`src/ai-provider.js`, `src/comfy-t2i.js`, `src/lists.js`, `src/icons.js`, `src/root-shim.js`, `src/image-gen-screen.js`) are loaded from script tags; the JSON5 CDN lazy-load block was removed (no longer nulls `window.JSON5`); footer text updated; `history.replaceState` now writes `location.pathname`; Settings modal gained AI Provider (Grok / DeepSeek / Ollama / LM Studio / custom) + optional embeddings API fields persisted via `pfaccAiProvider.saveConfig`.
- lists.perchance: no changes needed — the standalone port re-implements its runtime functions in `src/lists.js` and `src/root-shim.js` (summarization, gzip, share-link generation, sandbox template evaluation) and keeps as much logic in the ported files as possible.
- The preset/starter character URLs were left untouched: the app parses them with `split("#")[1]`, so the `https://perchance.org/...` prefix is irrelevant in the local build.

## [0.1.0] - 2026-08-19

### Added

- Character selection page redesign: centered card grid with `⋯` menu on each card (edit / change folder / use as persona / duplicate / share / delete).
- List/card view toggle for the character list (persisted in `db.misc` as `characterViewMode`; row layout with compact avatar, ellipsized name + folder badge + tagline).
- Instant character search (name / role / folder / tagline) with clear button.
- Custom sort dropdowns for the character list (name / recently used / recently created / id).
- Folder tree sidebar (`#characterFolderSidebar`): collapsible, own search, own sort (name / recent / created / id), persistent "Unfiled" pseudo-folder (`__UNFILED__`), folder emoji support.
- Folder sidebar now **collapsed by default** for new users; last state is remembered and restored via `db.misc` (`characterFolderSidebarCollapsed`).
- Character editor: dossier header (avatar + name + tagline), tabbed sections (Character Details, Chat setup, Appearance, Memory, Lore, Advanced) via hidden `__charEditorActiveTab` input + `show` functions; rich hover tooltips for info icons; prompt2 width 820px; all `hidden:true` spec flags removed; tagline field added.
- Universal toast system (`showToast` / `showTaskToast` / `finishTaskToast` / `updateToast` / `hideToast`) with `info` / `success` / `error` / `warning` / `loading` types.
- Click-to-stop task toasts wired into all three reply flows (`doBotReplyIfNeeded`, `regenerateMessage`, `doBotReplyInPlaceOfUser`).
- Spawn engine: `/spawn` command, spawn button, `searchDepth` dropdown (0/10/20/30/40/50), search keywords + description boxes, auto-generated `spawnReminderMessage` from the persona's Mind/Mannerisms/Drive + dialogue examples.
- Group chat creation modal (`showGroupChatModal`) + chat-vs-group choice popup.
- Force-load feature: redesigned popup with compact toggle, help tooltip, over-limit callout; quick force-load button on the options row; truncation recommendation toast (5-minute throttle via `window.__lastForceLoadRecommendationTime`).
- Context info: sticky section with live tooltip, chevron-only toggle, "last updated X ago" label (`window.__contextInfoLastUpdatedAt`, `updateContextInfoLastUpdatedEl`).
- Generated-image **delete button** on message images (ported from `acc.html`), hidden after keep.
- AI prompt improvements: authority preamble before `<character>` blocks, behavioral-priority notes in default `mainInstructions`.
- Placeholder text contrast fix (CSS + CodeMirror) and standardized type/size scale.
- Custom select components (`createCustomSelectButton`) for sort/icon dropdowns; sidebar expand button when collapsed.
- `CHANGELOG.md` (this file) + `AGENTS.md` conventions added for cross-chat history tracking.

### Changed

- Character selection search bar: pill style, icon, clear button; folder tree rows tightened (name flush to left edge, smaller padding/gap, larger subfolder chevron).
- View toggle button text/icon (list ↔ card); buttons layout rework (send / options+quick-toggler / spawn with user-plus icon).
- Thread cards redesigned with `⋯` menu (includes force-load quick button).
- Left column icons converted to FA icons (`fa-icon-plugin`); emoji removed from UI chrome.
- Folder filtering semantics: `""` (All characters) shows everything, `"__UNFILED__"` shows characters with no folder, otherwise exact folder path.
- Import plugin names standardized (`ai`, `t2i`, `icon`, `kv`, `comments`).

### Fixed

- **Image keep not persisting across thread switches**: `data-core-prompt` attribute was not HTML-escaped — prompts containing quotes truncated the attribute, so the saved-image lookup key never matched on re-render and the image regenerated. Now `sanitizeHtml(corePrompt)` + guards for missing message / `customData` / iframe data URL.
- Character list in list view overlapping the folder sidebar on smaller screens: grid rows defaulted to `min-width:auto`, so a nowrap tagline's min-content widened the `1fr` track beyond the container (centered overflow both sides, painting over the sidebar). Added `min-width: 0` to list-view rows; media query now uses `!important` so inline sidebar styles (`position:sticky`, `max-height:85vh`) no longer defeat the ≤900px column layout.
- Mojibake corruption of non-ASCII characters (emojis/accents) caused by CP1252 re-encoding: recovered via CP1252-byte → UTF-8 re-decode; file verified strict-UTF-8, no BOM, CRLF.
- Dexie `contextInfo` crash: `customData.contextInfo.basic` must exist even when `contextInfoToggle` is `"no"`.
- `main-badge` style leftovers from prompt2 spec leaks; `show` functions no longer leak into persisted result objects (Dexie cannot clone functions).
- Toast/stop flows restored after merge; `warning` toast type added for truncation recommendation.

### Removed

- Image generator shortcut from the character selection header.
- Native `<select>` for icon/sort dropdowns (replaced by custom select components that can render FA icons).
- Crown emoji usage (use star for main character); emoji replaced by FA icons in character selection and left column.

### Deprecated

- (none)

### Security

- AI/user-generated text is HTML-escaped (`sanitizeHtml`) when embedded into HTML attributes (`data-*`, `title`, `alt`).