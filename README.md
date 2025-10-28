# Iterable Handlebars Reference - AI Optimized

**Structured JSON reference for AI agents to efficiently assist with Iterable Handlebars questions.**

## Overview

This reference has been optimized for AI agent consumption with:
- ✅ **Modular JSON structure** - Load only what's needed
- ✅ **Quick lookups** - Direct access to specific helpers/contexts
- ✅ **Structured data** - Schema-enforced, easily queryable
- ✅ **Minimal token usage** - Efficient for AI processing
- ✅ **Comprehensive coverage** - All Handlebars features documented

---

## 🚀 How to Use This Guide

### Option 1: Filesystem MCP (Best for Local Development) ⭐⭐

**Best for:** Personal use, fastest access, offline capability, real-time updates

**Setup (Cursor, Claude Desktop, etc.):**
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/YOUR_USER/Development/IterableHandlebars"]
    }
  }
}
```

**Benefits:**
- ✅ **Fastest access** - No API calls, instant file reads
- ✅ **Works offline** - No internet required
- ✅ **Real-time updates** - Changes immediately available (no git push needed)
- ✅ **No rate limits** - Direct file system access
- ✅ **Lower latency** - No network overhead
- ✅ **Privacy** - Files stay on your machine

**Steps:**
1. Clone this repository to your local machine
2. Add the filesystem MCP configuration (see above)
3. Update the path to match your local directory
4. Restart your AI agent
5. Ask questions - AI loads files on-demand

**Drawbacks:**
- ❌ Only works on your local machine
- ❌ Not automatically shared with team members

---

### Option 2: GitHub MCP (Best for Version Control & Sharing) ⭐

**Best for:** Teams, version tracking, remote access, multi-machine access

1. **Clone or fork this repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/IterableHandlebars.git
   ```

2. **Connect via GitHub MCP**
   - Use the [GitHub MCP](https://github.com/modelcontextprotocol/servers/tree/main/src/github) integration
   - Point to your repository
   - AI agent gets automatic access to all files

3. **Benefits:**
   - ✅ AI agent can read any file on-demand
   - ✅ Modular loading (only loads files when needed)
   - ✅ Always up-to-date with your latest changes
   - ✅ Minimal token usage (loads incrementally)
   - ✅ Can query multiple files efficiently

**Example AI Agent Usage:**
```
You: "How do I use the dateFormat helper?"
AI: *Reads index.json → finds dateFormat in date_time category*
    *Opens schema/helpers.json → date_time → dateFormat*
    *Returns syntax, examples, use cases*
```

---

### Option 3: Claude Projects / Custom GPTs (Best for Team Sharing) ⭐⭐⭐

**Best for:** Zero setup for users, persistent knowledge, easy team access

#### For Claude (Projects):

1. **Create a new Claude Project**
   - Go to Claude.ai → Projects → Create Project
   - Name it "Iterable Handlebars Reference"

2. **Upload key JSON files to Project Knowledge:**
   ```
   ✅ index.json (always include - master catalog)
   ✅ schema/helpers.json (most queries need this)
   ✅ schema/common-patterns.json (for examples)
   ✅ schema/contexts.json (for "where can I use" questions)
   ✅ schema/troubleshooting.json (for debugging)
   ```

3. **Set custom instructions:**
   ```
   You are an expert in Iterable Handlebars templating. Use the uploaded JSON 
   files to provide accurate, detailed answers with code examples. Always 
   reference the schema files for syntax, parameters, and use cases.
   ```

4. **Share with team:**
   - Click "Share" on the project
   - Team members get instant access
   - No technical setup required

**Benefits:**
- ✅ **Zero configuration** for end users
- ✅ **Always available** in that project
- ✅ **Shareable** with unlimited team members
- ✅ **Persistent context** across conversations
- ✅ **No token costs** for repeated access
- ✅ **Works on any device** (web, mobile, desktop)

**Drawbacks:**
- ❌ Manual updates when documentation changes
- ❌ File size limits (~30MB total for Claude)
- ❌ All files pre-loaded (not on-demand)

#### For ChatGPT (Custom GPTs):

1. **Create a Custom GPT**
   - Go to ChatGPT → Explore GPTs → Create
   - Name it "Iterable Handlebars Assistant"

2. **Upload JSON files** as knowledge
3. **Configure instructions** (similar to Claude above)
4. **Share link** with team or publish publicly

**Verdict:** **Best option for non-technical teams and clients!** 🎯

---

### Option 4: Upload Individual JSON Files

**Best for:** Quick one-off questions, specific topics, no persistence needed

**What to upload:**
- For general questions: `index.json` (48 lines, ~2KB)
- For helper details: `schema/helpers.json` (2,109 lines, ~85KB)
- For context info: `schema/contexts.json` (545 lines, ~25KB)
- For patterns: `schema/common-patterns.json` (353 lines, ~15KB)
- For troubleshooting: `schema/troubleshooting.json` (427 lines, ~20KB)

**When to use each:**

| Your Question | Upload This File |
|--------------|------------------|
| "What helpers are available?" | `index.json` |
| "How does [specific helper] work?" | `schema/helpers.json` |
| "Where can I use Handlebars?" | `schema/contexts.json` |
| "Show me a pattern for [use case]" | `schema/common-patterns.json` |
| "My template won't save" | `schema/validation-rules.json` |
| "I'm getting an error" | `schema/troubleshooting.json` |
| "What unsubscribe links exist?" | `schema/universal-parameters.json` |

**Tip:** Upload `index.json` first to get an overview, then upload specific schema files as needed.

---

### Option 5: Use Original Markdown File

**Best for:** Manual reading in text editors, offline reference (humans, not AI)

**File:** `source_guide/ITERABLE_HANDLEBARS_REFERENCE.md`

**Size:** 4,674 lines (~27,860 tokens)

**⚠️ Important Notes:**
- **Too large for some AI agents** - May exceed token limits (typical limit: 25,000 tokens)
- **Requires sequential reading** - AI must parse entire file
- **Higher token costs** - Uses more tokens than JSON approach
- **Good for human reading** - If you prefer Markdown format

**When to use:**
- ✅ Reading in a text editor or IDE
- ✅ Searching with Ctrl+F for specific terms
- ✅ Offline reference without AI agent
- ❌ NOT recommended for AI agent uploads (use JSON instead)

**Alternative:** If your AI agent supports large files, you can try uploading the `.md` file, but the JSON structure is much more efficient.

---

### Option 6: Direct Workspace Access (Cursor/VS Code)

**Best for:** Already using Cursor, VS Code with AI extensions

If your AI agent has workspace access (like Cursor):

```bash
# Point AI agent to this directory
/path/to/IterableHandlebars/

# Agent can then read files on-demand:
# - index.json for quick lookups
# - schema/*.json for detailed information
```

---

## 📊 Comprehensive Comparison

### By Technical Approach

| Method | Setup Difficulty | Speed | Token Usage | Offline | Sharing | Best For |
|--------|------------------|-------|-------------|---------|---------|----------|
| **Filesystem MCP** | Easy | ⚡⚡⚡ Fastest | Very Low | ✅ Yes | ❌ No | Solo developers |
| **GitHub MCP** | Medium | ⚡⚡ Fast | Very Low | ❌ No | ✅ Yes | Teams with git workflow |
| **Claude Projects** | Very Easy | ⚡⚡ Fast | None (pre-loaded) | ❌ No | ✅✅✅ Best | Non-technical teams |
| **JSON Upload** | Very Easy | ⚡ Medium | Low | ✅ Yes | ❌ No | One-off questions |
| **MD Upload** | Very Easy | ⚡ Slow | Very High | ✅ Yes | ❌ No | Human reading only |
| **Workspace Access** | None | ⚡⚡⚡ Fastest | Very Low | ✅ Yes | ❌ No | Cursor/VS Code users |

### By Use Case

| Your Situation | Recommended Method | Why |
|----------------|-------------------|-----|
| **Solo developer with Cursor/VS Code** | Workspace Access or Filesystem MCP | Already integrated, fastest access |
| **Solo developer, other tools** | Filesystem MCP | Fastest, works offline, real-time updates |
| **Small team (2-10 people)** | Claude Projects | Zero setup for users, easy sharing |
| **Large team with git workflow** | GitHub MCP | Version control, change tracking |
| **Clients/customers** | Claude Projects or Custom GPT | No technical knowledge required |
| **One-time consultation** | JSON Upload | Quick, no setup |
| **Need offline access** | Filesystem MCP or MD file | Works without internet |
| **Multiple data sources** | RAG/Vector DB | Semantic search, scalability |
| **Public documentation** | GitHub + Claude Projects | Discoverable + easy to use |

### By Priority

| Priority | Choose This | Reason |
|----------|-------------|--------|
| **Speed** | Filesystem MCP > Workspace Access | Direct file system, no API |
| **Ease of Sharing** | Claude Projects | One click, zero setup |
| **Version Control** | GitHub MCP | Track changes, collaborate |
| **Zero Setup** | Workspace Access (if using Cursor) | Already built-in |
| **Offline Access** | Filesystem MCP | No internet required |
| **Cost** | Filesystem MCP or Workspace | No API costs |
| **Scalability** | Claude Projects or RAG | Handle many users |

---

## 💡 Recommended Workflows

### 🏆 For Solo Developers (You!)

**Best Setup: Filesystem MCP** (if using Cursor/Claude Desktop)
1. Clone repository to your machine
   ```bash
   cd ~/Development
   git clone https://github.com/YOUR_USERNAME/IterableHandlebars.git
   ```
2. Add to MCP config:
   ```json
   {
     "mcpServers": {
       "filesystem": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/YOUR_USER/Development/IterableHandlebars"]
       }
     }
   }
   ```
3. Restart AI agent
4. Ask questions naturally - files load automatically

**Alternative: Just use Cursor workspace** (even simpler!)
- Keep repo in your workspace
- AI already has access via workspace tools
- No MCP setup needed

---

### 👥 For Small Teams (2-10 People)

**Best Setup: Claude Projects**
1. **One person** (you) creates a Claude Project
2. Upload these files to Project Knowledge:
   - `index.json`
   - `schema/helpers.json`
   - `schema/common-patterns.json`
   - `schema/contexts.json`
   - `schema/troubleshooting.json`
3. Set custom instructions (see Option 3 above)
4. **Share project** with team members
5. Everyone gets instant access with zero setup

**Benefits:**
- ✅ Team members need zero technical knowledge
- ✅ Works on any device (web, mobile, desktop)
- ✅ Always available in that project
- ✅ No individual setup required

---

### 🏢 For Large Teams / Enterprises

**Best Setup: Hybrid (GitHub MCP + Claude Projects)**

**For developers:**
- Use GitHub MCP for version-controlled access
- Can update documentation via pull requests
- Track changes over time

**For non-technical users:**
- Provide Claude Projects link
- No git knowledge required
- Easy access to reference

**Setup:**
1. Maintain canonical repository on GitHub
2. Set up GitHub MCP for dev team
3. Create Claude Project for general users
4. Update Claude Project when repo changes

---

### 🤝 For Clients / Customers

**Best Setup: Claude Projects or Custom GPT**
1. Create dedicated Project/GPT for them
2. Upload relevant JSON files
3. Share link or publish GPT
4. They use it instantly - no setup

**Alternative: Public GitHub + Instructions**
- Keep repo public on GitHub
- Provide README with upload instructions
- They choose their preferred method

---

### 🔧 For Active Development

**Best Setup: Local Filesystem MCP**
1. Clone repository locally
2. Use Filesystem MCP (not GitHub MCP)
3. Make changes to JSON files
4. Test immediately (no git push needed)
5. Commit and push when ready

**Benefits:**
- ✅ Instant feedback on changes
- ✅ No network latency
- ✅ Commit only when satisfied

---

### ❓ For One-Off Questions

**Best Setup: JSON Upload**
1. Download this repository (or specific JSON files)
2. Upload relevant file(s) to AI chat
3. Ask your questions
4. Done - no persistence needed

**Which file to upload:**
- General overview → `index.json`
- Helper details → `schema/helpers.json`
- Example patterns → `schema/common-patterns.json`
- Troubleshooting → `schema/troubleshooting.json`

---

## 🔧 Quick Setup Guides

### Filesystem MCP Setup (Recommended for Local Use)

**For Cursor:**

1. **Locate your Cursor config directory:**
   - macOS: `~/Library/Application Support/Cursor/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
   - Windows: `%APPDATA%\Cursor\User\globalStorage\saoudrizwan.claude-dev\settings\cline_mcp_settings.json`
   - Linux: `~/.config/Cursor/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`

2. **Add this configuration:**
   ```json
   {
     "mcpServers": {
       "filesystem": {
         "command": "npx",
         "args": [
           "-y",
           "@modelcontextprotocol/server-filesystem",
           "/Users/YOUR_USERNAME/Development/IterableHandlebars"
         ]
       }
     }
   }
   ```

3. **Update the path** to match where you cloned the repository

4. **Restart Cursor**

5. **Test it:**
   ```
   You: "What date helpers are available in Iterable?"
   AI: *Reads index.json and schema/helpers.json automatically*
   ```

**For Claude Desktop:**

1. **Open config file:**
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`

2. **Add configuration** (same as above)

3. **Restart Claude Desktop**

**Benefits:**
- ⚡ Instant access to all files
- 🔄 Real-time updates (edit JSON, immediately reflected)
- 💾 Works offline
- 🚀 Faster than GitHub MCP

---

### GitHub MCP Setup (For Teams/Version Control)

If you're using an AI agent with GitHub MCP support (like Cursor, Claude Desktop, etc.):

### Step 1: Get the Repository

**Option A: Fork this repository (recommended for customization)**
1. Click "Fork" on GitHub
2. Clone your fork:
   ```bash
   git clone https://github.com/YOUR_USERNAME/IterableHandlebars.git
   ```

**Option B: Clone directly (read-only access)**
```bash
git clone https://github.com/ORIGINAL_OWNER/IterableHandlebars.git
```

### Step 2: Configure GitHub MCP

**For Cursor/Claude Desktop:**
1. Open MCP settings
2. Add GitHub server configuration
3. Point to your repository URL
4. Grant read access

**Example MCP Config:**
```json
{
  "mcpServers": {
    "github": {
      "command": "mcp-server-github",
      "args": ["--repository", "YOUR_USERNAME/IterableHandlebars"]
    }
  }
}
```

### Step 3: Test the Connection

Ask your AI agent:
```
"What Handlebars helpers are available for date formatting?"
```

The AI should:
1. Read `index.json` to find date_time category
2. Open `schema/helpers.json` and extract date helpers
3. Provide details with examples

### Step 4: Start Using

Your AI agent now has access to:
- ✅ All helper definitions (`schema/helpers.json`)
- ✅ Context information (`schema/contexts.json`)
- ✅ Common patterns (`schema/common-patterns.json`)
- ✅ Troubleshooting guides (`schema/troubleshooting.json`)
- ✅ Validation rules (`schema/validation-rules.json`)
- ✅ Universal parameters (`schema/universal-parameters.json`)

**No need to manually upload files - the AI loads them on-demand!**

---

## 📁 What's in This Repository

### Core Files

```
/IterableHandlebars/
├── index.json                    # 📋 Start here - master catalog
├── README.md                     # 📖 This file - usage guide
├── CHANGELOG.md                  # 📝 Version history
└── schema/                       # 🗂️ Structured reference data
    ├── helpers.json              # 🛠️ 70+ Handlebars helpers
    ├── contexts.json             # 📍 Where Handlebars works
    ├── validation-rules.json     # ✅ Syntax rules & validation
    ├── common-patterns.json      # 💡 Real-world examples
    ├── troubleshooting.json      # 🔧 Common issues & solutions
    └── universal-parameters.json # 🌐 Always-available params
```

### Source Files

```
source_guide/
└── ITERABLE_HANDLEBARS_REFERENCE.md  # Original 4,674-line MD file
```

**Note:** The original `.md` file is preserved for reference but is **not optimal for AI agents** due to size. Use the JSON files instead.

---

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

