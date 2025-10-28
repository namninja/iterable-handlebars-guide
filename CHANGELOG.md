# Changelog

## 2025-10-28 - Logic Snippets Pattern Added

### New Section: `logic_snippets` in `schema/common-patterns.json`

Added comprehensive documentation for using snippets as pure Handlebars logic containers.

#### What Was Added:

1. **Overview** - Concept of snippets for logic (not just UI)
2. **Grid Data Preparation** - Complete 2x2 product grid example
3. **Conditional Data Prep** - User tier/styling variables
4. **Date Calculations** - Pre-calculate dates for reuse
5. **Cart Calculations** - Shopping cart math
6. **Event Data Extraction** - Extract catalog data from event productId
7. **Best Practices** - Naming, placement, documentation, initialization, fallbacks
8. **Common Use Cases** - List of typical scenarios
9. **Why This Works** - Explanation of execution order and benefits

#### Key Concept:

**Problem:** Drag-and-drop email builders have static grid positions - you can't use `{{#each}}` loops to dynamically create grid cells.

**Solution:** 
- Create a "logic snippet" that extracts collection/catalog data
- Use `{{assign}}` to store data in numbered variables (product1Name, product2Name, etc.)
- Reference those variables in static grid cell positions

#### Example Pattern:

```handlebars
<!-- Step 1: Call logic snippet at top of template -->
{{{snippet "product-grid-data-prep"}}}

<!-- Step 2: Use assigned variables in static grid cells -->
<div class="grid-cell-1">
  <img src="{{product1Image}}" alt="{{product1Name}}">
  <h3>{{product1Name}}</h3>
  <p>{{numberFormat product1Price "currency" locale}}</p>
</div>

<div class="grid-cell-2">
  <img src="{{product2Image}}" alt="{{product2Name}}">
  <h3>{{product2Name}}</h3>
  <p>{{numberFormat product2Price "currency" locale}}</p>
</div>
```

#### Also Updated:

- `index.json` - Added `logic_snippets` and `grid_layouts` to quick_reference.common_tasks

#### Benefits:

- ✅ Works with drag-and-drop builders
- ✅ Separates logic from presentation
- ✅ Reusable across templates
- ✅ Maintainable (update logic in one place)
- ✅ Event data + catalog + grids = powerful combinations

---

## 2025-10-28 - Initial AI-Optimized Structure

Converted 4,674-line Markdown file into modular JSON structure for efficient AI agent consumption.
