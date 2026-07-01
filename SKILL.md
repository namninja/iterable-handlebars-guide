---
name: iterable-handlebars-reference
description: Expert reference for Iterable Handlebars templating. Use when asked about any Handlebars helper, template syntax, catalog lookups, groupBy, sortBy, catalogCollection, personalization, conditional blocks, date formatting, math, snippets, data feeds, or debugging Iterable templates. Read the JSON schema files in this repo — do not answer from training data alone.
---

# Iterable Handlebars Reference — Cursor Skill

This file makes the repo a self-describing Cursor skill. When Cursor loads this skill, AI agents
know how to navigate the JSON schema files to answer Handlebars questions accurately.

## File Map — Read the Right File for the Question

| Question type | File to read |
|---|---|
| What helpers exist? / Overview | `index.json` |
| Specific helper — syntax, params, examples | `schema/helpers.json` |
| New helpers added in 2026 (groupBy, sortBy, catalogCollection) | `schema/helpers-2026.json` |
| Where can Handlebars be used? (email, SMS, push, journey…) | `schema/contexts.json` |
| Real-world pattern for a use case | `schema/common-patterns.json` |
| New 2026 patterns (groupBy + catalog, REI order confirmation…) | `schema/patterns-2026.json` |
| Template won't save / syntax error | `schema/validation-rules.json` |
| Template rendering wrong / debugging | `schema/troubleshooting.json` |
| New 2026 troubleshooting (groupBy silent failure, wrong key path…) | `schema/troubleshooting-2026.json` |
| Unsubscribe links, system merge tags | `schema/universal-parameters.json` |
| Reserved field names (ip, userId, emailListIds…) | `schema/reserved-fields.json` |

## How to Use This Skill

1. **Start with `index.json`** — find the right category and helper name.
2. **Open `schema/helpers.json` or `schema/helpers-2026.json`** — get full syntax, params, and examples.
3. **Check `schema/common-patterns.json` or `schema/patterns-2026.json`** — real-world usage patterns.
4. **Validate against `schema/validation-rules.json`** before suggesting template code.
5. **Check `schema/troubleshooting.json` or `schema/troubleshooting-2026.json`** when something renders wrong.

## Engine Facts (Always Apply)

- Engine: **`jknack/handlebars.java`** — Java port of Handlebars.js. JS-specific extensions do not work.
- `{{var}}` HTML-escapes output. `{{{var}}}` renders raw HTML — required for snippets.
- `{{#assign "name"}}value{{/assign}}` stores **scalar strings only** — never objects or arrays.
- `{{#each arr as |alias|}}` block alias syntax IS supported; use to avoid `../` in nested loops.
- Data priority order: **Event data → User profile → Data feed**.
- `{{{snippet "name"}}}` MUST use triple braces.
- FreeMarker syntax (`<#if>`, `<#list>`) is NOT Handlebars — renders as literal text.
- `[[field]]` square-bracket syntax only works with `/api/campaigns/create` (email only).

## Key Platform Facts

### Feature Flag: HandlebarsArrayHelpers
Required for `groupBy` and `sortBy`. When the flag is off, both helpers return `""` silently —
no error, no output. Must be enabled per-project by an Iterable admin.

### groupBy Helper (GA May 28, 2026 — PR #30663)
- Requires `HandlebarsArrayHelpers` feature flag.
- Max 100 input items — silently truncates beyond that.
- Key uses the **full dot path**: `field="a.b.c"` → access as `{{group.key.a.b.c}}`.
- Preserves insertion order of first-seen keys (not sorted).
- `@index`, `@first`, `@last` inside `{{#each group.items}}` reflect the **outer** groups loop,
  not the inner items — use named block params instead:
  `{{#each group.items as |item iIdx|}}`.
- Empty array → renders block with empty `groups`; wrap with `{{#if groups}}` for empty state.

### sortBy Helper (GA June 5, 2026 — PR #30792)
- Same `HandlebarsArrayHelpers` flag as `groupBy`.
- Field type (Date / Number / String) auto-detected from first non-null value — no `type` param.
- Nulls always sort last, regardless of `asc` / `desc`.
- Multi-field tiebreakers: `field="priority,name"` — first field is primary sort.
- Composition: `sortBy` → `groupBy` → `sortBy` within each group confirmed working.

### catalogCollection Helper
- Returns an **array** of objects, not a single object.
- Access specific items via index: `{{items.[0].name}}`.
- Loop all items: `{{#each items}}{{name}}{{/each}}`.
- Max 50 items per collection (Iterable platform limit).
- `dataFields`-backed fields are flattened to top level: use `{{item.brand}}`, not `{{item.dataFields.brand}}`.
- Pass directly into `groupBy` or `sortBy`: `{{#groupBy items field="category" as |groups|}}`.

### Catalog Field Mapping Rule
Set ALL field type mappings **before** loading items into the catalog. Types set after ingestion
do not apply retroactively. Required sequence:
1. Create catalog
2. Set all field mappings (including standard fields: `name`, `url`, `price`, `imageUrl`, `description`, `categories`)
3. Upload catalog items

### Collection Query Builder — Undocumented Handlebars Support
Handlebars expressions work as Custom Values in the Collection Query Builder UI.
`{{product.sku}}` resolves from event payload at query time. The public docs say otherwise —
the docs are wrong on this point.

## Quick Patterns

### groupBy with catalogCollection
```handlebars
{{#catalogCollection "AllProducts" as |items|}}
  {{#groupBy items field="category" as |groups|}}
    {{#each groups as |group|}}
      <h2>{{group.key.category}} ({{group.count}} items)</h2>
      {{#each group.items as |product|}}
        <img src="{{product.imageUrl}}" alt="{{product.name}}" width="200">
        <h3>{{product.name}}</h3>
        <p>{{product.description}}</p>
        <p>${{numberFormat product.price "0.00"}}</p>
      {{/each}}
    {{/each}}
  {{/groupBy}}
{{/catalogCollection}}
```

### sortBy then groupBy (control group order)
```handlebars
{{#sortBy items field="deliveryDate" order="asc" as |sorted|}}
  {{#groupBy sorted field="deliveryDate" as |groups|}}
    {{#each groups as |group|}}
      <h3>Arriving {{group.key.deliveryDate}} ({{group.count}} items)</h3>
      {{#each group.items as |item|}}
        {{item.name}}
      {{/each}}
    {{/each}}
  {{/groupBy}}
{{/sortBy}}
```
Pre-sorting forces groups into chronological order because `groupBy` preserves first-seen key order.

### groupBy within each group (nested sort)
```handlebars
{{#groupBy items field="shippingMethod" as |groups|}}
  {{#each groups as |group|}}
    <h3>{{group.key.shippingMethod}}</h3>
    {{#sortBy group.items field="price" order="asc" as |sorted|}}
      {{#each sorted as |p|}}
        {{p.name}} — ${{numberFormat p.price "0.00"}}
      {{/each}}
    {{/sortBy}}
  {{/each}}
{{/groupBy}}
```

### REI-style order confirmation (deep nested event data)
```handlebars
{{#sortBy xmldata.ShipTos.ShipTo field="LineItems.ItemFirstPromisedDate" order="asc" as |sorted|}}
{{#groupBy sorted field="LineItems.ItemShippingMethod,LineItems.ItemFirstPromisedDate" as |groups|}}
{{#each groups as |group|}}
  <h3>{{group.key.LineItems.ItemShippingMethod}} — Arrives {{group.key.LineItems.ItemFirstPromisedDate}}</h3>
  {{#each group.items as |item|}}
    {{item.LineItems.ItemName}} (Qty: {{item.LineItems.ItemQuantity}})
  {{/each}}
{{/each}}
{{/groupBy}}
{{/sortBy}}
```

### @index inside groupBy — use named block params
```handlebars
{{!-- WRONG — @index reflects outer groups loop --}}
{{#each group.items}}{{@index}}: {{name}}{{/each}}

{{!-- CORRECT — use block alias --}}
{{#each group.items as |item idx|}}{{idx}}: {{item.name}}{{/each}}
```
