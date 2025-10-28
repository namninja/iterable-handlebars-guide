# Changelog

## 2025-10-28 - README Updated with Comprehensive Usage Options

### Major README Expansion

**Added 6 different usage methods** with detailed comparisons and setup instructions.

#### New Usage Options:

1. **Filesystem MCP** (⭐⭐ Recommended for local development)
   - Fastest access method
   - Works offline
   - Real-time updates without git push
   - Complete setup guide for Cursor and Claude Desktop

2. **GitHub MCP** (⭐ For teams and version control)
   - Repositioned as team/version control solution
   - Maintained existing setup instructions

3. **Claude Projects / Custom GPTs** (⭐⭐⭐ For team sharing)
   - NEW: Complete guide for creating Claude Projects
   - Step-by-step instructions with file recommendations
   - Custom instructions template included
   - ChatGPT Custom GPT alternative documented

4. **Individual JSON Upload** (For one-off questions)
   - Enhanced decision table for file selection
   - File size information added

5. **Original Markdown File** (For manual reading)
   - Clear warnings about token limits for AI agents
   - Explicit "not recommended for AI uploads" notice

6. **Workspace Access** (Cursor/VS Code)
   - NEW: Documentation for built-in workspace access
   - Zero-setup option for Cursor users

#### New Comprehensive Comparison Tables:

**By Technical Approach:**
- Comparison across 6 methods
- Speed, token usage, offline capability, sharing ability

**By Use Case:**
- 9 different user scenarios
- Specific recommendations for each

**By Priority:**
- 7 priority categories (speed, sharing, cost, etc.)
- Best method for each priority

#### New Workflow Guides:

- 🏆 Solo Developers - Filesystem MCP recommended
- 👥 Small Teams (2-10) - Claude Projects
- 🏢 Large Teams/Enterprises - Hybrid approach
- 🤝 Clients/Customers - Claude Projects
- 🔧 Active Development - Local Filesystem MCP
- ❓ One-Off Questions - JSON Upload

#### Statistics:

- **Before:** 449 lines
- **After:** 731 lines
- **Added:** 282 lines (+63%)

#### Key Improvements:

- ✅ Filesystem MCP now primary recommendation for solo developers
- ✅ Claude Projects highlighted as best for non-technical teams
- ✅ GitHub MCP repositioned as team/version control tool
- ✅ Complete setup guides with platform-specific paths
- ✅ Honest assessment of trade-offs for each method
- ✅ Decision support for choosing the right approach

---

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
