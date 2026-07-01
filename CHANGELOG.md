# Changelog

## v1.1.0 — 2026-06-30 — groupBy, sortBy, catalogCollection, Cursor Skill File

### What's New

This release captures everything learned about Iterable's new array helpers during live client
engagements in May–June 2026. It also adds a `SKILL.md` so the repo self-describes as a Cursor
skill, enabling AI agents to navigate it without human guidance.

---

### New File: `SKILL.md` — Cursor Skill

The repo root now contains `SKILL.md`. Add this repo as a Cursor skill and AI agents will
automatically know:

- Which schema file to read for each type of question
- Key platform facts (engine, feature flags, data priority order)
- groupBy / sortBy / catalogCollection quick reference with copy-paste examples
- Catalog field access rules and setup order

**Why a SKILL.md instead of a Claude Project gem?**
A skill file lives in the repo, is version-controlled, is always in sync with the schema, and
works in any AI tool that supports Cursor skill conventions. A gem requires manual updates
separate from the codebase.

---

### New File: `schema/helpers-2026.json`

Full helper definitions for:

#### `groupBy` (GA May 28, 2026 — PR #30663)
- Requires `HandlebarsArrayHelpers` feature flag
- Max 100 input items (silently truncates beyond that)
- Key access uses **full dot path**: `field="a.b.c"` → `{{group.key.a.b.c}}`
- Group order = first-seen insertion order
- `@index`/`@first`/`@last` inside `{{#each group.items}}` reflect the outer loop — use named block params: `{{#each group.items as |item iIdx|}}`
- Output structure: `groups` array of `{ key, count, items }`

#### `sortBy` (GA June 5, 2026 — PR #30792)
- Same `HandlebarsArrayHelpers` flag as `groupBy`
- Field type (Date/Number/String) auto-detected from first non-null value
- Nulls always sort last regardless of `asc`/`desc`
- Multi-field tiebreakers: `field="priority,name"`
- Full nesting confirmed: `sortBy → groupBy → sortBy` composition works

#### `catalogCollection` (updated documentation)
- Returns an **array** — access by index `{{items.[0].name}}` or loop with `{{#each items}}`
- Max 50 items (Iterable platform limit)
- Fields accessed directly — no `dataFields` wrapper: `{{item.brand}}` not `{{item.dataFields.brand}}`
- Passes directly into `groupBy` or `sortBy`

---

### New File: `schema/patterns-2026.json`

Real-world patterns using the new helpers:

| Pattern | Description |
|---|---|
| `groupby_with_catalog_display` | catalogCollection → groupBy by category, full product display with image/name/desc/price/sale |
| `sortby_price_ascending` | catalogCollection → sortBy price asc |
| `sort_then_group` | Pre-sort array to control group insertion order |
| `group_then_sort_within` | groupBy by shipping method, sortBy price within each group |
| `sort_group_sort_composition` | Full sortBy → groupBy → sortBy nesting |
| `rei_order_confirmation` | Deep nested event data: sort by date → group by method+date → inner sort by price |
| `named_block_params_at_index_fix` | `@index`/`@last` workaround with named block params |
| `empty_state_handling` | `{{#if groups}}` pattern for empty input arrays |
| `multi_field_sort_tiebreaker` | sortBy with primary + tiebreaker fields |

---

### New File: `schema/troubleshooting-2026.json`

New troubleshooting entries:

| Issue | Root Cause |
|---|---|
| `groupby_returns_nothing` | HandlebarsArrayHelpers feature flag off (silent failure) |
| `groupby_wrong_key_path` | Using leaf name instead of full dot path in `{{group.key.*}}` |
| `sortby_returns_nothing` | Same HandlebarsArrayHelpers flag as groupBy |
| `at_index_wrong_in_nested_each` | `@index`/`@last` scope in nested each — use named block params |
| `groupby_missing_items_after_100` | groupBy 100-item limit (silently truncates) |
| `catalog_field_blank_after_groupby` | Accessing via `dataFields` wrapper instead of top level |
| `catalog_field_types_not_working` | Field type mappings set after items loaded (must be before) |
| `groupby_with_datafeed_vs_catalog` | groupBy works on Handlebars-context arrays, not raw data feed arrays |

---

### Updated: `index.json` → v1.1.0

- Added file pointers: `helpers_2026`, `patterns_2026`, `troubleshooting_2026`
- Added `SKILL.md` reference in `file_guidance`
- Added `catalogCollection` to `catalog` category
- Added `groupBy`, `sortBy` to `arrays` category
- Added `feature_flags` section documenting `HandlebarsArrayHelpers`
- Added `catalog_field_access` and `catalog_setup_order` quick reference entries
- Added `block_aliases` syntax rule for named block params

---

### Architectural Differences: Original PR #28863 vs Merged PR #30663 (groupBy)

| Dimension | Original (#28863) | Merged (#30663) |
|---|---|---|
| Implementation style | Object (singleton) | Class (dependency-injected) |
| Item limit | 500 | 100 |
| Feature flag | Not gated | Gated by HandlebarsArrayHelpers |
| Key path access | Leaf name only | Full dot path |
| Exception philosophy | Permissive / silent | Explicit typed exceptions |
| Multi-field grouping | Not in original | Supported |

---

## 2025-10-30 - Dynamic Collection Sizing for Pseudo-Random Selection

### Critical Improvement: Use collection.size for Dynamic Sizing

Added dynamic collection sizing pattern that uses `collection.size` instead of hardcoding assumptions about collection size. This is now the **BEST PRACTICE** approach.

#### What Was Added:

**New Section in `schema/common-patterns.json`:**

`product_recommendations.pseudo_random_selection.dynamic_collection_sizing` - Complete guide to dynamic sizing (~75 lines).

#### The Critical Improvement:

**❌ OLD APPROACH (Hardcoded):**
```handlebars
{{!-- Assumes exactly 50 items --}}
{{#assign "video1Index"}}{{math (math (timestamp) "%" 10) "+" 20}}{{/assign}}
{{#assign "video2Index"}}{{math (math (math (timestamp) "/" 100) "%" 10) "+" 30}}{{/assign}}
{{#assign "video3Index"}}{{math (math (math (timestamp) "/" 200) "%" 10) "+" 40}}{{/assign}}
```
**Problem:** Breaks if collection is 30, 80, or any size other than 50.

**✅ NEW APPROACH (Dynamic):**
```handlebars
{{!-- Works with ANY collection size --}}
{{#assign "collectionSize"}}{{collection.size}}{{/assign}}
{{#assign "videosNeeded"}}3{{/assign}}
{{#assign "sectionSize"}}{{math collectionSize "/" videosNeeded}}{{/assign}}

{{#assign "video1Index"}}{{math (timestamp) "%" sectionSize}}{{/assign}}
{{#assign "video2Index"}}{{math (math (math (timestamp) "/" 100) "%" sectionSize) "+" sectionSize}}{{/assign}}
{{#assign "video3Index"}}{{math (math (math (timestamp) "/" 200) "%" sectionSize) "+" (math sectionSize "*" 2))}}{{/assign}}
```
**Benefit:** Works automatically with 30, 50, 100, or ANY number of items!

---

## 2025-10-30 - Pseudo-Random Selection from Catalog Collections

Added comprehensive documentation for selecting pseudo-random items from catalog collections
using timestamp-based offsets and sectioning strategy. See full entry in original CHANGELOG below.

---

## 2025-10-30 - Catalog Collection Array Syntax Documentation

Added documentation explaining that `catalogCollection` returns an array and must be accessed via
index notation or `#each`. See full entry in original CHANGELOG below.

---

## 2025-10-28 - Reserved Fields, API Endpoints, Hosted Unsubscribe, Logic Snippets, Initial Structure

See git history for full entries on these earlier additions.
