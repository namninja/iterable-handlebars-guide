# Iterable Handlebars Reference - AI Optimized

**Structured JSON reference for AI agents to efficiently assist with Iterable Handlebars questions.**

## Overview

This reference has been optimized for AI agent consumption with:
- ✅ **Modular JSON structure** - Load only what's needed
- ✅ **Quick lookups** - Direct access to specific helpers/contexts
- ✅ **Structured data** - Schema-enforced, easily queryable
- ✅ **Minimal token usage** - Efficient for AI processing
- ✅ **Comprehensive coverage** - All Handlebars features documented

## Structure

```
/IterableHandlebars/
├── index.json                              # Master catalog & quick reference
├── schema/
│   ├── helpers.json                        # All ~70+ Handlebars helpers
│   ├── contexts.json                       # Where Handlebars works
│   ├── validation-rules.json               # Syntax rules & error prevention
│   ├── common-patterns.json                # Frequent use cases
│   ├── troubleshooting.json                # Common issues & solutions
│   └── universal-parameters.json           # Always-available parameters
└── README.md                               # This file
```

## Quick Start for AI Agents

### 1. Start with index.json
```json
{
  "categories": {
    "conditional_logic": ["if", "unless", "ifEq", "ifGt", ...],
    "date_time": ["dateFormat", "dateMath", "now", ...],
    "math": ["math", "numberFormat", ...],
    ...
  }
}
```

### 2. Look up specific helpers
**Example: Find helper details**
- Open `schema/helpers.json`
- Navigate to category → helper name
- Get: syntax, parameters, examples, use cases

**Example JSON structure:**
```json
{
  "conditional_logic": {
    "ifEq": {
      "name": "ifEq",
      "syntax": "{{#ifEq value1 value2}}...{{/ifEq}}",
      "parameters": [...],
      "examples": [...],
      "use_cases": [...],
      "tags": ["comparison", "equality", "conditional"]
    }
  }
}
```

### 3. Check where Handlebars works
**File:** `schema/contexts.json`

Find out:
- Email templates (subject, body, preheader)
- SMS, Push, In-App, WhatsApp
- Segmentation, Dynamic Lists, Journey Splits
- Webhooks, Data Feeds, Catalog Collections

### 4. Validate syntax
**File:** `schema/validation-rules.json`

Check:
- Proper closing tags
- Named parameter syntax
- Escaping rules ({{}} vs {{{}}})
- Common errors and fixes

### 5. Find patterns
**File:** `schema/common-patterns.json`

Categories:
- Personalization
- Conditional content
- Dates & times
- Pricing & currency
- Product recommendations
- Abandoned cart
- URLs & links

### 6. Troubleshoot issues
**File:** `schema/troubleshooting.json`

Includes:
- Common issues & causes
- Step-by-step solutions
- Debugging tips
- Security concerns

## Example AI Queries

### "How do I format a date?"
1. Look in `index.json` → categories → date_time → ["dateFormat"]
2. Open `schema/helpers.json` → date_time → dateFormat
3. Get syntax, parameters, examples

### "Show me a personalized greeting"
1. Open `schema/common-patterns.json`
2. Navigate to personalization → greeting_with_fallback
3. Get code + variants

### "Why won't my template save?"
1. Open `schema/troubleshooting.json`
2. Look up "cannot_save_template"
3. Get cause, symptoms, solution

### "What Handlebars helpers are available?"
1. Open `index.json`
2. Check categories object
3. Or open `schema/helpers.json` for full list

## Benefits Over Single Markdown File

| Old (MD) | New (JSON) |
|----------|------------|
| 4,674 lines | Modular files |
| 27,860+ tokens | Load only needed sections |
| Sequential reading | Direct lookups |
| Unstructured text | Structured, queryable data |
| Exceeded token limits | Efficient token usage |

## File Descriptions

### index.json
Master catalog with:
- File locations
- Helper categories
- Quick reference for common tasks
- Syntax rules summary

### schema/helpers.json
Complete helper reference:
- 70+ helpers across 10 categories
- Syntax, parameters, return types
- Multiple examples per helper
- Use cases and common errors
- Tags for searchability

### schema/contexts.json
Where Handlebars works:
- Template content (email, SMS, push, etc.)
- Segmentation & targeting
- Data feeds & webhooks
- Project settings
- String conversion contexts

### schema/validation-rules.json
Syntax validation:
- All syntax rules
- Valid/invalid examples
- Common errors & fixes
- Best practices
- Validation checklist

### schema/common-patterns.json
Real-world patterns:
- Personalization
- Conditional content
- Date/time operations
- Pricing & currency
- Product recommendations
- Cart abandonment
- Localization

### schema/troubleshooting.json
Problem solving:
- Common issues
- Causes & solutions
- Debugging tips
- Security concerns
- Performance tips

### schema/universal-parameters.json
Always-available params:
- Unsubscribe links
- Campaign metadata
- Brand details
- Recipient info
- System parameters

## Usage Tips for AI Agents

1. **Start broad, then narrow**
   - Check `index.json` categories first
   - Then dive into specific schema files

2. **Use tags for search**
   - All helpers have tags
   - Example: "comparison", "date", "currency"

3. **Check multiple files**
   - Helper details: `helpers.json`
   - Usage location: `contexts.json`
   - Real examples: `common-patterns.json`

4. **Validate before suggesting**
   - Cross-reference with `validation-rules.json`
   - Ensure syntax is correct

5. **Provide troubleshooting**
   - If user reports issue, check `troubleshooting.json`
   - Offer specific solutions

## Updating This Reference

When adding new helpers or features:

1. Update `schema/helpers.json` with new helper definition
2. Add to appropriate category in `index.json`
3. Add usage examples to `schema/common-patterns.json`
4. Update `schema/contexts.json` if new context support added
5. Add troubleshooting entries if common issues emerge

## Version

- **Version:** 1.0.0
- **Last Updated:** October 28, 2025
- **Source:** Iterable Handlebars Documentation

## Original Source

This structured reference was generated from the comprehensive Iterable Handlebars documentation to optimize for AI agent consumption.

