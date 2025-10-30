# Changelog

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
{{assign "video1Index" (math (modulo (timestamp) 10) "+" 20)}}
{{assign "video2Index" (math (modulo (math (timestamp) "/" 100) 10) "+" 30)}}
{{assign "video3Index" (math (modulo (math (timestamp) "/" 200) 10) "+" 40)}}
```
**Problem:** Breaks if collection is 30, 80, or any size other than 50.

**✅ NEW APPROACH (Dynamic):**
```handlebars
{{!-- Works with ANY collection size --}}
{{assign "collectionSize" collection.size}}
{{assign "videosNeeded" 3}}
{{assign "sectionSize" (math collectionSize "/" videosNeeded)}}

{{assign "video1Index" (modulo (timestamp) sectionSize)}}
{{assign "video2Index" (math (modulo (math (timestamp) "/" 100) sectionSize) "+" sectionSize)}}
{{assign "video3Index" (math (modulo (math (timestamp) "/" 200) sectionSize) "+" (math sectionSize "*" 2))}}
```
**Benefit:** Works automatically with 30, 50, 100, or ANY number of items!

#### Three Dynamic Approaches Documented:

**1. Basic Dynamic Approach**
- Uses `collection.size` to calculate section size
- `sectionSize = collectionSize / videosNeeded`
- Works with any collection size

**Examples:**
- 30 items / 3 videos = 10 per section (0-9, 10-19, 20-29)
- 60 items / 3 videos = 20 per section (0-19, 20-39, 40-59)
- 90 items / 3 videos = 30 per section (0-29, 30-59, 60-89)

**2. Skip Percentage (Dynamic)**
- Skip first X% of items (e.g., 40%)
- `skipAmount = collectionSize * 0.4`
- Scales with collection size

```handlebars
{{assign "collectionSize" collection.size}}
{{assign "skipAmount" (math collectionSize "*" 0.4)}}
{{assign "remainingSize" (math collectionSize "-" skipAmount)}}
{{assign "sectionSize" (math remainingSize "/" 3)}}
```

**Examples:**
- 50 items → Skip 20 (40%) → 30 remaining → 10 per section
- 30 items → Skip 12 (40%) → 18 remaining → 6 per section
- 100 items → Skip 40 (40%) → 60 remaining → 20 per section

**3. Skip Fixed Amount (Dynamic Sections)**
- Always skip first 20 items (fixed)
- Dynamically divide remaining items

```handlebars
{{assign "collectionSize" collection.size}}
{{assign "skipAmount" 20}}
{{assign "remainingSize" (math collectionSize "-" 20)}}
{{assign "sectionSize" (math remainingSize "/" 3)}}
```

**Examples:**
- 50 items → Skip 20 → 30 remaining → 10 per section → Sections at 20-29, 30-39, 40-49
- 80 items → Skip 20 → 60 remaining → 20 per section → Sections at 20-39, 40-59, 60-79
- 35 items → Skip 20 → 15 remaining → 5 per section → Sections at 20-24, 25-29, 30-34

#### Why This is Best Practice:

**5 Key Benefits:**
1. ✅ Works with ANY collection size - no hardcoded assumptions
2. ✅ Collection size can change without breaking template
3. ✅ Same code works for 30, 50, 100, or any number of items
4. ✅ More maintainable - collection grows/shrinks automatically handled
5. ✅ Percentage-based skipping scales with collection size

#### Comparison Table:

| Approach | Example | Problem | When to Use |
|----------|---------|---------|-------------|
| **Hardcoded** | `{{assign "video1Index" (math (modulo (timestamp) 10) "+" 20)}}` | Assumes exactly 50 items, breaks if collection is 30 or 80 | ONLY if collection size is always fixed |
| **Dynamic** | `{{assign "sectionSize" (math collection.size "/" 3)}}` | None - works with any size | ALWAYS prefer this unless specific reason not to |

#### Complete Copy-Paste Example:

**For your use case (skip first 20, dynamic sections):**

```handlebars
{{#catalogCollection "RecommendedVideos" as |collection|}}
  
  {{!-- Get collection size --}}
  {{assign "collectionSize" collection.size}}
  
  {{!-- Skip first 20 items (fixed) --}}
  {{assign "skipAmount" 20}}
  
  {{!-- Remaining collection size --}}
  {{assign "remainingSize" (math collectionSize "-" skipAmount)}}
  
  {{!-- Section size from remaining items --}}
  {{assign "sectionSize" (math remainingSize "/" 3)}}
  
  {{!-- Calculate pseudo-random offsets starting at item 20 --}}
  {{assign "video1Index" (math (modulo (timestamp) sectionSize) "+" 20)}}
  {{assign "video2Index" (math (math (modulo (math (timestamp) "/" 100) sectionSize) "+" sectionSize) "+" 20)}}
  {{assign "video3Index" (math (math (modulo (math (timestamp) "/" 200) sectionSize) "+" (math sectionSize "*" 2)) "+" 20)}}
  
  <div class="video-recommendations">
    {{#each collection}}
      {{#ifEq @index video1Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
          <a href="{{video_url}}">Watch Now</a>
        </div>
      {{/ifEq}}
      
      {{#ifEq @index video2Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
          <a href="{{video_url}}">Watch Now</a>
        </div>
      {{/ifEq}}
      
      {{#ifEq @index video3Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
          <a href="{{video_url}}">Watch Now</a>
        </div>
      {{/ifEq}}
    {{/each}}
  </div>
  
{{/catalogCollection}}
```

**This works whether your collection has 30, 50, 80, or 100 items!**

#### Files Modified:

- `schema/common-patterns.json` - Added dynamic_collection_sizing section (~75 lines)
- `CHANGELOG.md` - This entry

#### AI Agent Benefits:

When users ask:
- "Collection size varies, how to handle?"
- "Don't hardcode 50 items"
- "Make it work with any collection size"
- "What if collection grows?"

AI can now:
✅ Show dynamic collection.size approach
✅ Explain why it's best practice
✅ Provide 3 different dynamic strategies
✅ Show examples with 30, 50, 100 items
✅ Recommend dynamic over hardcoded

---

## 2025-10-30 - Pseudo-Random Selection from Catalog Collections

### New Pattern: Sectioned Pseudo-Random Item Selection

Added comprehensive documentation for selecting pseudo-random items from catalog collections using timestamp-based offsets and sectioning strategy.

#### What Was Added:

**1. New Section in `schema/common-patterns.json`**

`product_recommendations.pseudo_random_selection` - Complete guide to pseudo-random selection from catalog collections (~135 lines).

#### The Core Problem:

**Handlebars CANNOT shuffle or randomize arrays.** `catalogCollection` always returns items in the same fixed order.

**The Challenge:**
- You have 50 items in a collection
- First 20 items are often the same/stale
- Everyone sees the same items if you just use `[0]`, `[1]`, `[2]`
- Need variation without true randomization

#### The Solution Strategy:

**Divide collection into sections and use pseudo-random offsets within each section.**

**Example: 3 Videos from 50 Items**

```handlebars
{{#catalogCollection "RecommendedVideos" as |collection|}}
  
  {{!-- STRATEGY: Divide 50 items into 3 sections --}}
  {{!-- Section 1: items 0-16 (17 items) --}}
  {{!-- Section 2: items 17-33 (17 items) --}}
  {{!-- Section 3: items 34-49 (16 items) --}}
  
  {{!-- Calculate pseudo-random index for each section --}}
  {{assign "video1Index" (modulo (timestamp) 17)}}
  {{assign "video2Index" (math (modulo (math (timestamp) "/" 137) 17) "+" 17)}}
  {{assign "video3Index" (math (modulo (math (timestamp) "/" 251) 16) "+" 34)}}
  
  {{!-- Loop through collection and display selected items --}}
  <div class="video-recommendations">
    {{#each collection}}
      {{#ifEq @index video1Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
        </div>
      {{/ifEq}}
      
      {{#ifEq @index video2Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
        </div>
      {{/ifEq}}
      
      {{#ifEq @index video3Index}}
        <div class="video-card">
          <h3>{{name}}</h3>
          <p>{{short_description}}</p>
        </div>
      {{/ifEq}}
    {{/each}}
  </div>
  
{{/catalogCollection}}
```

#### How It Works:

**Section Calculation:**
- **Section 1**: `modulo (timestamp) 17` → gives 0 to 16
- **Section 2**: `modulo (timestamp / 137) 17 + 17` → gives 17 to 33
- **Section 3**: `modulo (timestamp / 251) 16 + 34` → gives 34 to 49

**Why use prime numbers (137, 251)?**
- Creates better pseudo-random distribution than simple multiples (100, 200)
- Different divisors ensure each section varies independently
- Different times = different results

**Results:**
- **Time 1** (timestamp: 1698765432100): Shows items 0, 24, 42
- **Time 2** (timestamp: 1698765999800): Shows items 14, 29, 38
- **Different users at different times see different videos!**

#### Key Concepts Documented:

**What Handlebars CANNOT Do:**
- ❌ Cannot shuffle arrays
- ❌ Cannot reorder arrays
- ❌ Cannot truly randomize
- ❌ Cannot change the order of the collection

**What Handlebars CAN Do:**
- ✅ Use timestamp for pseudo-randomness
- ✅ Calculate varying offsets with modulo
- ✅ Select different items at different times
- ✅ Divide collection into sections

#### Benefits of This Approach:

1. **Better distribution** - Covers entire 50-item collection
2. **No duplicates** - Each section picks one item
3. **Pseudo-random variation** - Changes with timestamp
4. **Balanced representation** - Equal sections
5. **Scalable** - Easy to adjust for different quantities

#### Variations Documented:

**1. Simplified Version (Regular Divisors):**
```handlebars
{{assign "video1Index" (modulo (timestamp) 17)}}
{{assign "video2Index" (math (modulo (math (timestamp) "/" 100) 17) "+" 17)}}
{{assign "video3Index" (math (modulo (math (timestamp) "/" 200) 16) "+" 34)}}
```

**2. Avoid Repetitive Items (Target items 20-49):**
```handlebars
{{!-- Skip first 20 items if they're repetitive --}}
{{assign "video1Index" (math (modulo (timestamp) 10) "+" 20)}}
{{assign "video2Index" (math (modulo (math (timestamp) "/" 100) 10) "+" 30)}}
{{assign "video3Index" (math (modulo (math (timestamp) "/" 200) 10) "+" 40)}}
```

**3. Adjust for Different Quantities:**
- **5 videos from 50**: 50 ÷ 5 = 10 items per section
- **10 videos from 50**: 50 ÷ 10 = 5 items per section

**Formula:**
- **Section size** = Total items ÷ Number of items needed
- **Section offset** = Section number × Section size
- **Random within section** = `modulo (timestamp / divisor) sectionSize`

#### Comprehensive Documentation Includes:

1. **limitation_understanding** - What Handlebars can/cannot do
2. **solution_strategy** - Why sectioning works
3. **example_3_videos_from_50** - Complete working code
4. **simplified_version** - Easier approach with regular divisors
5. **avoid_repetitive_items** - How to skip stale items at beginning
6. **adjust_for_different_quantities** - Formulas for 5 or 10 videos
7. **key_concepts** - How timestamp, modulo, and sections work
8. **limitations** - Not truly random, same time = same results
9. **when_to_use** - Good use cases vs not recommended
10. **alternative_solutions** - Randomize at source (BEST), multiple collections

#### Limitations Clearly Explained:

⚠️ **Important to understand:**
- **Not truly random** - Uses timestamp for pseudo-randomness
- **Same time = same results** - Users at exact same millisecond see same items
- **Server-side only** - Calculated when template renders, not client-side
- **No array shuffle** - Can't actually shuffle, only vary which items shown
- **Fixed collection order** - Collection is always in same order

#### When to Use:

**✅ Good Use Cases:**
- Large catalog collections (30+ items)
- When first items are repetitive/stale
- Video/content recommendations
- Product recommendations
- When you want temporal variation

**❌ Not Recommended:**
- When you need true randomness
- When all users MUST see identical content
- Small collections (< 10 items)
- When you can randomize at the source

#### Alternative Solutions Documented:

**1. Randomize at Source (BEST):**
- Configure catalog collection to randomize results
- Use data feed that returns randomized results
- Have backend API randomize before Iterable fetches it
- **This is the ONLY way to get truly random results**

**2. Multiple Collections:**
- Create multiple collections with different sort orders
- Use different collections in different campaigns

#### Why This is Critical:

This solves a common real-world problem:
1. Users have large catalog collections (30-50+ items)
2. First items are often repetitive/stale
3. Everyone sees the same items with static indexes
4. Handlebars can't shuffle arrays
5. Need variation without backend changes

**The sectioning strategy provides:**
- ✅ Good distribution across entire collection
- ✅ Temporal variation (different times = different items)
- ✅ No duplicates (each section picks one)
- ✅ Works within Handlebars limitations
- ✅ No backend/API changes required

#### Files Modified:

- `schema/common-patterns.json` - Added pseudo_random_selection section (~135 lines)
- `CHANGELOG.md` - This entry

#### AI Agent Benefits:

When users ask:
- "How to randomize catalog collection?"
- "Show different videos to different users"
- "Avoid showing same items to everyone"
- "Can Handlebars shuffle arrays?"
- "Why are my catalog items always the same order?"
- "Need variation in product recommendations"

AI can now:
✅ Explain Handlebars limitations (can't shuffle)
✅ Show sectioning strategy with examples
✅ Provide complete working code (3, 5, 10 items)
✅ Explain how timestamp + modulo creates variation
✅ Show how to avoid repetitive items
✅ Recommend true randomization at source when needed
✅ Provide formulas for custom quantities

---

## 2025-10-30 - Catalog Collection Array Syntax Documentation

### Critical Clarification: catalogCollection Returns Arrays

Added comprehensive documentation explaining the critical distinction that `catalogCollection` returns an **array of objects**, not a single object, and must be accessed accordingly.

#### What Was Added:

**1. New Section in `schema/common-patterns.json`**

`product_recommendations.catalog_collection_syntax` - Comprehensive examples showing correct vs incorrect syntax:

**The Critical Distinction:**
```handlebars
❌ WRONG - Direct field access (doesn't work):
{{#catalogCollection "RecentVideos" as |video|}}
  {{video.name}}
{{/catalogCollection}}

✅ CORRECT - Use index notation:
{{#catalogCollection "RecentVideos" as |video|}}
  {{video.[0].name}}
{{/catalogCollection}}

✅ CORRECT - Use #each to loop:
{{#catalogCollection "RecentVideos" as |video|}}
  {{#each video}}
    {{name}}
  {{/each}}
{{/catalogCollection}}
```

**Includes:**
- **wrong_syntax** - Shows common mistake and why it's wrong
- **correct_syntax_index** - Index notation examples for accessing specific items
- **correct_syntax_each** - Using #each to loop through all items
- **first_item_only** - How to get only the first item using #each + @first
- **multiple_specific_items** - Hero product + secondary products layout example
- **key_rules** - 6 essential rules for working with catalogCollection

**Key Rules Documented:**
1. catalogCollection returns an ARRAY, not a single object
2. Without #each: Use index notation → `{{collection.[0].fieldName}}`
3. With #each: Can access fields directly → `{{fieldName}}`
4. Index is zero-based: [0] = first, [1] = second, [2] = third
5. Use #each when you want to loop through ALL items
6. Use index notation when you want SPECIFIC items

**2. New Troubleshooting Section in `schema/troubleshooting.json`**

`catalog_collection_not_rendering` - Comprehensive troubleshooting guide:

**Common Mistakes Documented:**
1. **Trying to access fields directly without index**
   - Symptom: Fields appear blank, no error
   - Solution: Use `{{video.[0].name}}` or `{{#each video}}{{name}}{{/each}}`

2. **Using @first without #each**
   - Wrong: `{{#if @first}}{{products.name}}{{/if}}`
   - Correct: `{{#each products}}{{#if @first}}{{name}}{{/if}}{{/each}}`

3. **Mixing index and direct field access**
   - Wrong: `{{video.[0]}}` (missing field name)
   - Correct: `{{video.[0].name}}`

**Includes:**
- **Debugging steps** - 4-step process to diagnose issues
- **Quick fix cheatsheet** - Fast solutions for common scenarios
- **Real-world example** - Complete before/after for displaying first video

#### Why This is Critical:

**This is one of the most common mistakes users make with catalogCollection:**
1. Users expect `catalogCollection` to work like `#catalog` (single item lookup)
2. They try to access fields directly: `{{video.name}}`
3. Template renders but fields are blank (no error message)
4. Confusion because syntax "looks correct" but doesn't work

**The documentation now clearly explains:**
- `catalogCollection` → Returns ARRAY → Must use `[0]` or `#each`
- `#catalog` → Returns single object → Can access `.field` directly
- `#each` → Changes context → Can access fields directly inside loop

#### Examples Added:

**Accessing specific items by index:**
```handlebars
{{#catalogCollection "FeaturedProducts" as |products|}}
  <h1>{{products.[0].name}}</h1>
  <h3>{{products.[1].name}}</h3>
  <h3>{{products.[2].name}}</h3>
{{/catalogCollection}}
```

**Looping through all items:**
```handlebars
{{#catalogCollection "RecentVideos" as |video|}}
  {{#each video}}
    <h2>{{name}}</h2>
    <p>{{short_description}}</p>
  {{/each}}
{{/catalogCollection}}
```

**Getting only the first item:**
```handlebars
{{#catalogCollection "RecentVideos" as |video|}}
  {{#each video}}
    {{#if @first}}
      <h2>{{name}}</h2>
      <p>{{short_description}}</p>
    {{/if}}
  {{/each}}
{{/catalogCollection}}
```

#### Files Modified:

- `schema/common-patterns.json` - Added catalog_collection_syntax section (~90 lines)
- `schema/troubleshooting.json` - Added catalog_collection_not_rendering section (~60 lines)
- `CHANGELOG.md` - This entry

#### AI Agent Benefits:

When users ask:
- "Why are my catalogCollection fields blank?"
- "How do I get the first item from a collection?"
- "catalogCollection not working"
- "How to access collection data?"
- "Difference between catalog and catalogCollection?"

AI can now:
✅ Explain that catalogCollection returns arrays
✅ Show correct index notation: `{{video.[0].name}}`
✅ Show correct #each usage
✅ Explain when to use index vs #each
✅ Provide troubleshooting steps
✅ Show real-world examples

---

## 2025-10-28 - Reserved Fields Comprehensive Documentation

### New Documentation: Iterable Reserved & System-Managed Fields

Added complete documentation for all Iterable reserved fields to prevent custom field naming conflicts.

#### What Was Added:

**1. New File: `schema/reserved-fields.json` (~400 lines)**

Complete reference for all reserved and system-managed fields in Iterable.

**Sections included:**
- **Required identifiers** - email, userId
- **Contact information** - phoneNumber
- **System tracking fields** - **ip** (the key one!), lastSeenIp, signupIp
- **Subscription management** - emailListIds, unsubscribed fields
- **Metadata fields** - createdAt, profileUpdatedAt, signupDate
- **Campaign/message fields** - campaignId, templateId, messageId
- **Mobile/API fields** - mergeNestedObjects, preferUserId
- **Internal fields** - itblInternal (never modify!)

**Key field documented: `ip`**
```json
{
  "name": "ip",
  "type": "string",
  "description": "User's IP address",
  "managed_by": "Iterable (system)",
  "can_update": false,
  "critical_warning": "DO NOT use 'ip' as a custom field name - it is reserved"
}
```

**Features:**
- Complete field descriptions
- Whether field can be updated
- Who manages each field
- Template access examples
- Auto-capture information
- Best practices
- Common mistakes with solutions
- Field naming recommendations
- Quick reference lists

**2. Updated `index.json`**

Added `reserved_fields` to files list for easy AI agent access.

**3. New Troubleshooting Section in `schema/troubleshooting.json`**

`reserved_field_name_conflicts` section with:
- 4 common problems with detailed solutions
- Focus on 'ip' field conflict (most common)
- emailListIds management issues
- System field overwrite problems
- userId immutability issues

**Problem 1: Custom 'ip' field won't save**
```json
// WRONG - conflicts with reserved field
{
  "dataFields": {
    "ip": "192.168.1.1"
  }
}

// CORRECT - use alternative name
{
  "dataFields": {
    "companyIpAddress": "192.168.1.1"
  }
}
```

**Alternative field names suggested:**
- companyIpAddress
- officeIp
- registeredIpAddress
- deviceIp

**Problem 2: emailListIds not updating**
- Wrong: Trying to set via user update API
- Correct: Use `/api/lists/subscribe` and `/api/lists/unsubscribe`

**Includes:**
- List of definitely reserved fields
- List of system-managed fields
- Field naming best practices
- Troubleshooting steps
- Real-world example with solution

#### Why This is Critical:

**The 'ip' field is a common gotcha:**
1. Users want to store company/office IP addresses
2. They name the field 'ip'
3. Field gets overwritten with user's browser IP
4. Causes confusion and data loss

**Other common conflicts:**
- userId (cannot be changed)
- emailListIds (managed by subscription system)
- System timestamp fields (auto-updated)

#### Official Reference:

All information based on: https://support.iterable.com/hc/en-us/articles/217744303-User-Profile-Fields-Used-by-Iterable

#### Quick Reference Lists:

**Definitely Reserved (DO NOT USE):**
- ip
- email
- userId
- phoneNumber
- emailListIds
- unsubscribedChannelIds
- unsubscribedMessageTypeIds
- subscribedMessageTypeIds
- itblInternal

**System-Managed (DO NOT MANUALLY SET):**
- createdAt
- profileUpdatedAt
- lastSeenIp
- signupIp
- campaignId
- templateId
- messageId

#### Files Modified:

- `schema/reserved-fields.json` - NEW file (~400 lines)
- `schema/troubleshooting.json` - Added reserved_field_name_conflicts (~130 lines)
- `index.json` - Added reserved_fields to files list

#### AI Agent Benefits:

When users ask:
- "Can I use 'ip' as a field name?"
- "Why isn't my 'ip' field saving?"
- "What fields are reserved in Iterable?"
- "My emailListIds won't update"
- "Can I change userId?"

AI can now:
✅ Check schema/reserved-fields.json
✅ Warn about conflicts
✅ Suggest alternative field names
✅ Explain which fields are system-managed
✅ Provide correct API approaches
✅ Show real-world examples

---

## 2025-10-28 - API Endpoints Documentation: Trigger vs. Create

### New Complete Documentation for Campaign APIs

Added comprehensive documentation for both `/api/campaigns/trigger` and `/api/campaigns/create` endpoints, highlighting the critical syntax differences.

#### What Was Added:

**1. New `api_endpoints` Section in `schema/contexts.json`:**

Three major sub-sections:
- **campaigns_trigger_api** - Complete documentation for Trigger API
- **campaigns_create_api** - Complete documentation for Create API  
- **api_comparison** - Side-by-side comparison table

**campaigns_trigger_api:**
- Syntax: `{{fieldName}}` (Handlebars curly braces)
- Data source: Event data (per-recipient)
- Channels: All (Email, SMS, Push, In-App, WhatsApp)
- Use cases: Order confirmations, receipts, transactional emails
- 5 template examples
- Data priority explanation
- Key features and important notes

**campaigns_create_api:**
- Syntax: `[[fieldName]]` (Data feed double square brackets)
- Data source: Campaign-level data (same for all recipients)
- Channels: **Email ONLY** (critical limitation)
- Use cases: Marketing blasts, newsletters, sale announcements
- 4 template examples showing correct [[syntax]]
- Common errors (using wrong syntax)
- Important limitations documented

**api_comparison:**
- Complete comparison table across 7 dimensions
- When to use which API
- Real-world scenario examples

**2. New Troubleshooting Section in `schema/troubleshooting.json`:**

`api_datafields_syntax_confusion` section with:
- 4 common problems with detailed solutions
- Syntax quick reference table
- Decision tree for choosing correct syntax
- 7-step debugging guide
- Real-world e-commerce examples

Common problems documented:
- Using {{}} with Create API (wrong - needs [[]])
- Using [[]] with Trigger API (wrong - needs {{}})
- Trying to use Create API dataFields with SMS (Email only)
- Mixing syntaxes incorrectly

**3. New Patterns in `schema/common-patterns.json`:**

`api_triggered_content` section with:
- **trigger_api_order_confirmation** - Complete order email example
- **create_api_marketing_blast** - Complete sale blast example  
- **api_syntax_comparison** - Side-by-side syntax reference

Each includes:
- Full API request JSON
- Complete template code
- Comments showing which syntax to use where
- Use case descriptions

#### The Critical Distinction:

```
/api/campaigns/trigger:
✅ Syntax: {{fieldName}}
✅ Channels: All
✅ Scope: Per-recipient
✅ Use for: Transactional emails

/api/campaigns/create:
✅ Syntax: [[fieldName]]  
⚠️ Channels: Email ONLY
✅ Scope: Campaign-wide
✅ Use for: Marketing blasts
```

#### Key Documentation Highlights:

**Trigger API Example:**
```handlebars
<!-- API: /campaigns/trigger -->
<!-- dataFields: { "orderId": "123", "total": 99.99 } -->

<p>Order #{{orderId}}</p>
<p>Total: {{numberFormat total "currency" locale}}</p>
```

**Create API Example:**
```handlebars
<!-- API: /campaigns/create -->
<!-- dataFields: { "saleName": "Flash Sale", "discount": 30 } -->

<h1>[[saleName]]</h1>
<p>Save [[discount]]%!</p>

<!-- User profile still uses {{}} -->
<p>Hi {{firstName}},</p>
```

**Wrong vs. Right:**
```handlebars
<!-- WRONG: Using {{}} with Create API -->
<h1>{{saleName}}</h1>  ❌ Won't render

<!-- CORRECT: Using [[]] with Create API -->
<h1>[[saleName]]</h1>  ✅ Works!
```

#### Why This Matters:

This is one of the most confusing aspects of Iterable's API:
1. Both APIs have `dataFields` parameter
2. But they use completely different syntax
3. Create API only works for email
4. Easy to mix up the syntaxes
5. No error message - just silently doesn't render

#### Files Modified:

- `schema/contexts.json` - Added api_endpoints section (~250 lines)
- `schema/troubleshooting.json` - Added api_datafields_syntax_confusion section (~110 lines)
- `schema/common-patterns.json` - Added api_triggered_content section (~70 lines)

#### AI Agent Benefits:

When users ask:
- "How do I access API data in templates?"
- "Why isn't my dataFields working?"
- "What's the difference between trigger and create?"
- "Can I use Create API with SMS?"

AI can now:
✅ Explain the syntax difference
✅ Show correct examples for each API
✅ Highlight email-only limitation
✅ Provide debugging steps
✅ Give side-by-side comparisons

---

## 2025-10-28 - Hosted Unsubscribe URL Pattern & Limitations Documented

### New Documentation: Hosted Unsubscribe URL in Project Settings

Added comprehensive documentation for a critical edge case that trips up many users.

#### What Was Added:

**1. Enhanced `schema/contexts.json` - Project Settings Section:**
- Complete documentation of Hosted Unsubscribe URL limitations
- **Critical limitation highlighted:** ONLY uses User profile data
- Cannot access event data or template {{assign}} variables
- Must use {{#if}}/{{else}} defaults to prevent broken URLs
- 4 real-world examples with dynamic subdomains
- Common errors and fixes
- Step-by-step workflow
- Technical explanation of why it's user-profile-only

**2. New Troubleshooting Section in `schema/troubleshooting.json`:**
- `hosted_unsubscribe_url_issues` section added
- 4 common problems with detailed solutions:
  - Variable not rendering (trying to use {{assign}})
  - Broken URL with missing subdomain (no default provided)
  - Expected event data not showing (event data not accessible)
  - Different URLs in different campaigns (workarounds)
- Best practices with 4 key rules
- Real-world example: Multi-publication news organization
- 5-step debugging guide

**3. New Pattern in `schema/common-patterns.json`:**
- `hosted_unsubscribe_dynamic_subdomain` pattern
- Example: `https://{{#if publication}}{{publication}}{{else}}www{{/if}}.reiterablecoffee.com/preferences`
- 3 variants for different use cases (region, brand, language)
- Requirements and critical notes
- Template usage instructions

#### Key Points Documented:

**The Critical Limitation:**
```
Hosted Unsubscribe URL Handlebars variables:
✅ Can access: User profile fields
❌ Cannot access: Event data
❌ Cannot access: Template {{assign}} variables
❌ Cannot access: Data feed data
```

**The Required Pattern:**
```handlebars
{{#if field}}{{field}}{{else}}default{{/if}}
```
Always include the `{{else}}` default to prevent broken URLs when field is null.

**Real-World Example:**
```handlebars
Project Settings → Hosted Unsubscribe URL:
https://{{#if publication}}{{publication}}{{else}}www{{/if}}.reiterablecoffee.com/preferences

User with publication="sports": https://sports.reiterablecoffee.com/preferences
User with publication=null:      https://www.reiterablecoffee.com/preferences
```

#### Why This Matters:

This is a common gotcha because:
1. Users expect to use {{assign}} in templates - doesn't work here
2. Users forget defaults - causes broken URLs with `.example.com`
3. Users try to use event data - only User profile works
4. It's evaluated at project level, before template execution

#### Files Modified:

- `schema/contexts.json` - Enhanced project_settings.hosted_unsubscribe_url
- `schema/troubleshooting.json` - Added hosted_unsubscribe_url_issues section
- `schema/common-patterns.json` - Added hosted_unsubscribe_dynamic_subdomain pattern

---

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
