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

## [0.6.6] - 2026-09-11

### Changed

- index.html: revamped the Scene Details editor in `window.openScenesManagerModal` with a sleek underline tabbed layout (Variant 2), placing Scene Title and Tags in a persistent header row above the tabs.
- index.html: separated the 3 main scene textareas into dedicated tabs (Premise & Purpose, Starting Setting & Atmosphere, and Opening Narration) while keeping all panels present in the DOM to ensure smooth tab switching and zero data loss.
- index.html: updated Tab 2 (Starting Setting & Atmosphere) and `getBotReply` AI instructions to explicitly designate the setting as the initial starting location and opening ambience, allowing characters and narrative events to naturally evolve and transition to new locations without rigid entrapment while retaining background context.

## [0.6.5] - 2026-09-11

### Added

- index.html: added a Scenes & Scenarios system allowing users to create, customize, and manage story premises, settings, and opening narrations that gently guide chat direction and character responses without forcing rigid outcomes.
- index.html: created `window.openScenesManagerModal` modal with search/filter, list of scenes with title/tag/preview cards, visual editor (Title, Tags, Premise & Purpose, Setting & Atmosphere, Opening Narration), Duplicate, Delete, JSON Export, JSON Import, and Reset to Starter Defaults.
- index.html: built-in 3 rich starter scenes (*Cabin in the Woods*, *Rainy Cafe Reunion*, *Midnight Heist Briefing*) stored non-destructively in `db.misc` under `"customScenes"` with zero schema migrations.
- index.html: added optional Scene / Scenario selector to the 1-on-1 chat creation popup card (`characterEl` click) and the Group Chat configuration modal (`showGroupChatModal`), defaulting to "None (Freestyle)" for zero friction.
- index.html: added automatic thread naming (`${scene.title} — ${characterName}`) and initial Narrator opening message insertion when a thread is initialized with a scene.
- index.html: added `window.openThreadSceneModal` allowing users to view, change, edit, or detach the active scene for any existing chat thread at any time.
- index.html: added sidebar button `[icon("film")] scenes` in `#appOptions`, `[icon("film")] scene / scenario` in the thread ⋯ dropdown menu, and `[icon("film")] scene / scenario` in `#threadOptionsPopup`.
- index.html: added `/scenes`, `/scene`, and `/scenario` slash commands to open the Scenes Manager or view the current thread's scene.
- index.html: injected `ACTIVE SCENE & STORY PURPOSE (GUIDE, DO NOT FORCE)` into `getBotReply` AI instructions, providing narrative context, setting atmosphere, and organic direction while explicitly instructing the model not to force or rush the premise.

## [0.6.4] - 2026-09-11

### Fixed

- index.html: fixed other characters speaking dialogue or having separate action paragraphs during someone else's reply (e.g. Jerry emerging and speaking while Ashley is the replying character) by adding comprehensive character name resolution across threads, messages, DB, and shortcuts (`getAllCharacterNamesInThread`).
- index.html: added `isParagraphAttributedToOtherCharacter` and `filterAndPruneOtherCharacterContent` to detect and filter out leading paragraphs where another character acts or speaks before the replying character, and truncate trailing paragraphs when another character's turn begins.
- index.html: added real-time stream stopping in `onChunk` as soon as a new paragraph begins describing actions or dialogue for another character.
- index.html: reinforced `getBotReply` instructions and `createAiChatCompletion` system prompt with strict mandatory opening rules (must begin directly with the replying character's actions/words) and zero tolerance for quotes spoken by or action blocks dedicated to other characters.

## [0.6.3] - 2026-09-11

### Changed

- index.html: redesigned the Shortcut Buttons Manager visual editor (`openBulkEditShortcutsModal`) into a compact, minimal, and collapsible layout to dramatically reduce vertical footprint and visual clutter.
- index.html: added collapsibility to all shortcut cards with one-click header chevron/row toggling, compact single-line collapsed bars showing `#index`, live preview button pill, command snippet, and active badges (`⚡ auto`, `🧹 clear`, insertion type).
- index.html: added "Collapse all" and "Expand all" toolbar controls to quickly toggle all cards simultaneously.
- index.html: streamlined the expanded card fields with tightened padding, smaller inputs, side-by-side Button Label and Insertion Behavior, and compact checkboxes.
- index.html: implemented ephemeral editor ID tracking (`_editorId`) ensuring collapse state and card order persist accurately during reordering (Move Up/Down), duplication, and deletion.

## [0.6.2] - 2026-09-11

### Fixed

- index.html: fixed AI characters ventriloquizing, godmoding, and roleplaying multiple characters in a single message by adding strict anti-godmoding instructions and character perspective boundaries to `getBotReply` (`CRITICAL CHARACTER PERSPECTIVE & ANTI-GODMODING RULES`) and `createAiChatCompletion` system prompt.
- index.html: updated prompt task instruction in `getBotReply` from generic multi-message writing (`write the next 3 messages in this chat`) to strictly scoped single-turn generation (`write ONLY the single next message in this chat as [[${replyingCharacterName}]]`), eliminating multi-character dialogue simulation.
- index.html: added other thread character names to stop sequences (`\n\n${otherName}:`), stream-chunk early termination, and multi-paragraph post-processing truncation to automatically cut off any accidental continuation into other characters' turns.

## [0.6.1] - 2026-09-11

### Fixed

- index.html: fixed incorrect character attribution and context confusion in multi-character chats where an AI reply written as "Sarah" was attributed to and displayed under "Jerry" by enhancing `characterList` resolution in `napAutoReply` to index all active thread participants, allowed loaded character IDs, and shortcut buttons instead of only buttons with ID hashes.
- index.html: added robust `extractCharacterFromAiResponse` in `napDynamicResponder` to reliably parse character selections from alternative and Perchance AI models (handling prefixes like `Character: `, markdown bold/italics, quotes, and punctuation) so responder selections map directly to the intended character rather than erroneously falling back to the thread character.
- index.html: updated dynamic responder context updating (`updateContextInfo`) to update the responding character's custom data rather than always updating the primary thread character.
- index.html: reinforced `getBotReply` instructions with character-specific perspective directives (`isAnotherCharacterReplying`) when an external character replies, ensuring the LLM speaks strictly as that character.
- index.html: eliminated leading colons (`: *She held...*`) across streaming and non-streaming responses by fixing premature delimiter flushing on `]]` in `createAiChatCompletion`, stripping bare leading colons in `stripLeadingSpeakerPrefix`, `computeFinalText`, `onStreamingReplyChunk`, `handleStreamingReplyChunk`, and final database assignment.

## [0.6.0] - 2026-09-11

### Added

- index.html: added interactive Shortcuts Manager modal (`window.openBulkEditShortcutsModal`) under `#shortcutButtonsCtn` featuring tabbed navigation between an intuitive Visual Card Editor and an Advanced Raw Text editor with real-time bidirectional synchronization.
- index.html: added individual shortcut cards with live-rendered button preview pills (resolving `{{char}}` and `{{user}}` in real-time), one-click Move Up (`▲`), Move Down (`▼`), Duplicate (`📋`), and Delete (`🗑️`) controls.
- index.html: added quick shortcut preset toolbar buttons (`🗣️ /ai`, `🗣️ /user`, `🗣️ /nar`, `🖼️ /image`) and a blank shortcut generator with placeholder auto-focus.
- index.html: added slash commands `/shortcuts` and `/shortcut` to open the Shortcuts Manager directly from the chat input box.
- index.html: added persistent `+ shortcuts` trigger button when a thread has zero shortcuts so users are never locked out of adding shortcuts back after bulk deletions.

### Changed

- index.html: updated the bulk-edit shortcut button (`#shortcutButtonsCtn button:first-child`) to use FontAwesome pencil icon and connected the `📝 bulk edit / manage shortcuts` action directly to the new visual Shortcuts Manager modal.
- index.html: improved shortcut text format serialization (`shortcutsToTextFormat` and `shortcutsFromTextFormat`) to safely escape and unescape newlines (`\n`) and provide automatic fallback names for untitled shortcuts.

## [0.5.0] - 2026-09-11

### Added

- index.html: added thread-level Story Goal & pacing feature (`thread.storyGoal`, `thread.storyGoalPacing`) to guide narrative progression in long chat sessions towards an overarching objective with slow-burn guardrails preventing premature resolution.
- index.html: injected pacing-governed story goal prompt into `getBotReply` instructions prior to `>>> TASK` with explicit progression guidelines (slow-burn organic stepping stones, ultra-slow strict non-resolution subtext, or moderate steady advancement).
- index.html: added Thread Options popup button (`⚙️ thread options` -> `$.storyGoalButton`) with modal editor (`window.openStoryGoalModal`) allowing users to define the story goal and select pacing ('slow', 'ultra-slow', 'moderate').
- index.html: added `/goal` slash command suite (`/goal` to open modal editor, `/goal clear` to remove goal, `/goal <text>` to set story goal directly) with helper documentation added to chat input tooltip.
- index.html: added inline Story Goal banner above chat input (`#storyGoalBannerCtn`) displaying current goal preview, pacing pill, quick edit button (`[✎]`), and clear button (`[✖]`).
- index.html: added `showInlineStoryGoal` toggle in Global Settings under the Appearance tab allowing users to show or hide the inline story goal banner.

## [0.4.0] - 2026-09-02

### Added

- index.html: alternative AI provider support for text generation with default "perchance" (built-in, zero-configuration website AI) and full OpenAI-compatible API support (`window.pfaccAiProvider`), including presets for OpenRouter, OpenAI, Grok (xAI), DeepSeek, Ollama (local), LM Studio (local), and Custom endpoints.
- index.html: searchable model input with native `<datalist>` auto-completion, dynamic search filter dropdown (`window.__updateModelSearchDropdown`, `window.__selectAiModel`), and `Fetch Models` button (`window.__fetchAiModels`) that queries the provider's `GET /models` endpoint with CORS-safe proxy fallback via `root.superFetch`.
- index.html: global persistence for AI provider configuration (provider, baseUrl, apiKey, model, temperature, maxTokens, maxContextTokens) stored in `db.misc` and `localStorage`, ensuring settings apply globally across all stories and characters rather than per-thread.
- index.html: dynamic provider indicator icon on the send button (`#sendButtonProviderIcon` via `updateSendButtonProviderIcon`) displaying a vibrant green leaf for free Perchance (`AI Provider: Perchance (Free)`) or a distinct icon (amber bolt for cloud/remote APIs, blue server for local Ollama/LM Studio models) with model details tooltip for alternative providers.

### Changed

- index.html: redesigned Global Settings modal to match the Character Editor layout with an 820px-wide card, user dossier header (avatar thumbnail, nickname, description), and tabbed navigation (`.settingsTabs button` with FontAwesome icons via `window.__setSettingsTab`): **Profile**, **AI Provider**, **Chat & Prompts**, **Appearance**, **Memory & Lore**, and **Advanced**.
- index.html: wrapped `root.aiTextPlugin` to seamlessly route all text generation calls across the application (chat streaming replies, context info generation, thread titles, summarization, character creation, and URL extraction) to the active AI provider while strictly keeping image generation on Perchance (`textToImagePlugin`).

### Fixed

- index.html: safeguarded `prompt2`'s initial focus query so hidden inputs (`input[type=hidden]`) are excluded from receiving focus and `selectionStart` errors are trapped, preventing `InvalidStateError: The input element's type ('hidden') does not support selection`.
- index.html: guarded `updateInputVisibilies` against undefined specs so any element with a custom `data-spec-key` safely checks `specs[key]?.show` instead of throwing `Cannot read properties of undefined (reading 'show')`.
- index.html: mapped `pfaccModel` directly in `specs` and replaced the character-like avatar circle in the Global Settings header with a dedicated settings cog banner to eliminate visual confusion with the Character Editor.
- index.html: fixed element selectors in `window.__fetchAiModels`, `window.__updateModelSearchDropdown`, and `window.__selectAiModel` to query `input[data-spec-key]` directly instead of the parent `<section>`, preventing `TypeError: Cannot read properties of undefined (reading 'trim')` when reading base URL and API key values.
- index.html: fixed prompt construction in `createAiChatCompletion` so `startWith` (recent messages and character reply prefix) is combined into the prompt rather than passed as an assistant turn, preventing OpenAI-compatible models from assuming the assistant already replied and generating the next character or user turn, and added initial speaker-prefix filtering (handling colon-less bracketed tags e.g. `[[Chloe]]\n` and `[[Chloe]]:`) with real-time stop sequence truncation.
- index.html: enabled auto-reply for AI-assisted user messages by removing hardcoded `expectsReply: false` from `/user` command invocations, allowing `napAutoReply` and `doBotReplyIfNeeded` to naturally trigger character auto-replies after AI-assisted user turns.

## [0.3.0] - 2026-08-21

### Changed

- index.html: the character editor "Memory" tab is now split into sub-tabs — **General** (lorebook usage in other threads, context-limit fitting method, extended character memory), **Context Info** (enable switch, info, prompt), and **Detailed Context Info** (enable switch, info, prompt) — via a new `__charEditorMemorySubTab` hidden input and `__setCharEditorMemorySubTab` switcher.
- index.html: the "enable context info updates" and "enable detailed context info updates" dropdowns are now toggle switches shown at the top of their sub-tabs (`__toggleCharEditorEnabled`).
- index.html: the "Extended character memory" dropdown is now a toggle switch and moved to the top of the General sub-tab (still hidden when summaries are disabled).
- index.html: the Context Info and Detailed Context Info text/prompt textareas no longer cap at `8rem` and now auto-grow to fit their content, including re-fitting when a sub-tab is opened and while typing.

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