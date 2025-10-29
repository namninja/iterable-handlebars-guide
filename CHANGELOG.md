# Changelog

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
