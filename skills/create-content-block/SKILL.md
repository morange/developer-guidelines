---
name: create-content-block
description: Create a new TYPO3 Content Block in the current project's sitepackage, following the shared Content Block conventions. Use when the user asks to add, create or scaffold a Content Block or a new content element.
---

# Create TYPO3 Content Block

Create a new TYPO3 Content Block in the current project's sitepackage.

## Before you start

1. Read the developer guidelines FIRST (mandatory). Paths are relative to the root of the
   `developer-guidelines` repository, usually `../developer-guidelines/` next to the project:
   - `guidelines/typo3/content-blocks.md` — **primary reference for all CB conventions**
   - `guidelines/typo3/integrator.md`
   - `guidelines/xliff/typo3.md` — labels.xlf, SiteSet labels
   - `guidelines/xliff/keys.md` — key naming
   - `guidelines/scss.md`
2. Identify the sitepackage: look for a `ContentBlocks/` directory under `packages/*/`
3. Study existing Content Blocks in that directory for project-specific patterns

## Information to gather

Ask the user (only what's not already clear from context):

1. **Name:** PascalCase directory name (e.g., `TechBadges`, `PriceCard`)
2. **Purpose:** One sentence — what does the element display?
3. **Fields:** Which fields are needed? Reuse before creating, and declare each field by who creates its column — see *Reusing fields* in `guidelines/typo3/content-blocks.md`:
   - Core fields (header, subheader, bodytext, header_link, assets, image): `useExistingField: true`
   - Fields from another extension (e.g. `icon_identifier` from `ot-sitekit-ce-texticon`): `useExistingField: true` plus `type`
   - Fields another Content Block creates: same `identifier` and `type`, without `useExistingField`

   Only create a custom field when none of these fits, and name it for its meaning, not for the block.
4. **Collection fields?** Does this element need repeatable sub-items (IRRE)? If yes, define the sub-fields.
5. **Icon?** Does it use ot-icons? If yes, use the `i:icon` ViewHelper with no hardcoded `iconStyle` (use SiteSet default).
6. **Styling:** Does it need custom CSS? Always create `assets/frontend.css` with portable defaults using the fallback pattern. If the project needs overrides, also create a Build SCSS file.
7. **JavaScript:** Does it need JS? If yes, always create `assets/frontend.js` for portable logic. For substantial library imports, additionally create a Build entry point.
8. **SiteKit groups:** Which groups should it appear in? (group_content_small, group_content_wide, group_cards, group_hero, group_advanced)

## Icon Selector (ot-iconselector)

When `ot_iconselector` is installed and the block has an `icon_identifier` field (top-level or in a Collection), it should use the visual icon picker via a TCA override that sets `renderType: otIconSelector`. Read `references/icon-selector.md` in this skill's directory for the override files and their naming.

## Gotchas

Add to this list whenever a Content Block run goes wrong in a new way.

- **Schema needs two runs.** After creating columns: `updateschema "*.add"`, `cache:flush`,
  then `updateschema "*.add"` again, and the second run must report nothing left to add.
  A new Collection can fail on the first run (see *A new Collection on deploy* in
  `content-blocks.md`).
- **No `fe-build`.** The file watcher handles SCSS and JS changes; running a build on top
  is wasted work.
- **`useExistingField: true` does not work inside Collections.** Child fields live in a
  separate table, so they need a TCA override named after that table
  (`cb_{blockname}_{field}.php`).
- **Set only `renderType`, never `type`.** TYPO3 derives the database schema from `type`;
  changing it alters the column.
- **Reuse before creating a field, and name a new one for its meaning**, not for the block.
- **No hardcoded `iconStyle`** with the `i:icon` ViewHelper; the SiteSet default applies.

## After creation

1. Create new columns and flush the caches: `ddev exec typo3 database:updateschema "*.add"`, `ddev exec typo3 cache:flush`, then `database:updateschema "*.add"` once more — it must report nothing left to add (a new Collection can fail on the first run, see *A new Collection on deploy* in the guideline)
2. Check the backend — the new element should appear in the configured SiteKit groups
3. Do NOT run `fe-build` — the file watcher handles SCSS/JS changes automatically
4. Test the element in the frontend — the portable CSS from `assets/frontend.css` should already provide basic styling
5. Test in both dark and light mode
6. If project overrides are needed, create `Build/Default/src/scss/ContentBlocks/_{Name}.scss` and import it in `Main.scss`