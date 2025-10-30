# Iterable Handlebars Reference Guide

**A comprehensive reference for AI agents to assist users with Handlebars questions in Iterable.**

This guide provides complete information about:
- ✅ **Where Handlebars can be used** in Iterable (email, SMS, push, in-app, WhatsApp, segmentation, journeys, webhooks, etc.)
- ✅ **Syntax validation rules** to ensure Handlebars code is formatted correctly
- ✅ **All available Handlebars helpers** with syntax examples and use cases
- ✅ **Best practices** for secure, maintainable templates
- ✅ **Troubleshooting** common errors and issues

Use this guide to:
- Answer questions about where Handlebars is accepted in Iterable
- Help validate Handlebars syntax and identify errors
- Provide examples of helper functions and their usage
- Explain data feed syntax, snippet usage, and advanced features
- Debug template rendering issues

---

## Table of Contents

1. [Where Handlebars Can Be Used in Iterable](#where-handlebars-can-be-used-in-iterable)
2. [User Profile Fields Used by Iterable](#user-profile-fields-used-by-iterable)
3. [Universal Merge Parameters](#universal-merge-parameters)
4. [Handlebars Syntax Validation](#handlebars-syntax-validation)
5. [Conditional Logic Helpers](#conditional-logic-helpers)
6. [Date and Time Helpers](#date-and-time-helpers)
7. [Math Helpers](#math-helpers)
8. [Encoding and Hashing Helpers](#encoding-and-hashing-helpers)
9. [Text Helpers](#text-helpers)
10. [HTML Rendering and Escaping](#html-rendering-and-escaping)
11. [Catalog Helpers](#catalog-helpers)
12. [Data Feed Helpers](#data-feed-helpers)
13. [Snippets](#snippets)
14. [Advanced Helpers](#advanced-helpers)

---

## Where Handlebars Can Be Used in Iterable

Handlebars templating is available across many areas of Iterable. Understanding where you can use Handlebars—and the specific behaviors in each context—is essential for building dynamic, personalized experiences.

### Template Content (Standard Usage)

Handlebars works normally in all message template content:

**✅ Email Templates**
- Subject lines
- Preheader text
- HTML body
- Plain text body
- From name and reply-to address

```handlebars
Subject: Hi {{firstName}}, check out these deals!
Body:
<h1>Welcome {{firstName}} {{lastName}}</h1>
<p>Your email: {{email}}</p>
```

**✅ SMS Templates**
- Message body
```handlebars
Hi {{firstName}}! Your order #{{orderId}} has shipped. Track: {{trackingUrl}}
```

**✅ Push Notification Templates** (non-JSON fields)
- Title
- Body
- Deep link URLs
```handlebars
Title: {{firstName}}, you have {{cartItemCount}} items in your cart!
```

**✅ In-App Message Templates** (non-JSON fields)
- Title
- Body
- Button text
- Image URLs
- Deep link URLs

**✅ Mobile Inbox Templates**
- Title
- Body
- Content
- Button text
- Image URLs

**✅ WhatsApp Message Templates**
- Message body
- Header text
- Footer text
- Button text
- Quick reply text
- Variable parameters in approved templates
```handlebars
<!-- WhatsApp approved template with variables -->
Hello {{firstName}}, your order {{orderId}} will arrive on {{deliveryDate}}.
```

---

### Segmentation

You can use Handlebars expressions in **segmentation filters** to create dynamic audience segments.

```handlebars
{{#ifGt totalPurchases 1000}}true{{else}}false{{/ifGt}}
```

**Use cases:**
- Complex conditional logic for segment membership
- Date-based segmentation with `{{now}}` and date helpers
- Calculated fields from user data

```handlebars
<!-- Segment users who joined more than 30 days ago -->
{{#ifLt (dateMath signupDate "add" "30" "days") (now)}}true{{else}}false{{/ifGt}}
```

---

### Dynamic Lists with Parameterized Queries

One of Iterable's most powerful features: you can create **dynamic lists with Handlebars placeholders** that get populated from triggering event data or API calls.

#### How It Works

1. **Create a dynamic list** with Handlebars in the segmentation query
2. **Reference the list** in a triggered campaign or journey
3. **At trigger time**, Iterable replaces the Handlebars placeholders with values from the triggering event or API call

#### Example: Email Domain Filtering

**Step 1: Create the Dynamic List**

In the segmentation tool, create a query using Handlebars:

```
emailDomain equals {{emailDomainFromEvent}}
```

Save this as a **Dynamic List** (e.g., "Users from Event Domain").

**Step 2: Use in a Triggered Campaign**

Reference this list in a triggered campaign or journey.

**Step 3: Trigger with Event Data**

When you trigger the campaign with an event that contains `emailDomainFromEvent`:

```json
{
  "email": "user@example.com",
  "eventName": "purchaseCompleted",
  "dataFields": {
    "emailDomainFromEvent": "example.com",
    "orderId": "12345"
  }
}
```

**Result:** The dynamic list query becomes:
```
emailDomain equals "example.com"
```

The campaign will target users whose `emailDomain` equals `"example.com"`.

---

#### Example: Category-Based Targeting

**Dynamic List Query:**
```
favoriteCategory equals {{categoryFromEvent}}
```

**Triggering API Call:**
```json
POST /api/campaigns/trigger
{
  "campaignId": 123456,
  "recipientEmail": "user@example.com",
  "dataFields": {
    "categoryFromEvent": "Electronics",
    "productName": "Wireless Headphones"
  }
}
```

**Result:** Targets users where `favoriteCategory` equals `"Electronics"`.

---

#### Example: Location-Based Campaigns

**Dynamic List Query:**
```
state equals {{stateFromEvent}} AND city equals {{cityFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "storeVisit",
  "dataFields": {
    "stateFromEvent": "CA",
    "cityFromEvent": "San Francisco",
    "storeName": "Downtown Store"
  }
}
```

**Result:** Targets users in California, San Francisco.

---

#### Example: Purchase Amount Targeting

**Dynamic List Query:**
```
totalPurchases greaterThan {{minimumSpendFromEvent}}
```

**Triggering API Call:**
```json
{
  "email": "user@example.com",
  "eventName": "vipPromotion",
  "dataFields": {
    "minimumSpendFromEvent": 500,
    "promoCode": "VIP2024"
  }
}
```

**Result:** Targets users with `totalPurchases > 500`.

---

#### Example: Multi-Field Dynamic Targeting

**Dynamic List Query:**
```
loyaltyTier equals {{tierFromEvent}} 
AND lastPurchaseDate greaterThan {{cutoffDateFromEvent}}
AND preferredChannel equals {{channelFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "reEngagementCampaign",
  "dataFields": {
    "tierFromEvent": "Gold",
    "cutoffDateFromEvent": "2024-01-01",
    "channelFromEvent": "email"
  }
}
```

**Result:** Highly targeted audience based on multiple event parameters.

---

#### Use Cases

**1. Referral Campaigns**
```
referralSource equals {{sourceFromEvent}}
```
Target users who came from a specific referral source.

**2. Abandoned Cart by Category**
```
lastViewedCategory equals {{categoryFromCart}}
```
Send reminders to users interested in the abandoned product category.

**3. Regional Promotions**
```
country equals {{countryFromEvent}}
```
Run country-specific campaigns dynamically.

**4. Tier-Based Offers**
```
membershipLevel equals {{offerTierFromEvent}}
```
Target specific membership tiers with different offers.

**5. Time-Sensitive Campaigns**
```
lastPurchaseDate lessThan {{campaignStartDate}}
```
Re-engage users who haven't purchased since a certain date.

---

#### Requirements & Best Practices

**✅ Requirements:**
- List must be saved as a **Dynamic List** (not static)
- Handlebars field names in the query must match field names in the triggering event/API call
- Must be used in a **triggered campaign** or **journey** (not scheduled campaigns)
- Event or API call must include the field with the matching name

**✅ DO:**
- Use descriptive field names: `{{emailDomainFromEvent}}`, `{{categoryFromEvent}}`
- Test with actual event data to verify field name matching
- Document which event fields are required for the campaign
- Use consistent naming conventions across events and lists

**❌ DON'T:**
- Try to use with scheduled campaigns (won't have event data)
- Forget to pass the required fields in the triggering event
- Use complex Handlebars helpers in the list query (simple placeholders work best)
- Reuse generic field names that might cause confusion

---

#### Troubleshooting

**List doesn't filter correctly:**
- Verify the event/API call includes the field name (case-sensitive)
- Check that the field name in the query matches exactly: `{{emailDomainFromEvent}}`
- Ensure the list is saved as "Dynamic" not "Static"

**Campaign sends to wrong users:**
- Test the list query with sample data first
- Verify the data type matches (string, number, date)
- Check for typos in field names

**No users in the list:**
- Ensure triggering event has all required fields
- Verify the field values match actual user data
- Check that user profile fields exist and are populated

---

#### Complete Workflow Example

**Scenario:** Send a campaign to users in the same city as a new store opening.

**1. Create Dynamic List:**
- Name: "Users in Store City"
- Query: `city equals {{storeCityFromEvent}}`
- Save as Dynamic List

**2. Create Triggered Campaign:**
- Campaign Type: Triggered
- Audience: "Users in Store City" (the dynamic list)
- Template: Store opening announcement

**3. Set up API call to trigger:**
```json
POST /api/campaigns/trigger
{
  "campaignId": 789012,
  "listId": 456789,
  "dataFields": {
    "storeCityFromEvent": "Seattle",
    "storeName": "Seattle Downtown Store",
    "openingDate": "2024-12-01",
    "storeAddress": "123 Main St, Seattle, WA"
  }
}
```

**4. What happens:**
- Iterable replaces `{{storeCityFromEvent}}` with `"Seattle"`
- Query becomes: `city equals "Seattle"`
- Campaign sends to all users where `city` equals `"Seattle"`
- Template can use `{{storeName}}`, `{{openingDate}}`, etc. from the event data

---

#### Advanced: Combining Static and Dynamic Criteria

You can mix static criteria with dynamic Handlebars placeholders:

```
emailDomain equals {{domainFromEvent}} 
AND emailOptIn equals true
AND country equals "US"
```

This creates a dynamic list that:
- Filters by a dynamic email domain from the event
- Only includes users who opted in to email (static)
- Only includes US users (static)

---

#### Quick Reference: Dynamic Lists

| Component | Example | Notes |
|-----------|---------|-------|
| **List Query** | `city equals {{cityFromEvent}}` | Use Handlebars placeholders |
| **List Type** | Dynamic List | Must be Dynamic, not Static |
| **Campaign Type** | Triggered Campaign or Journey | Won't work with scheduled campaigns |
| **Event Field** | `"cityFromEvent": "Seattle"` | Field name must match exactly |
| **Result** | `city equals "Seattle"` | Placeholder replaced at trigger time |

---

### Catalog Collections with Dynamic Queries

Catalog Collections can use **Handlebars placeholders in collection queries**, allowing you to dynamically filter catalog items based on event data or data feed values.

#### How It Works

1. **Create a catalog collection** with a custom value query
2. **Use Handlebars placeholders** in the query criteria
3. **At runtime**, Iterable replaces the placeholders with values from the triggering event or data feed
4. **Collection returns** only items matching the dynamic criteria

---

#### Example: Filter by Event Data

**Collection Query Setup:**

In the catalog collection configuration, choose **Custom Value** and enter:

```
CatalogID equals {{eventItemId}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "productViewed",
  "dataFields": {
    "eventItemId": "PROD-12345",
    "productName": "Wireless Headphones"
  }
}
```

**Result:**
- Query becomes: `CatalogID equals "PROD-12345"`
- Collection returns only the catalog item with ID `"PROD-12345"`

**Template Usage:**
```handlebars
{{#catalog "products" eventItemId as |product|}}
  <h2>{{product.name}}</h2>
  <p>{{product.description}}</p>
  <span>{{numberFormat product.price "currency" locale}}</span>
{{/catalog}}
```

---

#### Example: Multiple Dynamic Criteria

**Collection Query:**
```
category equals {{categoryFromEvent}} AND price lessThan {{maxPriceFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "browseCategory",
  "dataFields": {
    "categoryFromEvent": "Electronics",
    "maxPriceFromEvent": 100
  }
}
```

**Result:**
- Query becomes: `category equals "Electronics" AND price lessThan 100`
- Collection returns electronics items under $100

---

#### Example: Dynamic Brand Filtering

**Collection Query:**
```
brand equals {{preferredBrandFromEvent}} AND inStock equals true
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "brandAlert",
  "dataFields": {
    "preferredBrandFromEvent": "Nike",
    "alertType": "new-arrivals"
  }
}
```

**Result:**
- Query becomes: `brand equals "Nike" AND inStock equals true`
- Collection returns only Nike items that are in stock

---

#### Using Data Feed Variables in Collections

You can also use **data feed variables** to populate collection queries, but you **must enable** the "Merge the Data Feed and User Contexts" setting.

**Requirements:**
1. Template must be configured to accept data feeds
2. "Merge the Data Feed and User Contexts" must be **enabled**
3. Use `{{}}` syntax (not `[[]]`) for data feed references

**Collection Query with Data Feed:**
```
category equals {{dataFeedCategory}} AND featured equals true
```

**Data Feed Response:**
```json
{
  "dataFeedCategory": "Winter Collection",
  "featuredItems": true
}
```

**Result:**
- Query becomes: `category equals "Winter Collection" AND featured equals true`
- Collection returns featured items from the winter collection

---

#### Example: Data Feed + Event Data Combined

**Collection Query:**
```
category equals {{categoryFromDataFeed}} AND priceRange equals {{priceRangeFromEvent}}
```

**Data Feed Response:**
```json
{
  "categoryFromDataFeed": "Shoes",
  "recommendedStyle": "Athletic"
}
```

**Triggering Event:**
```json
{
  "dataFields": {
    "priceRangeFromEvent": "budget",
    "userId": "user123"
  }
}
```

**Result:**
- Query becomes: `category equals "Shoes" AND priceRange equals "budget"`
- Collection returns budget shoes from the data feed's recommended category

---

#### Use Cases

**1. Abandoned Browse Recovery**
```
productId equals {{abandonedItemId}}
```
Show the exact item the user was viewing.

**2. Similar Items by Category**
```
category equals {{viewedCategory}} AND productId notEquals {{currentProductId}}
```
Show similar items from the same category, excluding the current item.

**3. Price-Based Recommendations**
```
category equals {{interestCategory}} AND price between {{minPrice}} and {{maxPrice}}
```
Show items in the user's price range.

**4. Location-Based Inventory**
```
availableInRegion equals {{userRegion}} AND inStock equals true
```
Show only items available in the user's region.

**5. Personalized Collection by Preference**
```
style equals {{preferredStyle}} AND size equals {{preferredSize}}
```
Pre-filter catalog items by user preferences.

**6. Seasonal Campaign Collections**
```
season equals {{campaignSeason}} AND discount greaterThan {{minDiscount}}
```
Dynamically filter items for seasonal campaigns.

---

#### Requirements & Best Practices

**✅ Requirements:**
- Catalog must exist with queryable fields
- Field names in query must match event/data feed field names (case-sensitive)
- For event data: Must be used in **triggered campaign** or **journey**
- For data feed data: Must enable "Merge the Data Feed and User Contexts"

**✅ DO:**
- Use descriptive field names: `{{categoryFromEvent}}`, `{{itemIdFromDataFeed}}`
- Test collections with sample event/data feed data
- Verify catalog field names match exactly
- Use consistent naming conventions
- Document required event/data feed fields

**❌ DON'T:**
- Try to use in scheduled campaigns (no event data)
- Use data feed variables without enabling merge contexts
- Forget to pass required fields in the triggering event/data feed
- Use overly complex Handlebars helpers in collection queries (simple placeholders work best)

---

#### Troubleshooting

**Collection returns no items:**
- Verify event/data feed includes the field (case-sensitive)
- Check field name matches exactly: `{{eventItemId}}`
- Ensure catalog has items matching the criteria
- Verify data types match (string, number, boolean)

**Collection returns wrong items:**
- Test the collection query with known catalog data
- Check that the field values match actual catalog field values
- Verify the comparison operator (equals, lessThan, etc.)
- Look for typos in field names

**Data feed variables not working:**
- Ensure "Merge the Data Feed and User Contexts" is **enabled**
- Use `{{}}` syntax, not `[[]]` for data feed variables in collections
- Verify data feed is returning the expected fields
- Check data feed URL is rendering correctly

---

#### Complete Workflow Example

**Scenario:** Send personalized product recommendations based on browsing behavior.

**1. Catalog Setup:**
- Catalog: "Products"
- Fields: `productId`, `category`, `price`, `inStock`

**2. Create Catalog Collection:**
- Name: "Dynamic Category Products"
- Query: `category equals {{browsedCategory}} AND inStock equals true`

**3. Create Triggered Campaign:**
- Trigger: `productBrowse` event
- Template includes the collection

**4. Template:**
```handlebars
<h1>More {{browsedCategory}} You Might Like</h1>

{{#each collectionItems}}
  {{#catalog "products" this.productId as |product|}}
    <div class="product-card">
      <img src="{{product.imageUrl}}" alt="{{product.name}}">
      <h3>{{product.name}}</h3>
      <p>{{numberFormat product.price "currency" locale}}</p>
      <a href="{{product.url}}">View Details</a>
    </div>
  {{/catalog}}
{{/each}}
```

**5. Trigger Event:**
```json
{
  "email": "user@example.com",
  "eventName": "productBrowse",
  "dataFields": {
    "browsedCategory": "Electronics",
    "browsedProductId": "PROD-456"
  }
}
```

**6. What Happens:**
- Collection query becomes: `category equals "Electronics" AND inStock equals true`
- Collection returns all in-stock electronics
- Template displays personalized product grid

---

#### Advanced: Combining Static and Dynamic Criteria

You can mix static and dynamic criteria in collection queries:

```
category equals {{categoryFromEvent}} 
AND inStock equals true
AND featured equals true
AND price lessThan 200
```

This creates a collection that:
- Filters by dynamic category from event (dynamic)
- Only includes items in stock (static)
- Only includes featured items (static)
- Only includes items under $200 (static)

---

#### Quick Reference: Catalog Collections

| Component | Example | Notes |
|-----------|---------|-------|
| **Collection Query** | `category equals {{categoryFromEvent}}` | Use Handlebars placeholders |
| **Event Data** | `"categoryFromEvent": "Shoes"` | Field name must match exactly |
| **Data Feed Data** | `{{dataFeedCategory}}` | Requires "Merge contexts" enabled |
| **Campaign Type** | Triggered Campaign or Journey | Won't work with scheduled campaigns |
| **Result** | `category equals "Shoes"` | Placeholder replaced at runtime |
| **Template Access** | `{{#each collectionItems}}` | Use collection in template |

---

### Journey Yes/No Splits with Dynamic Conditions

Journey Yes/No splits can accept **Handlebars placeholders from triggering source data**, allowing you to create dynamic branching logic based on event properties or API call data.

#### How It Works

1. **Create a Yes/No split** in a journey
2. **Use Handlebars placeholders** in the split condition
3. **At trigger time**, Iterable replaces the placeholders with values from the triggering event or API call
4. **User branches** based on the evaluated condition

---

#### Example: Order Value Split

**Journey Setup:**

**Yes/No Split Condition:**
```
orderTotal greaterThan {{minimumOrderFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "orderPlaced",
  "dataFields": {
    "minimumOrderFromEvent": 100,
    "orderId": "ORD-12345",
    "orderTotal": 150
  }
}
```

**Result:**
- Condition becomes: `orderTotal greaterThan 100`
- User's `orderTotal` is `150`
- User takes the **YES** branch (150 > 100)

---

#### Example: Product Category Split

**Yes/No Split Condition:**
```
lastPurchaseCategory equals {{targetCategoryFromEvent}}
```

**Triggering API Call:**
```json
POST /api/workflows/triggerWorkflow
{
  "workflowId": 123456,
  "email": "user@example.com",
  "dataFields": {
    "targetCategoryFromEvent": "Electronics",
    "campaignName": "Tech Enthusiasts"
  }
}
```

**Result:**
- If user's `lastPurchaseCategory` is `"Electronics"` → **YES** branch
- Otherwise → **NO** branch

---

#### Example: Location-Based Split

**Yes/No Split Condition:**
```
state equals {{stateFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "regionalPromotion",
  "dataFields": {
    "stateFromEvent": "CA",
    "promoCode": "CA2024",
    "discountPercent": 20
  }
}
```

**Result:**
- California users → **YES** branch (get CA-specific content)
- Other states → **NO** branch (get generic content)

---

#### Example: Membership Tier Split

**Yes/No Split Condition:**
```
loyaltyTier equals {{requiredTierFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "vipEvent",
  "dataFields": {
    "requiredTierFromEvent": "Platinum",
    "eventDate": "2024-12-15",
    "eventLocation": "San Francisco"
  }
}
```

**Result:**
- Platinum members → **YES** branch (VIP invitation)
- Other tiers → **NO** branch (waitlist or alternate offer)

---

#### Example: Date-Based Split

**Yes/No Split Condition:**
```
lastPurchaseDate greaterThan {{cutoffDateFromEvent}}
```

**Triggering API Call:**
```json
{
  "email": "user@example.com",
  "eventName": "winbackCampaign",
  "dataFields": {
    "cutoffDateFromEvent": "2024-06-01",
    "offerType": "reactivation"
  }
}
```

**Result:**
- Users who purchased after June 1, 2024 → **YES** branch (gentle nudge)
- Users who haven't purchased since June 1 → **NO** branch (aggressive offer)

---

#### Example: Multi-Condition Split

**Yes/No Split Condition:**
```
totalPurchases greaterThan {{minPurchasesFromEvent}} 
AND loyaltyPoints greaterThan {{minPointsFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "eliteProgram",
  "dataFields": {
    "minPurchasesFromEvent": 10,
    "minPointsFromEvent": 500,
    "programName": "Elite Rewards"
  }
}
```

**Result:**
- Users with 10+ purchases AND 500+ points → **YES** branch
- All others → **NO** branch

---

#### Use Cases

**1. Dynamic Discount Qualification**
```
totalSpent greaterThan {{qualifyingAmountFromEvent}}
```
Adjust discount thresholds per campaign.

**2. Geographic Targeting**
```
country equals {{targetCountryFromEvent}}
```
Branch based on user location vs. campaign target.

**3. Segment Membership Check**
```
userSegment equals {{targetSegmentFromEvent}}
```
Route users based on dynamic segment assignment.

**4. Product Affinity**
```
favoriteCategory equals {{categoryFromEvent}}
```
Personalize journey based on product interest.

**5. Engagement Level**
```
emailOpensLast30Days greaterThan {{engagementThresholdFromEvent}}
```
Different paths for engaged vs. dormant users.

**6. Subscription Status**
```
subscriptionTier equals {{requiredTierFromEvent}}
```
Exclusive content for specific subscription levels.

**7. Time-Window Filtering**
```
lastActivityDate greaterThan {{activityWindowFromEvent}}
```
Target recently active users differently.

---

#### Combining with Template Data

The event data is also available in the journey templates:

**Event:**
```json
{
  "email": "user@example.com",
  "eventName": "campaignTrigger",
  "dataFields": {
    "minimumSpendFromEvent": 100,
    "offerCode": "SAVE20",
    "expirationDate": "2024-12-31"
  }
}
```

**Yes/No Split:**
```
totalPurchases greaterThan {{minimumSpendFromEvent}}
```

**Template (YES branch):**
```handlebars
<h1>Congratulations!</h1>
<p>You qualify for our exclusive offer!</p>
<p>Use code: <strong>{{offerCode}}</strong></p>
<p>Expires: {{expirationDate}}</p>
```

**Template (NO branch):**
```handlebars
<h1>You're almost there!</h1>
<p>Spend just a bit more to unlock exclusive rewards.</p>
<p>Current threshold: ${{minimumSpendFromEvent}}</p>
```

Both branches can access the event data!

---

#### Requirements & Best Practices

**✅ Requirements:**
- Must be used in a **Journey** (not standalone campaigns)
- Journey must be **triggered** (not scheduled)
- Field names in split condition must match event/API data fields
- Event or API call must include the matching field

**✅ DO:**
- Use descriptive field names: `{{minimumOrderFromEvent}}`
- Test journey with sample event data
- Document required event fields for the journey
- Use consistent naming across events and conditions
- Consider fallback branches for missing data

**❌ DON'T:**
- Try to use in scheduled journeys (no event data)
- Forget to pass required fields in the triggering event
- Use overly complex Handlebars expressions (keep it simple)
- Assume default values - explicitly pass all needed fields

---

#### Troubleshooting

**Split always goes to NO branch:**
- Verify event/API call includes the field (case-sensitive)
- Check field name matches exactly: `{{minimumOrderFromEvent}}`
- Ensure data types match (number vs. string)
- Test with known user data first

**Users going to wrong branch:**
- Verify the comparison operator (equals, greaterThan, etc.)
- Check that user profile fields exist and are populated
- Test the condition with actual user data
- Look for typos in field names

**Event data not available in templates:**
- Ensure journey is triggered (not scheduled)
- Verify event includes all needed fields
- Check that templates are using correct field names

---

#### Complete Journey Example

**Scenario:** VIP event invitation based on dynamic spend threshold.

**1. Journey Setup:**
- Trigger: `vipEventInvite` event
- Yes/No Split: `totalSpent greaterThan {{spendThresholdFromEvent}}`
  - **YES branch:** VIP invitation email
  - **NO branch:** Thank you email with encouragement

**2. Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "vipEventInvite",
  "dataFields": {
    "spendThresholdFromEvent": 500,
    "eventName": "Exclusive Preview Night",
    "eventDate": "2024-12-20",
    "eventLocation": "Downtown Showroom",
    "rsvpLink": "https://example.com/rsvp"
  }
}
```

**3. Split Evaluation:**
- Condition becomes: `totalSpent greaterThan 500`
- User A: `totalSpent = 750` → **YES** branch
- User B: `totalSpent = 300` → **NO** branch

**4. YES Branch Template:**
```handlebars
<h1>You're Invited!</h1>
<p>Dear {{firstName}},</p>
<p>Thank you for being a valued customer! You're invited to our exclusive:</p>
<h2>{{eventName}}</h2>
<p><strong>Date:</strong> {{eventDate}}</p>
<p><strong>Location:</strong> {{eventLocation}}</p>
<p>You've spent ${{numberFormat totalSpent "currency" locale}} with us - you deserve this!</p>
<a href="{{rsvpLink}}">RSVP Now</a>
```

**5. NO Branch Template:**
```handlebars
<h1>Thank You!</h1>
<p>Dear {{firstName}},</p>
<p>We appreciate your support! While this event is for our VIP customers 
   (${{{spendThresholdFromEvent}}}+ total spend), we'd love to see you there next time.</p>
<p>You've spent ${{numberFormat totalSpent "currency" locale}}. 
   Keep shopping to unlock exclusive perks!</p>
```

---

#### Advanced: Multiple Dynamic Splits in Sequence

You can chain multiple Yes/No splits with different dynamic conditions:

**Journey Flow:**
```
Trigger Event
    ↓
Split 1: state equals {{targetStateFromEvent}}
    ├─ YES → Split 2: totalSpent greaterThan {{vipThresholdFromEvent}}
    │            ├─ YES → VIP State-Specific Offer
    │            └─ NO → Regular State-Specific Offer
    └─ NO → Split 3: totalSpent greaterThan {{vipThresholdFromEvent}}
                 ├─ YES → VIP National Offer
                 └─ NO → Regular National Offer
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "complexCampaign",
  "dataFields": {
    "targetStateFromEvent": "CA",
    "vipThresholdFromEvent": 1000,
    "nationalOfferCode": "SAVE10",
    "stateOfferCode": "CA15"
  }
}
```

Each split evaluates dynamically based on the same event data!

---

#### Quick Reference: Journey Splits

| Component | Example | Notes |
|-----------|---------|-------|
| **Split Condition** | `totalSpent greaterThan {{minFromEvent}}` | Use Handlebars placeholders |
| **Journey Type** | Triggered Journey | Won't work with scheduled |
| **Event Field** | `"minFromEvent": 100` | Field name must match exactly |
| **Result** | `totalSpent greaterThan 100` | Evaluated per user |
| **Template Access** | `{{minFromEvent}}` in emails | Event data available in all journey messages |

---

### Data Feed URLs

Data feed URLs can accept **Handlebars** just like webhook URLs, allowing you to create dynamic, personalized data feed requests for each user.

**Location:** Template Settings → Data Feeds → Data Feed URL

#### Basic Dynamic Data Feed URL

**Example:**
```handlebars
https://api.example.com/recommendations?userId={{userId}}&category={{favoriteCategory}}&locale={{locale}}
```

**At send time for user:**
```
https://api.example.com/recommendations?userId=user123&category=Electronics&locale=en_US
```

Each user gets a personalized data feed URL with their own data!

---

#### Use Cases

**1. Personalized Product Recommendations**
```handlebars
https://api.example.com/products/recommend?userId={{userId}}&tier={{loyaltyTier}}&budget={{averageOrderValue}}
```

**2. Location-Based Content**
```handlebars
https://api.example.com/stores/nearby?zip={{zipCode}}&radius={{preferredRadius}}&country={{country}}
```

**3. User-Specific Inventory**
```handlebars
https://api.example.com/inventory?userId={{userId}}&lastCategory={{lastViewedCategory}}&priceMax={{pricePreference}}
```

**4. Personalized News Feed**
```handlebars
https://api.example.com/news?topics={{join interests ","}}&locale={{locale}}&userId={{userId}}
```

**5. Dynamic Pricing**
```handlebars
https://api.example.com/pricing?userId={{userId}}&tier={{membershipLevel}}&region={{state}}
```

---

#### With Campaign/Event Data

You can combine user profile data with campaign data:

```handlebars
https://api.example.com/offers?userId={{userId}}&campaignId={{campaignId}}&offerType={{offerTypeFromEvent}}
```

In a triggered campaign, `offerTypeFromEvent` comes from the triggering event:

```json
{
  "email": "user@example.com",
  "eventName": "specialOffer",
  "dataFields": {
    "offerTypeFromEvent": "seasonal",
    "discountPercent": 20
  }
}
```

---

#### Advanced: Using Snippets in Data Feed URLs

One of the most powerful (and complex) techniques: **using snippets to dynamically build data feed URLs**.

**Why use snippets in data feed URLs?**
- Build complex query strings with conditional logic
- Construct URLs based on user attributes
- Reuse URL-building logic across multiple templates

#### Example: Conditional Parameters Snippet

**Snippet Name:** `build-recommendation-params`

**Snippet Content:**
```handlebars
userId={{userId}}{{#if favoriteCategory}}&category={{urlEncode favoriteCategory}}{{/if}}{{#if loyaltyTier}}&tier={{urlEncode loyaltyTier}}{{/if}}{{#ifGt totalPurchases 0}}&returning=true{{/ifGt}}
```

**Data Feed URL:**
```handlebars
https://api.example.com/recommendations?{{{snippet "build-recommendation-params"}}}
```

**Result for user with full data:**
```
https://api.example.com/recommendations?userId=user123&category=Electronics&tier=Gold&returning=true
```

**Result for new user with minimal data:**
```
https://api.example.com/recommendations?userId=user456
```

The snippet conditionally adds parameters based on available user data!

---

#### Example: Multi-Source Parameter Building

**Snippet Name:** `build-feed-url-params`

**Snippet Content:**
```handlebars
userId={{userId}}&email={{urlEncode email}}{{#if campaignId}}&campaignId={{campaignId}}{{/if}}{{#if state}}&region={{urlEncode state}}{{/if}}{{#if locale}}&locale={{locale}}{{else}}&locale=en_US{{/if}}
```

**Data Feed URL:**
```handlebars
https://api.example.com/content?{{{snippet "build-feed-url-params"}}}
```

**Advantages:**
- Reusable across multiple templates
- Centralized URL logic
- Easy to maintain and update
- Handles missing fields gracefully

---

#### Example: API Key and Authentication Snippet

**Snippet Name:** `api-auth-params`

**Snippet Content:**
```handlebars
apiKey=YOUR_API_KEY&userId={{userId}}&timestamp={{now format="yyyy-MM-dd'T'HH:mm:ss'Z'"}}&hash={{sha256 (concat userId "YOUR_SECRET")}}
```

**Data Feed URL:**
```handlebars
https://api.example.com/secure-data?{{{snippet "api-auth-params"}}}
```

Centralizes authentication logic for secure API calls!

---

#### Best Practices for Data Feed URLs

**✅ DO:**
- Always use `{{urlEncode field}}` for query parameter values
- Test data feed URLs with actual user data
- Handle missing user fields gracefully with snippets
- Cache data feed responses when possible (template setting)
- Use HTTPS for all data feed URLs

**❌ DON'T:**
- Forget to URL-encode values (will break with special characters)
- Put sensitive data in URLs (use headers or POST if available)
- Create overly complex URLs (keep readable)
- Hardcode values that could be parameterized

---

#### Triple Braces Required for Snippets

When using snippets in URLs, use **triple braces** `{{{}}}` to prevent HTML escaping:

**✅ Correct:**
```handlebars
https://api.example.com?{{{snippet "params"}}}
```

**❌ Wrong (will escape ampersands):**
```handlebars
https://api.example.com?{{snippet "params"}}
```

Without triple braces, `&` becomes `&amp;` and breaks the URL!

---

#### Complete Example: Dynamic Product Feed

**Scenario:** Personalized product recommendations based on user profile and campaign data.

**Snippet Name:** `product-feed-params`

**Snippet Content:**
```handlebars
userId={{userId}}&category={{urlEncode favoriteCategory}}{{#if loyaltyTier}}&tier={{urlEncode loyaltyTier}}{{/if}}&priceMax={{#if maxPrice}}{{maxPrice}}{{else}}1000{{/if}}{{#if campaignId}}&campaign={{campaignId}}{{/if}}{{#if targetCategoryFromEvent}}&targetCategory={{urlEncode targetCategoryFromEvent}}{{/if}}
```

**Template Data Feed Configuration:**
- **Data Feed URL:** 
  ```handlebars
  https://api.example.com/products/recommend?{{{snippet "product-feed-params"}}}
  ```
- **Alias:** `recommendations`
- **Cache:** Enabled (1 hour)

**Template Usage:**
```handlebars
<h1>Hi {{firstName}}, check out these picks for you!</h1>

{{#each [[recommendations.products]]}}
  <div class="product">
    <img src="[[this.imageUrl]]" alt="[[this.name]]">
    <h3>[[this.name]]</h3>
    <p>{{numberFormat [[this.price]] "currency" locale}}</p>
    <a href="[[this.url]]">Shop Now</a>
  </div>
{{/each}}
```

**What happens at send time:**
1. Snippet builds URL: `https://api.example.com/products/recommend?userId=user123&category=Electronics&tier=Gold&priceMax=1000&campaign=456&targetCategory=Headphones`
2. Iterable fetches data from this URL
3. Response is available as `[[recommendations.products]]`
4. Template loops through and displays personalized products

---

### Snippets in Subject Lines

Snippets can be used in **email subject lines** to create reusable, dynamic subject line logic.

**Why use snippets in subject lines?**
- Reuse complex logic across multiple templates
- Maintain consistent subject line patterns
- Centralize personalization rules
- A/B test subject lines from one place

---

#### Basic Subject Line Snippet

**Snippet Name:** `personalized-greeting`

**Snippet Content:**
```handlebars
{{#if firstName}}{{firstName}}{{else}}there{{/if}}
```

**Subject Line:**
```handlebars
Hi {{{snippet "personalized-greeting"}}}, check out our latest deals!
```

**Result:**
- User with firstName: `Hi Sarah, check out our latest deals!`
- User without firstName: `Hi there, check out our latest deals!`

---

#### Example: Dynamic Urgency

**Snippet Name:** `urgency-text`

**Snippet Content:**
```handlebars
{{#ifLt (dateMath (now) "add" "2" "days") expirationDate}}⏰ Expires Soon{{else}}Limited Time{{/ifLt}}
```

**Subject Line:**
```handlebars
{{{snippet "urgency-text"}}}: Save {{discountPercent}}% on {{productCategory}}
```

**Result:**
- If expiration < 2 days: `⏰ Expires Soon: Save 20% on Electronics`
- Otherwise: `Limited Time: Save 20% on Electronics`

---

#### Example: Tier-Based Prefix

**Snippet Name:** `tier-prefix`

**Snippet Content:**
```handlebars
{{#ifEq loyaltyTier "Platinum"}}💎 VIP{{else}}{{#ifEq loyaltyTier "Gold"}}⭐ Exclusive{{else}}🎁{{/ifEq}}{{/ifEq}}
```

**Subject Line:**
```handlebars
{{{snippet "tier-prefix"}}} Offer: {{offerTitle}}
```

**Result:**
- Platinum: `💎 VIP Offer: Early Access to New Products`
- Gold: `⭐ Exclusive Offer: Early Access to New Products`
- Other: `🎁 Offer: Early Access to New Products`

---

#### Example: Smart Personalization

**Snippet Name:** `smart-subject-name`

**Snippet Content:**
```handlebars
{{#if firstName}}{{firstName}}{{else}}{{#if email}}{{email}}{{else}}Valued Customer{{/if}}{{/if}}
```

**Subject Line:**
```handlebars
{{{snippet "smart-subject-name"}}}, you left items in your cart!
```

**Fallback hierarchy:**
1. First name if available
2. Email address if firstName missing
3. "Valued Customer" if both missing

---

#### Example: Event-Driven Subject

**Snippet Name:** `event-subject-builder`

**Snippet Content:**
```handlebars
{{#if eventName}}{{#ifEq eventTypeFromEvent "urgent"}}🚨 URGENT{{else}}{{#ifEq eventTypeFromEvent "reminder"}}⏰ Reminder{{else}}📬{{/ifEq}}{{/ifEq}}{{else}}📬{{/if}}
```

**Subject Line (in triggered campaign):**
```handlebars
{{{snippet "event-subject-builder"}}}: {{subjectFromEvent}}
```

**Triggering Event:**
```json
{
  "email": "user@example.com",
  "eventName": "notification",
  "dataFields": {
    "eventTypeFromEvent": "urgent",
    "subjectFromEvent": "Your account needs attention"
  }
}
```

**Result:** `🚨 URGENT: Your account needs attention`

---

#### Best Practices for Snippets in Subject Lines

**✅ DO:**
- Use **triple braces** `{{{}}}` to prevent HTML escaping
- Keep subject lines under 50 characters total
- Test with users who have missing data
- Use emojis sparingly (some email clients don't support them)
- Provide fallback values for all dynamic fields

**❌ DON'T:**
- Create overly long subject lines
- Use complex logic that's hard to debug
- Forget to test on mobile devices
- Overuse emojis (looks spammy)
- Forget triple braces (will show Handlebars code)

---

#### Triple Braces Required

Always use **triple braces** for snippets in subject lines:

**✅ Correct:**
```handlebars
{{{snippet "personalized-greeting"}}}
```

**❌ Wrong (will show raw Handlebars):**
```handlebars
{{snippet "personalized-greeting"}}
```

---

#### Complete Example: Multi-Template Subject Line Snippet

**Scenario:** Maintain consistent personalized subject line pattern across all marketing emails.

**Snippet Name:** `marketing-subject-prefix`

**Snippet Content:**
```handlebars
{{#if firstName}}Hi {{firstName}}{{else}}Hello{{/if}}{{#ifEq loyaltyTier "Platinum"}} 💎{{else}}{{#ifEq loyaltyTier "Gold"}} ⭐{{/ifEq}}{{/ifEq}}
```

**Use in Multiple Templates:**

**Template 1 (Newsletter):**
```handlebars
Subject: {{{snippet "marketing-subject-prefix"}}} - This Week's Top Picks
```

**Template 2 (Sale):**
```handlebars
Subject: {{{snippet "marketing-subject-prefix"}}} - Flash Sale: 50% Off!
```

**Template 3 (Abandoned Cart):**
```handlebars
Subject: {{{snippet "marketing-subject-prefix"}}} - You Left Items in Your Cart
```

**Result for Platinum user "Sarah":**
- Template 1: `Hi Sarah 💎 - This Week's Top Picks`
- Template 2: `Hi Sarah 💎 - Flash Sale: 50% Off!`
- Template 3: `Hi Sarah 💎 - You Left Items in Your Cart`

**Result for standard user without firstName:**
- Template 1: `Hello - This Week's Top Picks`
- Template 2: `Hello - Flash Sale: 50% Off!`
- Template 3: `Hello - You Left Items in Your Cart`

**Benefits:**
- Update snippet once, affects all templates
- Consistent personalization across campaigns
- Easy A/B testing of subject line patterns
- Centralized logic maintenance

---

### Hosted Unsubscribe URL (Project Settings)

You can create **dynamic hosted unsubscribe URLs** in Project Settings using Handlebars to pass user information.

**Location:** Project Settings → Subscription Settings → Hosted Unsubscribe URL

**Example:**
```handlebars
https://example.com/preferences?email={{urlEncode email}}&userId={{urlEncode userId}}&listIds={{join emailListIds ","}}
```

**Common use cases:**
```handlebars
<!-- Pass multiple parameters -->
https://example.com/unsubscribe?email={{urlEncode email}}&campaignId={{campaignId}}&timestamp={{now format="yyyy-MM-dd"}}

<!-- Include custom user fields -->
https://example.com/preferences?userId={{userId}}&locale={{locale}}&tier={{loyaltyTier}}
```

**Then use in templates:**
```handlebars
<a href="{{hostedUnsubscribeUrl}}">Manage your email preferences</a>
```

---

### Webhook URLs

Handlebars can be used in **webhook endpoint URLs** to pass dynamic parameters in the query string.

**Location:** Integrations → Webhooks → Webhook URL

**Example:**
```handlebars
https://api.example.com/events?userId={{userId}}&email={{urlEncode email}}&eventType={{eventName}}
```

**Use cases:**
```handlebars
<!-- Pass campaign metadata -->
https://tracking.example.com/webhook?campaignId={{campaignId}}&templateId={{templateId}}&timestamp={{now format="yyyy-MM-dd'T'HH:mm:ss"}}

<!-- Include user segmentation data -->
https://api.example.com/sync?userId={{userId}}&tier={{loyaltyTier}}&listIds={{join emailListIds "|"}}
```

---

### Webhook JSON Body

You can use Handlebars in the **webhook JSON payload**, but with an **important limitation**: all Handlebars references are **automatically converted to strings**.

**Location:** Integrations → Webhooks → JSON Body

#### ⚠️ Critical Behavior: Automatic String Conversion

**What you want to write:**
```json
{
  "userId": {{userId}},
  "age": {{age}},
  "isPremium": {{isPremiumUser}},
  "purchaseCount": {{totalPurchases}}
}
```

**What Iterable actually saves and sends:**
```json
{
  "userId": "{{userId}}",
  "age": "{{age}}",
  "isPremium": "{{isPremiumUser}}",
  "purchaseCount": "{{totalPurchases}}"
}
```

**Result at send time:**
```json
{
  "userId": "user123",
  "age": "35",
  "isPremium": "true",
  "purchaseCount": "47"
}
```

:::danger IMPORTANT
**All Handlebars values become strings, even if the original data is a number or boolean.**

- Numbers become `"123"` instead of `123`
- Booleans become `"true"` or `"false"` instead of `true` or `false`
- Arrays become stringified versions

Your receiving API must handle string-to-type conversions.
:::

---

#### ✅ Correct Way to Use Handlebars in JSON

Since everything becomes a string, always quote your Handlebars references:

```json
{
  "user": {
    "email": "{{email}}",
    "userId": "{{userId}}",
    "firstName": "{{firstName}}",
    "lastName": "{{lastName}}"
  },
  "campaign": {
    "campaignId": "{{campaignId}}",
    "campaignName": "{{campaignName}}",
    "templateId": "{{templateId}}"
  },
  "metadata": {
    "sentAt": "{{now format='yyyy-MM-dd'T'HH:mm:ss'Z'}}",
    "isPremium": "{{isPremiumUser}}",
    "totalSpent": "{{totalPurchases}}"
  }
}
```

---

#### Using Arrays and Objects

**Arrays require string formatting:**

```json
{
  "listIds": "{{join emailListIds ','}}",
  "tags": "{{join userTags '|'}}"
}
```

**Result:**
```json
{
  "listIds": "101,203,405",
  "tags": "vip|subscriber|purchaser"
}
```

**Nested objects - use string values:**
```json
{
  "user": {
    "profile": {
      "city": "{{city}}",
      "state": "{{state}}",
      "country": "{{country}}"
    }
  }
}
```

---

#### Workaround: Use `toJson` Helper

For complex objects, consider using the `toJson` helper:

```json
{
  "userData": "{{toJson user}}",
  "customFields": "{{toJson customFields}}"
}
```

**Result (as string, but properly formatted JSON):**
```json
{
  "userData": "{\"email\":\"user@example.com\",\"firstName\":\"John\"}",
  "customFields": "{\"tier\":\"Gold\",\"points\":1500}"
}
```

Your receiving API can then parse these JSON strings.

---

### Raw JSON Payloads in Templates

Some template types accept **raw JSON payloads** for custom data or platform-specific configurations. The **same string conversion behavior applies**.

#### Push Notification Templates (Custom Payload)

**Location:** Push Template → Advanced → Custom Payload

**Example (what you write):**
```json
{
  "aps": {
    "alert": "{{pushMessage}}",
    "badge": "{{badgeCount}}",
    "sound": "default"
  },
  "customData": {
    "userId": "{{userId}}",
    "campaignId": "{{campaignId}}",
    "deepLink": "{{deepLinkUrl}}"
  }
}
```

**What gets sent (all strings):**
```json
{
  "aps": {
    "alert": "You have a new message!",
    "badge": "5",
    "sound": "default"
  },
  "customData": {
    "userId": "user123",
    "campaignId": "456789",
    "deepLink": "myapp://product/789"
  }
}
```

:::warning
`"badge": "5"` is a **string**, not a number. Most push platforms require the badge to be a number. Your backend or mobile SDK may need to parse this.
:::

---

#### In-App Message Templates (Custom Payload)

**Location:** In-App Template → Advanced → Custom Payload

Same behavior as push notifications:

```json
{
  "title": "{{inAppTitle}}",
  "ctaUrl": "{{ctaLink}}",
  "displayTime": "{{displayDuration}}",
  "priority": "{{messagePriority}}"
}
```

**All values will be strings at send time.**

---

#### WhatsApp Templates

WhatsApp templates support Handlebars in:
- Message body content
- Header text (if applicable)
- Footer text (if applicable)
- Variable parameters within approved templates

**Example:**
```json
{
  "body": "Hi {{firstName}}, your appointment is scheduled for {{appointmentDate}} at {{appointmentTime}}.",
  "header": "{{businessName}} Appointment Confirmation"
}
```

**Note:** WhatsApp templates must be pre-approved by Meta. Handlebars variables are substituted at send time, but the template structure must match the approved version.

#### Embedded Messaging (In-App Messages via SDK)

Embedded messaging supports Handlebars in:
- Message content (HTML and text)
- Button text and URLs
- Image URLs
- Custom action payloads

**Note:** If custom payloads include JSON, the same string conversion behavior applies (all values become strings).

---

### Best Practices for JSON Handlebars Usage

**✅ DO:**
- Always quote Handlebars references in JSON: `"field": "{{value}}"`
- Document that your receiving API must parse strings to numbers/booleans
- Use `toJson` for complex nested objects
- Use `join` helper for arrays: `"{{join array ','}}"`
- Test with actual data to verify string conversion

**❌ DON'T:**
- Try to use unquoted Handlebars for numbers: `"age": {{age}}` ❌
- Expect booleans to remain boolean: `"active": {{isActive}}` ❌
- Assume arrays will be native JSON arrays
- Forget to URL-encode values in webhook URLs: use `{{urlEncode field}}`

---

### Quick Reference: Handlebars Usage by Location

| Location | Handlebars Supported | String Conversion | Notes |
|----------|---------------------|-------------------|-------|
| Email body/subject | ✅ Yes | No | Standard usage, HTML supported |
| Email subject lines | ✅ Yes | No | **Snippets supported** |
| Email headers | ✅ Yes | No | From name, reply-to, preheader |
| SMS body | ✅ Yes | No | Text only, no HTML |
| Push title/body | ✅ Yes | No | Non-JSON fields only |
| Push deep links | ✅ Yes | No | Dynamic URL parameters |
| In-App title/body | ✅ Yes | No | Non-JSON fields, HTML supported |
| In-App buttons/images | ✅ Yes | No | Button text, image URLs |
| Mobile Inbox | ✅ Yes | No | Title, body, content |
| **WhatsApp** | ✅ Yes | No | Message body, headers, approved template variables |
| **Embedded Messaging** | ✅ Yes | No | Content, buttons, custom actions |
| Segmentation | ✅ Yes | No | Dynamic segment logic |
| Dynamic Lists | ✅ Yes | No | Parameterized queries from events |
| Journey Yes/No Splits | ✅ Yes | No | Dynamic branching from events |
| Data Feed URLs | ✅ Yes | No | **Snippets supported for complex URLs** |
| Data Feed Headers | ✅ Yes | No | Custom HTTP headers |
| Hosted Unsubscribe URL | ✅ Yes | No | Query parameters |
| Webhook URLs | ✅ Yes | No | Query parameters |
| Webhook JSON Body | ✅ Yes | **⚠️ Yes - All strings** | All values stringified |
| Webhook Headers | ✅ Yes | No | Custom HTTP headers |
| Push Custom Payload | ✅ Yes | **⚠️ Yes - All strings** | Numbers/booleans → strings |
| In-App Custom Payload | ✅ Yes | **⚠️ Yes - All strings** | Numbers/booleans → strings |
| WhatsApp Custom Data | ✅ Yes | **⚠️ Yes - All strings** | If using custom payloads |
| Template Previews | ✅ Yes | No | Renders for testing |
| Template Validation | ✅ Yes | No | Syntax checking before save |

---

### Complete Webhook Example

**Webhook URL:**
```handlebars
https://api.example.com/events?campaignId={{campaignId}}&timestamp={{now format="yyyy-MM-dd"}}
```

**Webhook JSON Body:**
```json
{
  "event": "email_sent",
  "timestamp": "{{now format='yyyy-MM-dd'T'HH:mm:ss'Z'}}",
  "user": {
    "email": "{{email}}",
    "userId": "{{userId}}",
    "firstName": "{{firstName}}",
    "isPremium": "{{isPremiumUser}}",
    "totalSpent": "{{numberFormat totalPurchases 'decimal' locale}}"
  },
  "campaign": {
    "id": "{{campaignId}}",
    "name": "{{campaignName}}",
    "templateId": "{{templateId}}"
  },
  "lists": "{{join sendListIds ','}}",
  "metadata": {
    "source": "iterable",
    "workflowId": "{{workflowId}}"
  }
}
```

**Receiving API must parse:**
- `isPremium` from string `"true"` to boolean `true`
- `totalSpent` from string to number
- `lists` from comma-delimited string to array

---

## User Profile Fields Used by Iterable

Iterable uses specific reserved fields in user profiles for system functionality. Understanding these fields helps you avoid conflicts and utilize Iterable's built-in features properly.

### Required Fields

Every user in Iterable must have **at least one** identifier:

**Email Address**
- Field: `email`
- Use: Primary identifier for email campaigns
- Format: Valid email address
- Example: `user@example.com`

**User ID**
- Field: `userId`
- Use: Unique identifier for API calls and cross-channel messaging
- Format: String (typically alphanumeric)
- Example: `"user123"` or `"550e8400-e29b-41d4-a716-446655440000"`

:::note
You can identify users by `email` only, `userId` only, or both. Using both provides more flexibility for cross-channel campaigns.
:::

---

### Reserved Fields

These fields have special meaning in Iterable and should **not** be used for custom data:

#### Contact Information
- `email` - User's email address (also serves as identifier)
- `phoneNumber` - User's mobile phone number (for SMS)
- `userId` - User's unique identifier

#### Subscription Management
- `emailListIds` - Lists the user is subscribed to (email)
- `unsubscribedChannelIds` - Channels user has unsubscribed from
- `unsubscribedMessageTypeIds` - Message types user has unsubscribed from
- `subscribedMessageTypeIds` - Message types user is subscribed to

#### Mobile & Push
- `mergeNestedObjects` - Controls object merging behavior for mobile/push
- `preferUserId` - Prefer userId over email for identification

#### Metadata
- `createdAt` - Timestamp when user profile was created
- `profileUpdatedAt` - Timestamp of last profile update
- `signupDate` - Date user signed up
- `signupSource` - Source of user signup

#### Campaign Data
- `campaignId` - ID of associated campaign
- `templateId` - ID of associated template
- `messageId` - Unique message identifier
- `itblInternal` - Internal Iterable data (do not modify)

---

### Best Practices

**✅ DO:**
- Use `email` or `userId` as your primary identifier
- Store custom user data in custom fields (e.g., `firstName`, `lastName`, `preferences`)
- Use consistent field naming across your project
- Document your custom fields

**❌ DON'T:**
- Use reserved field names for custom data
- Manually set `emailListIds` or `unsubscribedChannelIds` (managed by Iterable)
- Modify `itblInternal` fields
- Use spaces or special characters in custom field names

---

### Accessing User Fields in Templates

**Standard user profile fields:**
```handlebars
{{email}}
{{userId}}
{{firstName}}
{{lastName}}
{{phoneNumber}}
```

**Custom user data fields:**
```handlebars
{{customField}}
{{user.preferences.newsletter}}
{{user.purchase_history.[0].productName}}
```

**Nested objects:**
```handlebars
{{user.address.street}}
{{user.address.city}}
{{user.address.zipCode}}
```

---

### Example User Profile Structure

```json
{
  "email": "sarah.johnson@example.com",
  "userId": "user_12345",
  "phoneNumber": "+1234567890",
  "firstName": "Sarah",
  "lastName": "Johnson",
  "signupDate": "2024-01-15T10:30:00Z",
  "emailListIds": [101, 102, 305],
  "customFields": {
    "preferences": {
      "newsletter": true,
      "smsAlerts": false
    },
    "loyaltyTier": "Gold",
    "totalPurchases": 1250.75,
    "favoriteCategory": "Electronics"
  }
}
```

**Using in a template:**
```handlebars
<h1>Hi {{firstName}}!</h1>
<p>Your email: {{email}}</p>
<p>Loyalty Status: {{customFields.loyaltyTier}}</p>

{{#if customFields.preferences.newsletter}}
  <p>You're subscribed to our newsletter!</p>
{{/if}}

<p>Total spent: {{numberFormat customFields.totalPurchases "currency" locale}}</p>
```

---

## Universal Merge Parameters

Universal merge parameters (also called built-in merge tags) are **always available** in Iterable templates, regardless of your project's data structure. These provide access to system-level data about campaigns, recipients, and your brand.

### Unsubscribe Links

**Required for compliance:** Email marketing messages must include unsubscribe functionality to comply with CAN-SPAM laws.

**`{{unsubscribeUrl}}`**
- Unsubscribes user from the **message channel** associated with the message
- Use in: Marketing emails
```handlebars
<a href="{{unsubscribeUrl}}">Unsubscribe</a>
```

**`{{unsubscribeMessageTypeUrl}}`**
- Unsubscribes user from the **message type** associated with the message
- Use in: Specific campaign types (e.g., promotional vs. transactional)
```handlebars
<a href="{{unsubscribeMessageTypeUrl}}">Unsubscribe from promotions</a>
```

**`{{hostedUnsubscribeUrl}}`**
- Links to your **custom-hosted** unsubscribe page
- Configure in: Project Settings → Hosted Unsubscribe URL
```handlebars
<a href="{{hostedUnsubscribeUrl}}">Manage preferences</a>
```

**`{{unsubscribeByPhoneUrl}}`**
- For **international SMS** sent from alphanumeric sender IDs
- Provides web-based opt-out for users who can't text "STOP"
```handlebars
To opt out, visit: {{unsubscribeByPhoneUrl}}
```

:::tip
If **Auto-append Unsubscribe Block** is enabled in Project Settings, Iterable automatically adds `{{unsubscribeUrl}}` to marketing emails. If disabled, you must manually include one of the unsubscribe merge tags.
:::

---

### Campaign Metadata

Access information about the campaign, template, and journey:

**Campaign Information:**
```handlebars
{{campaignName}}           <!-- "Spring Sale 2024" -->
{{campaignId}}             <!-- 123456 -->
{{recurringCampaignId}}    <!-- Parent campaign ID for recurring campaigns -->
```

**Template Information:**
```handlebars
{{templateName}}           <!-- "Welcome Email" -->
{{templateId}}             <!-- 789012 -->
{{clientTemplateId}}       <!-- Custom ID set via API -->
```

**Channels & Types:**
```handlebars
{{channelId}}              <!-- Message channel ID -->
{{messageTypeId}}          <!-- Message type ID -->
```

**Journey/Workflow:**
```handlebars
{{workflowId}}             <!-- ID of the journey that triggered the send -->
```

**Audience:**
```handlebars
{{sendListIds}}            <!-- Array of list IDs campaign was sent to -->
{{join sendListIds ","}}   <!-- "101,205,308" (formatted with commas) -->
```

**Example usage:**
```handlebars
<!-- Footer metadata for tracking -->
<div class="email-footer" style="font-size: 10px; color: #999;">
  Campaign: {{campaignName}} ({{campaignId}})<br>
  Template: {{templateName}}<br>
  {{#if workflowId}}Journey ID: {{workflowId}}{{/if}}
</div>
```

---

### Your Brand Details

Access your company information configured in Project Settings:

**`{{companyName}}`**
- Your project's name (represents your brand)
- Set in: Project Settings → Name
- Auto-included in: Default unsubscribe blocks, subscription pages
```handlebars
<p>© 2024 {{companyName}}. All rights reserved.</p>
```

**`{{physicalAddress}}`**
- Your company's physical mailing address
- Set in: Project Settings → Physical Address
- **Required** in email marketing messages (CAN-SPAM compliance)
```handlebars
<p>{{companyName}}<br>{{physicalAddress}}</p>
```

**`{{brandName}}`**
- Your brand name (for SMS double opt-in)
- Set in: SMS double opt-in message type settings
- Max: 50 characters
```handlebars
<!-- SMS confirmation message -->
{{brandName}}: Reply Y to subscribe to text messages about: {{messagingInitiative}}
```

**Example footer:**
```handlebars
<div class="footer">
  <p><strong>{{companyName}}</strong></p>
  <p>{{physicalAddress}}</p>
  <p><a href="{{unsubscribeUrl}}">Unsubscribe</a> | <a href="{{hostedUnsubscribeUrl}}">Manage Preferences</a></p>
</div>
```

---

### Recipient Details

**`{{email}}`**
- Recipient's email address
```handlebars
<p>This email was sent to: {{email}}</p>
```

**`{{userId}}`**
- Recipient's user ID
```handlebars
<p>User ID: {{userId}}</p>
<!-- Or with fallback: -->
<p>User ID: {{userId|fallback="N/A"}}</p>
```

:::tip
Always include fallback values for `{{email}}` and `{{userId}}` to avoid blank spaces if the field is missing or invalid.
:::

**Example personalized tracking:**
```handlebars
<a href="https://example.com/profile?userId={{userId}}&email={{urlEncode email}}">
  View Your Profile
</a>
```

---

### Other Universal Parameters

**`{{now}}`**
- Current date/time (generated at send time)
- Default format: `MMM DD, YYYY` (e.g., "Oct 24, 2024")
- Supports formatting with `format` parameter

```handlebars
{{now}}                                    <!-- Oct 24, 2024 -->
{{now format="yyyy-MM-dd"}}                <!-- 2024-10-24 -->
{{now format="EEEE, MMMM d, yyyy"}}        <!-- Thursday, October 24, 2024 -->
{{now format="h:mm a"}}                    <!-- 3:45 PM -->
```

**`{{viewInBrowserUrl}}`**
- Link to web version of email
- Allows recipients to view email in browser
```handlebars
<p><a href="{{viewInBrowserUrl}}">View this email in your browser</a></p>
```

**`{{sendSkip}}`**
- Aborts message send and generates a "send skip" event
- Use: Conditional send logic
- Named parameters: Store additional data with the skip event

```handlebars
<!-- Skip send if user doesn't have enough credit -->
{{#ifLt creditAvailable product.price}}
  {{sendSkip cause="insufficient credit" creditAvailable=creditAvailable creditRequired=product.price}}
{{/ifLt}}
```

**`{{messagingInitiative}}`** (SMS Double Opt-In)
- Describes content of SMS subscription
- Set in: SMS double opt-in message type settings
- Max: 100 characters
```handlebars
{{brandName}}: Reply Y to subscribe to text messages about: {{messagingInitiative}}
```

**`{{smsDisclaimerLink}}`** (SMS Double Opt-In)
- URL to your terms/privacy policy
- Set in: SMS double opt-in message type settings
- Must be `https://` URL
```handlebars
{{brandName}}: Msg & data rates may apply. Reply HELP for help, STOP to cancel. 
Disclaimer: {{smsDisclaimerLink}}
```

---

### Complete Example: Professional Email Footer

```handlebars
<!DOCTYPE html>
<html>
<body>
  <!-- Email content here -->
  
  <!-- Professional footer with all key elements -->
  <div class="footer" style="margin-top: 40px; padding: 20px; background-color: #f5f5f5; font-size: 12px; color: #666;">
    
    <!-- Brand info -->
    <div style="margin-bottom: 15px;">
      <strong style="font-size: 14px;">{{companyName}}</strong><br>
      {{physicalAddress}}
    </div>
    
    <!-- Unsubscribe links -->
    <div style="margin-bottom: 15px;">
      <a href="{{unsubscribeUrl}}" style="color: #007bff;">Unsubscribe</a> | 
      <a href="{{hostedUnsubscribeUrl}}" style="color: #007bff;">Manage Preferences</a> | 
      <a href="{{viewInBrowserUrl}}" style="color: #007bff;">View in Browser</a>
    </div>
    
    <!-- Metadata for tracking -->
    <div style="font-size: 10px; color: #999;">
      Campaign: {{campaignName}} (ID: {{campaignId}})<br>
      Sent: {{now format="MMMM d, yyyy 'at' h:mm a"}} to {{email}}<br>
      {{#if workflowId}}Journey: {{workflowId}}<br>{{/if}}
      Template: {{templateName}}
    </div>
    
    <!-- Copyright -->
    <div style="margin-top: 15px;">
      © {{now format="yyyy"}} {{companyName}}. All rights reserved.
    </div>
  </div>
</body>
</html>
```

---

### Best Practices for Universal Parameters

**✅ DO:**
- Always include unsubscribe links in marketing emails
- Use `{{physicalAddress}}` in email footers (legal requirement)
- Include `{{viewInBrowserUrl}}` for better email client compatibility
- Use `{{now}}` for dynamic dates (copyright years, timestamps)
- Format URLs with `urlEncode` when passing as query parameters

**❌ DON'T:**
- Forget unsubscribe links in marketing emails (legal violation)
- Manually type unsubscribe URLs (they're unique per recipient)
- Use `{{sendSkip}}` without conditional logic (aborts all sends)
- Modify or override system-generated values

---

## Conditional Logic Helpers

### defaultIfEmpty
Display a fallback value if a field is empty.

```handlebars
{{defaultIfEmpty firstName "there"}}
```
**Output:** `Hi there` (if firstName is empty)

---

### #ifContainsStr
Display content if text is found within a string.

```handlebars
{{#ifContainsStr favoriteFoods "blueberry"}}
    It's blueberry season!
{{/ifContainsStr}}
```

---

### #if / #unless
Display content based on true/false values.

```handlebars
{{#if activeUser}}
    Welcome back!
{{else}}
    We miss you!
{{/if}}
```

```handlebars
{{#unless activeUser}}
    We miss you! Come back for 10% off.
{{/unless}}
```

---

### #and / #or / #not
Logical operations on multiple fields.

```handlebars
{{#and optedIn completedSurvey}}
    Thanks for completing the survey!
{{else}}
    Please complete our survey.
{{/and}}
```

```handlebars
{{#or likesCoffee likesTea}}
    Craving a hot drink?
{{else}}
    Check out our cold beverages!
{{/or}}
```

```handlebars
{{not isAdult yes="Sorry, adults only" no="Welcome!"}}
```

---

### #ifEq
Display content if values are equal.

```handlebars
{{#ifEq firstName preferredName}}
    Hi, {{firstName}}!
{{else}}
    Hi, {{preferredName}}!
{{/ifEq}}
```

---

### #ifGt / #ifGte
Display content if one value is greater than another.

```handlebars
{{#ifGt loyaltyPoints 100}}
    You've reached VIP status!
{{else}}
    Keep earning points for VIP status.
{{/ifGt}}
```

```handlebars
{{#ifGte age 21}}
    Check out our new French wines!
{{else}}
    Sorry, you must be 21+
{{/ifGte}}
```

---

### #ifLt / #ifLte
Display content if one value is less than another.

```handlebars
{{#ifLt purchases 5}}
    Keep shopping to reach VIP status.
{{else}}
    Welcome to VIP!
{{/ifLt}}
```

```handlebars
{{#ifLte age 17}}
    Check out this summer's coolest kids' movies!
{{else}}
    Check out our adult features.
{{/ifLte}}
```

---

### #ifModEq
Display content if the remainder of division equals a value.

```handlebars
{{#ifModEq daysSinceLastSend 30 0}}
    New month, new featured recipe!
{{/ifModEq}}
```

---

### #ifMatchesRegexStr
Display content if strings match a regular expression.

```handlebars
{{#ifMatchesRegexStr dogName "[Pp]ippin"}}
    Is your dog's name Pippin?
{{/ifMatchesRegexStr}}
```

---

## Date and Time Helpers

### dateFormat
Format dates in various ways.

**Full date (with day of week):**
```handlebars
{{dateFormat signupDate format="full"}}
```
**Output:** `Wednesday, February 12, 2025`

**Long date:**
```handlebars
{{dateFormat signupDate format="long"}}
```
**Output:** `February 12, 2025`

**Medium date:**
```handlebars
{{dateFormat signupDate format="medium"}}
```
**Output:** `Feb 12, 2025`

**Short date:**
```handlebars
{{dateFormat signupDate format="short"}}
```
**Output:** `02/12/25`

**Custom pattern:**
```handlebars
{{dateFormat signupDate "yyyy-MM-dd HH:mm:ss"}}
```
**Output:** `2025-02-12 13:45:30`

**With locale:**
```handlebars
{{dateFormat signupDate "long" "de_DE"}}
```
**Output:** `12. Februar 2025`

**With timezone:**
```handlebars
{{dateFormat signupDate tz="America/Los_Angeles"}}
```

---

### dateMath
Calculate dates relative to another date.

**Add time to a date:**
```handlebars
{{dateMath signupDate "+7d"}}
```
**Output:** Date 7 days after signup

**Subtract time from a date:**
```handlebars
{{dateMath signupDate "-1M"}}
```
**Output:** Date 1 month before signup

**Calculate from now:**
```handlebars
{{dateMath "now" "-1d"}}
```
**Output:** Yesterday's date

**Calculate age:**
```handlebars
{{dateMath birthDate (now format="-y'y'+1'y'-M'M'+1'M'-d'd'+1'd'-H'H'-m'm'-s's'" tz="UTC") format="yy"}}
```

**Calculate days away from a date:**
```handlebars
{{dateMath myDateField (now format="-y'y'+1'y'-M'M'+1'M'-d'd'+1'd'-H'H'-m'm'-s's'" tz="UTC") format="D"}}
```

---

### now / timestamp
Display current date/time.

```handlebars
{{now format="yyyy"}}
```
**Output:** `2025`

```handlebars
{{now format="EEEE"}}
```
**Output:** `Monday`

```handlebars
{{timestamp}}
```
**Output:** `1744132260210` (Unix timestamp in milliseconds)

---

### Date/Time Format Codes

| Code | Meaning | Output Examples |
|------|---------|-----------------|
| y, yy, yyyy | Year | 2025, 25 |
| M, MM, MMM, MMMM | Month | 4, 04, Apr, April |
| d, dd | Day of month | 8, 08 |
| D, DD, DDD | Day of year | 98 |
| e | Day of week (Monday = 1) | 2 |
| E, EEEE | Day of week | Tue, Tuesday |
| a | AM/PM | PM |
| w | Week of year | 27 |
| h | Clock hour (1-12) | 12 |
| H | Hour of day (0-23) | 0 |
| k | Clock hour (1-24) | 16 |
| K | Hour of halfday (0-11) | 4 |
| m | Minute of hour | 30 |
| s | Second of minute | 55 |
| S | Milliseconds | 978 |
| z | Time zone | PST |
| Z | Time zone offset | -0800 |

---

## Math Helpers

### math
Perform mathematical operations.

**Addition:**
```handlebars
{{math age '+' 2}}
```
**Output:** `32` (if age is 30)

**Subtraction:**
```handlebars
{{math age '-' 2}}
```
**Output:** `28` (if age is 30)

**Multiplication:**
```handlebars
{{math age '*' 3}}
```
**Output:** `90` (if age is 30)

**Division:**
```handlebars
{{math age '/' 2}}
```
**Output:** `15` (if age is 30)

**Modulo (remainder):**
```handlebars
{{math age '%' 17}}
```
**Output:** `1` (if age is 35)

---

### numberFormat
Format numbers in various ways.

**Currency:**
```handlebars
{{numberFormat price "currency" locale}}
```
**Output:** `$29.99` (if locale is en_US)
**Output:** `29,99 €` (if locale is fr_FR)

**Percentage:**
```handlebars
{{numberFormat completionLevel "percent"}}
```
**Output:** `23%` (if value is 0.23)

**Maximum decimal places:**
```handlebars
{{numberFormat height maximumFractionDigits=3}}
```
**Output:** `178.079` (from 178.0787402)

**Minimum decimal places:**
```handlebars
{{numberFormat height minimumFractionDigits=5}}
```
**Output:** `178.07000` (from 178.07)

**Rounding modes:**
```handlebars
{{numberFormat value "integer" roundingMode="up"}}
{{numberFormat value "integer" roundingMode="down"}}
{{numberFormat value "integer" roundingMode="ceiling"}}
{{numberFormat value "integer" roundingMode="floor"}}
{{numberFormat value "integer" roundingMode="half_up"}}
{{numberFormat value "integer" roundingMode="half_down"}}
{{numberFormat value "integer" roundingMode="half_even"}}
```

**Thousands separator:**
```handlebars
{{numberFormat annualRevenue groupingUsed=true}}
```
**Output:** `200,000,000`

**Custom pattern:**
```handlebars
{{numberFormat revenue "#,###.00"}}
```
**Output:** `1,234.57`

---

### Comparison Helpers (Numbers)

```handlebars
{{eq value1 value2 yes="Equal" no="Not equal"}}
{{gt value1 value2 yes="Greater" no="Not greater"}}
{{gte value1 value2 yes="Greater or equal" no="Less"}}
{{lt value1 value2 yes="Less" no="Not less"}}
{{lte value1 value2 yes="Less or equal" no="Greater"}}
```

---

## Encoding and Hashing Helpers

### Hashing Helpers

**SHA-256 HMAC (most secure):**
```handlebars
{{hmacSHA256 userId}}
```
**Output:** `10ec54aa2d39e7ba838e3c485b46436d70c5f577f3df20f8ba8c5e7559d568dc`

**SHA-256:**
```handlebars
{{sha256 email}}
```
**Output:** `3a86c6f084291fda367f24e885c74d2f1d50419eb4028d2b1bb2060d8f45ce0b`

**SHA-1 HMAC:**
```handlebars
{{hmacSHA1 userId}}
```
**Output:** `310cf1d869f57ad61fc46d45fc9496fa1a628162`

**SHA-1:**
```handlebars
{{sha1 email}}
```
**Output:** `63a710569261a24b3766275b7000ce8d7b32e2f7`

**MD5:**
```handlebars
{{md5 email}}
```
**Output:** `b58996c504c5638798eb6b511e6f49af`

---

### Encoding Helpers

**Base64:**
```handlebars
{{#base64}}{{email}}{{/base64}}
```
**Output:** `dXNlckBleGFtcGxlLmNvbQ==`

**JSON:**
```handlebars
{{toJson dataField}}
```
**Output:** `"value"` (for strings), `123` (for numbers), `{"key":"value"}` (for objects)

**URL-encoded JSON:**
```handlebars
{{toUrlEncodedJson dataField}}
```
**Output:** `%22value%22`

**URL encoding:**
```handlebars
{{#urlEncode}}{{favoriteFood}}{{/urlEncode}}
```
**Output:** `pi%C3%B1a+colada` (from "piña colada")

**Hex encoding:**
```handlebars
{{hexEncode email}}
```
**Output:** `75736572406578616d706c652e636f6d`

---

## Text Helpers

### String Information

**Length:**
```handlebars
{{favoriteBook.length}}
```
**Output:** `21` (for "To Kill A Mockingbird")

---

### String Comparison

**Equality:**
```handlebars
{{eq firstName preferredName}}
```
**Output:** `true` or `false`

**Alphabetical order:**
```handlebars
{{lt "apple" "zebra"}}  → true
{{gt "zebra" "apple"}}  → true
```

---

### Case Conversion

**Capitalize first word:**
```handlebars
{{capitalizeFirst text}}
```
**Output:** `Blueberry scone` (from "blueberry scone")

**Capitalize all words:**
```handlebars
{{capitalize text}}
```
**Output:** `Blueberry Scone` (from "blueberry scone")

**Uppercase:**
```handlebars
{{upper text}}
```
**Output:** `JANUARY` (from "january")

**Lowercase:**
```handlebars
{{lower text}}
```
**Output:** `fiterable` (from "FItErAbLE")

---

### String Manipulation

**Cut (remove text):**
```handlebars
{{cut breakfastOrder "pan"}}
```
**Output:** `cakes` (from "pancakes")

**Replace:**
```handlebars
{{replace text "summer" "winter"}}
```
**Output:** `The cat days of winter` (from "The cat days of summer")

**Abbreviate:**
```handlebars
{{abbreviate iceCreamFlavor 7}}
```
**Output:** `choc...` (from "chocolate")

**Center with padding:**
```handlebars
{{center greeting size=20 pad="-"}}
```
**Output:** `------Hi there------`

**Slugify:**
```handlebars
{{slugify itemName}}
```
**Output:** `lil-café-milk-frother-` (from "lil' café milk frother 2.0")

**Replace newlines:**
```handlebars
{{#breaklines}}{{text}}{{/breaklines}}
```

**Substring:**
```handlebars
{{substring brand 3 6}}
```
**Output:** `era` (from "Fiterable")

---

## HTML Rendering and Escaping

Understanding how Handlebars handles HTML is crucial for both functionality and security.

### Escaped Output (Default - Safe)

**Double curly braces** `{{}}` escape HTML by default, preventing HTML injection:

```handlebars
{{userGeneratedContent}}
```

**Input:** `<script>alert('XSS')</script>`  
**Output:** `&lt;script&gt;alert('XSS')&lt;/script&gt;` (displays as text, not executed)

**Example with user content:**
```handlebars
<div class="user-bio">
    {{userBio}}
</div>
```
If `userBio` contains `<strong>Hello</strong>`, it displays as literal text: `<strong>Hello</strong>`

---

### Unescaped Output (Render HTML)

**Triple curly braces** `{{{}}}` render HTML without escaping:

```handlebars
{{{htmlContent}}}
```

**Input:** `<strong>Hello</strong>`  
**Output:** **Hello** (renders as bold HTML)

**Example with rich content:**
```handlebars
<div class="message">
    {{{customHtmlMessage}}}
</div>
```
If `customHtmlMessage` contains `<p>Welcome! <em>New products available.</em></p>`, it renders with formatting.

---

### When to Use Each

**Use `{{}}` (escaped) for:**
- User-generated content
- Untrusted data sources
- Plain text that might contain special characters
- Default safety

**Use `{{{}}}` (unescaped) for:**
- Content you control and trust
- Pre-sanitized HTML from your CMS
- Iterable snippets
- Rich text from secure sources

---

### Snippets (Always Unescaped)

Snippets must use triple braces to render properly:

```handlebars
{{{snippet "header" title="Welcome"}}}
{{{snippet "footer" year=(now format="yyyy")}}}
```

**Wrong (will show Handlebars code):**
```handlebars
{{snippet "header"}}  ❌
```

---

### Security Best Practices

⚠️ **XSS (Cross-Site Scripting) Prevention:**

**Dangerous:**
```handlebars
<!-- NEVER do this with untrusted user input -->
<div>{{{userComment}}}</div>  ❌
```

**Safe:**
```handlebars
<!-- User input should always be escaped -->
<div>{{userComment}}</div>  ✅
```

**Safe with controlled content:**
```handlebars
<!-- Your CMS content or snippets -->
<div>{{{companyNewsletter}}}</div>  ✅
```

---

### Practical Examples

**Newsletter with HTML sections:**
```handlebars
<html>
<body>
    <h1>Hi {{firstName}}!</h1>
    
    <!-- Safe: User's name is escaped -->
    <p>Welcome back, {{firstName}} {{lastName}}!</p>
    
    <!-- Unescaped: Your controlled HTML content -->
    {{{newsletter_html_content}}}
    
    <!-- Safe: User's text feedback -->
    <div class="feedback">
        <p>Your feedback: {{userFeedback}}</p>
    </div>
    
    <!-- Unescaped: Your footer snippet -->
    {{{snippet "email_footer"}}}
</body>
</html>
```

**Conditional HTML rendering:**
```handlebars
{{#if isPremiumUser}}
    {{{premiumContentHtml}}}
{{else}}
    <p>{{standardMessage}}</p>
{{/if}}
```

**Mixed content:**
```handlebars
<div class="product-card">
    <h2>{{productName}}</h2>  <!-- Escaped: product name from database -->
    <p>{{productDescription}}</p>  <!-- Escaped: plain text description -->
    {{{productDetailsHtml}}}  <!-- Unescaped: rich HTML from your CMS -->
    <span>Price: {{numberFormat price "currency" locale}}</span>
</div>
```

---

### HTML in Data Feeds

Data feed content follows the same rules:

**Escaped (default):**
```handlebars
[[productDescription]]
```

**Unescaped:**
```handlebars
[[[productRichHtml]]]
```

**With merged context:**
```handlebars
{{{richContent}}}  <!-- Renders HTML -->
{{plainContent}}   <!-- Escapes HTML -->
```

---

### Common Mistakes

**Mistake 1: Forgetting triple braces for snippets**
```handlebars
{{snippet "header"}}  ❌ Shows raw Handlebars
{{{snippet "header"}}}  ✅ Renders snippet
```

**Mistake 2: Using triple braces for user input**
```handlebars
{{{userComment}}}  ❌ Security risk!
{{userComment}}  ✅ Safe
```

**Mistake 3: Double-escaping**
```handlebars
<!-- If field already contains escaped HTML -->
{{preEscapedContent}}  <!-- This double-escapes! -->
{{{preEscapedContent}}}  <!-- Use this if content is pre-escaped -->
```

---

### Testing HTML Output

**To test escaping:**
```handlebars
Test: {{testField}}
```
Set `testField` to: `<b>Bold</b>`
- If you see `<b>Bold</b>` → correctly escaped
- If you see **Bold** → incorrectly unescaped

**To test HTML rendering:**
```handlebars
Test: {{{testField}}}
```
Set `testField` to: `<b>Bold</b>`
- Should render as **Bold**

---

## Catalog Helpers

### #catalog
Retrieve and use catalog data.

```handlebars
{{#catalog "artists" 47 required=true as |artist|}}
    Artist name is {{artist.name}}
    Price: {{artist.price}}
{{else}}
    Artist not found
{{/catalog}}
```

**With iteration:**
```handlebars
{{#each catalogItems}}
    {{#catalog "products" this.productId as |product|}}
        <div>
            <h3>{{product.name}}</h3>
            <p>{{product.description}}</p>
            <span>${{product.price}}</span>
        </div>
    {{/catalog}}
{{/each}}
```

---

## Data Feed Helpers

Data feeds allow you to reference external data sources in your templates. Templates must be configured to accept data feeds before using these helpers.

### Basic Data Feed Syntax

**Using double square brackets (default):**
```handlebars
[[suggestedProduct.[0].price]]
[[dataFeedField]]
[[products.[0].name]]
```

**Using double curly braces (with merged context):**
```handlebars
{{suggestedProduct.[0].price}}
{{dataFeedField}}
{{products.[0].name}}
```

:::note
When using merged context (double curly braces), if a field exists in both the user profile and data feed, Iterable prioritizes the **user profile** value. This allows graceful fallback from user data to data feed data.
:::

---

### Referencing Data Feeds by Alias

When data feeds are assigned aliases, you can reference them more clearly:

**With alias:**
```handlebars
[[my_alias.suggestedProduct.[1].price]]
{{my_alias.productName}}
[[recommendations.items.[0].title]]
```

**Accessing nested data:**
```handlebars
[[product_feed.categories.[0].items.[0].price]]
{{inventory.warehouse1.stock_count}}
```

---

### Data Feed Configuration Options

**1. Cache data feed response (up to 1 hr)**
- Reduces load on external servers
- Caches responses per unique URL (after parameter substitution)
- Reuses cached data if available within past hour

**2. Merge the data feed and user contexts**
- Allows use of `{{}}` syntax instead of `[[]]`
- Enables fallback: prefers user profile data, falls back to data feed
- Simplifies template syntax when using multiple data sources

**3. Use data feed alias**
- Reference data feeds by custom names
- Makes templates more readable
- Easier to maintain when using multiple feeds

---

### Data Feed Examples

**Product recommendation:**
```handlebars
{{#each [[recommendedProducts]]}}
    <div class="product">
        <h2>[[this.name]]</h2>
        <p>[[this.description]]</p>
        <span class="price">{{numberFormat [[this.price]] "currency" locale}}</span>
        <img src="[[this.imageUrl]]" alt="[[this.name]]">
    </div>
{{/each}}
```

**With merged context:**
```handlebars
{{#each recommendedProducts}}
    <div class="product">
        <h2>{{this.name}}</h2>
        <p>{{this.description}}</p>
        <span class="price">{{numberFormat this.price "currency" locale}}</span>
    </div>
{{/each}}
```

**Using alias:**
```handlebars
{{#each [[products.items]]}}
    <div>
        <h3>[[products.items.[this.@index].title]]</h3>
        <p>Price: $[[products.items.[this.@index].price]]</p>
    </div>
{{/each}}
```

**Conditional data feed content:**
```handlebars
{{#if [[productAvailable]]}}
    <p>{{[[productName]]}} is in stock!</p>
    <p>Price: {{numberFormat [[productPrice]] "currency" locale}}</p>
{{else}}
    <p>This product is currently unavailable.</p>
{{/if}}
```

**Fallback to data feed when user data missing:**
```handlebars
<!-- With merged context enabled -->
<h1>Hi {{firstName}}!</h1>  <!-- Uses user.firstName if available -->
<p>Check out: {{productName}}</p>  <!-- Falls back to dataFeed.productName if user.productName doesn't exist -->
```

---

## Snippets

Snippets are reusable content blocks that you can add to templates to manage frequently used elements, store complex logic, and update multiple templates simultaneously.

### What Are Snippets?

Snippets allow you to:
- Create reusable content blocks (headers, footers, banners)
- Store complex Handlebars logic in one place
- Update multiple templates at once by editing the snippet
- Pass custom variables to make snippets dynamic

---

### Snippet Components

**1. Name**
- Unique identifier for the snippet
- Cannot be changed after creation
- Can include letters, numbers, spaces, dashes, underscores

**2. Content**
- HTML (including images via URLs)
- Inline CSS (using `style` attribute)
- Handlebars expressions and logic
- Data feeds (template must be configured)

**Cannot include:**
- `<script>` tags
- `<iframe>` tags
- JavaScript
- Self-references

**3. Variables (Parameters)**
- Dynamic fields that templates can customize
- Make snippets reusable with different values
- Templates must provide values for all variables

**4. Snippet Expression**
- Handlebars code used to include the snippet in templates
- Generated automatically by Iterable

---

### Basic Snippet Syntax

**Simple snippet (no variables):**
```handlebars
{{{snippet "footer"}}}
{{{snippet "email_header"}}}
```

**Snippet with variables:**
```handlebars
{{{snippet "promo_banner" discount=20 ctaText="Shop Now"}}}
{{{snippet "header" title="Welcome" userName=firstName}}}
```

**Snippet with Handlebars helpers:**
```handlebars
{{{snippet "footer" year=(now format="yyyy")}}}
{{{snippet "greeting" name=(upper firstName)}}}
```

⚠️ **Always use triple braces** `{{{}}}` for snippets to render HTML correctly!

---

### Snippet Variables

Variables allow templates to pass custom values into a snippet.

**Defining variables in snippet:**

When creating a snippet, you define variables that templates must provide.

**Using variables in snippet content:**
```handlebars
<!-- Inside the "promo_banner" snippet -->
<div class="banner" style="background-color: {{backgroundColor}};">
    <h2>Save {{discount}}%!</h2>
    <p>{{promoMessage}}</p>
    <button>{{ctaText}}</button>
</div>
```

**Calling snippet from template:**
```handlebars
{{{snippet "promo_banner" 
    discount=25 
    ctaText="Shop Sale" 
    promoMessage="Limited time offer!"
    backgroundColor="#FF5733"
}}}
```

**Output:**
```html
<div class="banner" style="background-color: #FF5733;">
    <h2>Save 25%!</h2>
    <p>Limited time offer!</p>
    <button>Shop Sale</button>
</div>
```

---

### Variable Syntax

**Passing literal values:**
```handlebars
{{{snippet "card" title="Welcome" count=5 active=true}}}
```

**Passing user data:**
```handlebars
{{{snippet "greeting" userName=firstName userEmail=email}}}
```

**Passing expressions:**
```handlebars
{{{snippet "badge" 
    points=loyaltyPoints 
    level=(ifGt loyaltyPoints 100 "Gold" "Silver")
}}}
```

**Passing nested fields:**
```handlebars
{{{snippet "address" 
    street=shippingAddress.street 
    city=shippingAddress.city
}}}
```

---

### Common Snippet Patterns

**Email Header Snippet:**

**Snippet definition (`email_header`):**
```handlebars
<table width="100%" cellpadding="0" cellspacing="0">
    <tr>
        <td style="background-color: {{headerColor}}; padding: 20px;">
            <img src="{{logoUrl}}" alt="{{companyName}}" style="height: 50px;">
            <h1 style="color: white;">{{headerTitle}}</h1>
        </td>
    </tr>
</table>
```

**Template usage:**
```handlebars
{{{snippet "email_header" 
    logoUrl="https://example.com/logo.png"
    headerTitle="Weekly Newsletter"
    headerColor="#4A90E2"
    companyName="Acme Corp"
}}}
```

---

**Conditional Promo Banner:**

**Snippet definition (`promo_banner`):**
```handlebars
{{#if showBanner}}
    <div style="background: {{bannerColor}}; padding: 15px; text-align: center;">
        {{#ifGt discountAmount 0}}
            <h2>Save {{discountAmount}}% Today!</h2>
        {{else}}
            <h2>{{bannerMessage}}</h2>
        {{/ifGt}}
        <a href="{{ctaUrl}}" style="background: white; padding: 10px 20px; text-decoration: none;">
            {{ctaText}}
        </a>
    </div>
{{/if}}
```

**Template usage:**
```handlebars
{{{snippet "promo_banner"
    showBanner=isPremiumUser
    bannerColor="#E74C3C"
    discountAmount=userDiscount
    bannerMessage="Exclusive Offer Inside"
    ctaText="Claim Now"
    ctaUrl=promoUrl
}}}
```

---

**Product Card Snippet:**

**Snippet definition (`product_card`):**
```handlebars
<div class="product-card" style="border: 1px solid #ddd; padding: 20px; margin: 10px;">
    <img src="{{productImage}}" alt="{{productName}}" style="width: 100%; max-width: 300px;">
    <h3>{{productName}}</h3>
    <p>{{productDescription}}</p>
    <div class="price">
        {{#ifGt originalPrice price}}
            <span style="text-decoration: line-through; color: #999;">
                {{numberFormat originalPrice "currency" locale}}
            </span>
            <span style="color: #E74C3C; font-weight: bold;">
                {{numberFormat price "currency" locale}}
            </span>
        {{else}}
            <span>{{numberFormat price "currency" locale}}</span>
        {{/ifGt}}
    </div>
    <a href="{{productUrl}}" style="background: {{buttonColor}}; color: white; padding: 10px 20px;">
        {{buttonText}}
    </a>
</div>
```

**Template usage:**
```handlebars
{{{snippet "product_card"
    productImage=featuredProduct.imageUrl
    productName=featuredProduct.name
    productDescription=featuredProduct.description
    price=featuredProduct.currentPrice
    originalPrice=featuredProduct.msrp
    productUrl=featuredProduct.url
    buttonColor="#4A90E2"
    buttonText="Buy Now"
    locale="en-US"
}}}
```

---

**Footer with Dynamic Year:**

**Snippet definition (`email_footer`):**
```handlebars
<table width="100%" cellpadding="0" cellspacing="0" style="background-color: #f8f8f8; padding: 30px;">
    <tr>
        <td style="text-align: center;">
            <p style="color: #666;">{{companyName}} | {{companyAddress}}</p>
            <p style="color: #666;">© {{currentYear}} All rights reserved.</p>
            {{#if includeUnsubscribe}}
                <a href="{{unsubscribeUrl}}" style="color: #666;">Unsubscribe</a>
            {{/if}}
            {{#if includeSocial}}
                <div style="margin-top: 15px;">
                    <a href="{{facebookUrl}}"><img src="https://example.com/fb-icon.png" alt="Facebook"></a>
                    <a href="{{twitterUrl}}"><img src="https://example.com/tw-icon.png" alt="Twitter"></a>
                </div>
            {{/if}}
        </td>
    </tr>
</table>
```

**Template usage:**
```handlebars
{{{snippet "email_footer"
    companyName="Acme Corporation"
    companyAddress="123 Main St, San Francisco, CA 94105"
    currentYear=(now format="yyyy")
    includeUnsubscribe=true
    unsubscribeUrl=unsubscribeUrl
    includeSocial=true
    facebookUrl="https://facebook.com/acme"
    twitterUrl="https://twitter.com/acme"
}}}
```

---

### Snippets with Data Feeds

Snippets can reference data feeds, but the template must be configured to accept them.

**Snippet definition (`data_feed_recommendations`):**
```handlebars
<div class="recommendations">
    <h2>{{sectionTitle}}</h2>
    {{#each [[recommendedProducts]]}}
        <div class="product">
            <img src="[[this.imageUrl]]" alt="[[this.name]]">
            <h3>[[this.name]]</h3>
            <p>{{numberFormat [[this.price]] "currency" locale}}</p>
        </div>
    {{/each}}
</div>
```

**Template usage:**
```handlebars
{{{snippet "data_feed_recommendations"
    sectionTitle="You Might Also Like"
    locale="en-US"
}}}
```

---

### Nested Snippets

You can call snippets from within other snippets:

**Snippet definition (`social_links`):**
```handlebars
<div class="social">
    <a href="{{fbUrl}}"><img src="{{fbIcon}}"></a>
    <a href="{{twUrl}}"><img src="{{twIcon}}"></a>
</div>
```

**Snippet definition (`full_footer`):**
```handlebars
<footer>
    <p>{{companyInfo}}</p>
    {{{snippet "social_links" 
        fbUrl=facebookUrl 
        twUrl=twitterUrl
        fbIcon="https://example.com/fb.png"
        twIcon="https://example.com/tw.png"
    }}}
</footer>
```

**Template usage:**
```handlebars
{{{snippet "full_footer"
    companyInfo="© 2025 Acme Corp"
    facebookUrl="https://facebook.com/acme"
    twitterUrl="https://twitter.com/acme"
}}}
```

---

### Best Practices

**1. Use descriptive variable names**
```handlebars
<!-- Good -->
{{{snippet "cta_button" buttonText="Shop Now" buttonColor="#FF5733"}}}

<!-- Bad -->
{{{snippet "cta_button" t="Shop Now" c="#FF5733"}}}
```

**2. Provide all required variables**
```handlebars
<!-- If snippet expects: title, subtitle, buttonText -->
{{{snippet "banner" title="Sale" subtitle="Up to 50% off" buttonText="Shop"}}}
```

**3. Test before editing production snippets**
- Save snippet as a copy
- Test the copy thoroughly
- Update the original only after verification

**4. Use consistent naming conventions**
```handlebars
{{{snippet "email_header"}}}
{{{snippet "email_footer"}}}
{{{snippet "promo_banner"}}}
```

**5. Document complex snippets**
Add descriptions to help your team understand snippet purpose and variables.

---

### Common Mistakes

**Mistake 1: Using double braces instead of triple**
```handlebars
{{snippet "footer"}}  ❌ Shows raw Handlebars code
{{{snippet "footer"}}}  ✅ Renders snippet content
```

**Mistake 2: Missing required variables**
```handlebars
<!-- Snippet expects: title, message, ctaText -->
{{{snippet "alert" title="Warning"}}}  ❌ Missing variables
{{{snippet "alert" title="Warning" message="Check email" ctaText="OK"}}}  ✅
```

**Mistake 3: Incorrect variable syntax**
```handlebars
{{{snippet "card" "title"="Hello"}}}  ❌ Quotes on variable name
{{{snippet "card" title="Hello"}}}  ✅ Correct
```

**Mistake 4: Forgetting snippet names are immutable**
Once created, snippet names cannot be changed. Choose wisely!

---

### Troubleshooting

**Snippet not rendering:**
- Verify triple braces: `{{{}}}` not `{{}}`
- Check all required variables are provided
- Ensure snippet name is spelled correctly

**HTML not rendering:**
- Must use triple braces `{{{}}}` for snippets
- Check snippet content for syntax errors

**Data feed not working:**
- Verify template is configured to accept data feeds
- Check data feed syntax: `[[]]` inside snippet

**Variables not working:**
- Ensure variable names match exactly (case-sensitive)
- Verify variables are defined in snippet
- Check you're passing the correct values

---

## Advanced Helpers

### #each
Loop over arrays and objects.

```handlebars
{{#each shoppingCartItems}}
    <li>{{this.name}} - ${{this.price}}</li>
{{/each}}
```

**With index:**
```handlebars
{{#each items}}
    {{@index}}: {{this.name}}
{{/each}}
```

**With key (for objects):**
```handlebars
{{#each userProfile}}
    {{@key}}: {{this}}
{{/each}}
```

---

### lookup
Dynamically access object properties.

```handlebars
{{lookup userData fieldName}}
```

**Example with array index:**
```handlebars
{{lookup shoppingCartItems 0}}
```

---

### assign
Create temporary variables using block syntax.

```handlebars
{{#assign "fullName"}}{{concat firstName " " lastName}}{{/assign}}
Hello, {{fullName}}!
```

---

### #snippet
Reuse template code (requires snippet creation in Iterable).

```handlebars
{{{snippet "header" title="Welcome"}}}
```

---

### #metadata
Access user metadata.

```handlebars
{{#metadata "users" userId as |user|}}
    Email: {{user.email}}
    Name: {{user.firstName}} {{user.lastName}}
{{/metadata}}
```

---

## Array Helpers

### maxInList
Find maximum value in an array.

```handlebars
{{maxInList prices}}
```

---

### minInList
Find minimum value in an array.

```handlebars
{{minInList prices}}
```

---

### inLeftNotInRight
Find items in one array but not another.

```handlebars
{{inLeftNotInRight array1 array2}}
```

---

## Special URL Helpers

### smartOptInUrl
Generate smart opt-in URL.

```handlebars
{{smartOptInUrl}}
```

---

### unsubscribeByPhoneUrl
Generate unsubscribe by phone URL.

```handlebars
{{unsubscribeByPhoneUrl}}
```

---

## Handlebars Syntax Validation

Understanding correct Handlebars syntax is essential for creating templates that render properly.

### Valid Syntax Rules

**1. Block Helpers Must Be Closed**
```handlebars
✅ CORRECT:
{{#if firstName}}
    Hello {{firstName}}!
{{/if}}

❌ INCORRECT (missing closing tag):
{{#if firstName}}
    Hello {{firstName}}!
```

**2. Opening and Closing Tags Must Match**
```handlebars
✅ CORRECT:
{{#each items}}
    {{this.name}}
{{/each}}

❌ INCORRECT (wrong closing tag):
{{#each items}}
    {{this.name}}
{{/if}}
```

**3. Nested Blocks Must Be Properly Nested**
```handlebars
✅ CORRECT:
{{#if isActive}}
    {{#each items}}
        {{this.name}}
    {{/each}}
{{/if}}

❌ INCORRECT (improper nesting):
{{#if isActive}}
    {{#each items}}
        {{this.name}}
{{/if}}
    {{/each}}
```

**4. Helper Arguments Must Be Properly Formatted**
```handlebars
✅ CORRECT:
{{dateFormat signupDate "yyyy-MM-dd"}}
{{math age '+' 5}}
{{#ifGt loyaltyPoints 100}}

❌ INCORRECT (missing quotes, wrong quotes):
{{dateFormat signupDate yyyy-MM-dd}}
{{math age + 5}}
{{#ifGt loyaltyPoints "100"}}  <!-- Numbers shouldn't be quoted in comparisons -->
```

**5. Snippet Syntax Must Use Triple Braces**
```handlebars
✅ CORRECT:
{{{snippet "header" title="Welcome"}}}

❌ INCORRECT (double braces):
{{snippet "header" title="Welcome"}}
```

**6. Field Names with Spaces Need Brackets**
```handlebars
✅ CORRECT:
{{[First Name]}}
{{[User Address]}}

❌ INCORRECT:
{{First Name}}
{{User Address}}
```

**7. Array Access Requires Bracket Notation**
```handlebars
✅ CORRECT:
{{items.[0].name}}
{{shoppingCart.[2].price}}

❌ INCORRECT:
{{items.0.name}}
{{shoppingCart.2.price}}
```

**8. Named Parameters Use `=` Not `:`**
```handlebars
✅ CORRECT:
{{numberFormat price maximumFractionDigits=2}}
{{dateFormat date format="yyyy-MM-dd" tz="America/Los_Angeles"}}

❌ INCORRECT:
{{numberFormat price maximumFractionDigits:2}}
{{dateFormat date format:"yyyy-MM-dd"}}
```

**9. Comments Must Be HTML Comments**
```handlebars
✅ CORRECT:
<!--{{#if firstName}}-->
    Hello {{firstName}}!
<!--{{/if}}-->

❌ INCORRECT:
//{{#if firstName}}
    Hello {{firstName}}!
//{{/if}}
```

**10. Whitespace Control Uses Tildes**
```handlebars
✅ CORRECT:
{{~#if condition~}}text{{~/if~}}

❌ INCORRECT:
{{ #if condition }}text{{ /if }}  <!-- Extra spaces don't control whitespace -->
```

---

### Common Syntax Errors

**Error: "Expecting 'CLOSE_RAW_BLOCK', got 'EOF'"**
- **Cause:** Missing closing tag
- **Fix:** Add the missing `{{/if}}`, `{{/each}}`, or `{{/unless}}`

**Error: "Parse error on line X: ... Expecting 'ID'"**
- **Cause:** Invalid helper name or field name
- **Fix:** Check for typos, ensure field names don't start with numbers

**Error: "Template execution failed"**
- **Cause:** Runtime error (missing field, invalid operation)
- **Fix:** Add fallbacks with `defaultIfEmpty` or conditional checks

**Error: "Missing helper"**
- **Cause:** Typo in helper name
- **Fix:** Check helper name spelling: `ifGt` not `ifGreaterThan`

**Error: "The partial X could not be found"**
- **Cause:** Snippet doesn't exist or name is misspelled
- **Fix:** Verify snippet name in Iterable UI

---

### Validation Checklist

Before saving a template, verify:
- [ ] All block helpers (`{{#if}}`, `{{#each}}`, etc.) have closing tags
- [ ] Nested blocks are properly nested
- [ ] All strings in helper arguments are quoted
- [ ] Numbers in comparisons are NOT quoted
- [ ] Snippets use triple braces `{{{}}}` not double `{{}}`
- [ ] Field names with spaces use brackets: `{{[Field Name]}}`
- [ ] Array indices use bracket notation: `{{array.[0]}}`
- [ ] No undefined helpers (check spelling)
- [ ] Data feeds use correct syntax: `[[]]` or `{{}}` based on merge settings

---

## Best Practices

### 1. Handle Missing Data
Always provide fallbacks for potentially missing fields:
```handlebars
{{defaultIfEmpty firstName "Valued Customer"}}
```

### 2. Use Block Helpers for Complex Logic
```handlebars
{{#ifGte loyaltyPoints 100}}
    {{#catalog "vipProducts" recommendedId as |product|}}
        Exclusive: {{product.name}}
    {{/catalog}}
{{else}}
    Shop more to unlock VIP products!
{{/ifGte}}
```

### 3. Combine Helpers
```handlebars
{{upper (substring (slugify productName) 0 10)}}
```

### 4. Date Comparisons
Format dates as numbers for comparisons:
```handlebars
{{#ifGte (dateFormat trialEndDate "yyyyMMdd") (dateFormat "now" "yyyyMMdd")}}
    Your trial is still active!
{{else}}
    Your trial has ended.
{{/ifGte}}
```

### 5. Secure User Data in URLs
```handlebars
https://example.com/preferences?user={{hmacSHA256 email}}
```

### 6. Dynamic Catalog Access
```handlebars
{{#each recommendedProducts}}
    {{#catalog "products" this.id as |product|}}
        <div class="product">
            <h2>{{product.name}}</h2>
            <p>{{product.description}}</p>
            <span class="price">{{numberFormat product.price "currency" locale}}</span>
        </div>
    {{/catalog}}
{{/each}}
```

### 7. Test Edge Cases
Always test templates with:
- Users with missing fields
- Users with all fields populated
- Users with special characters in fields
- Users with empty arrays or objects

### 8. Use Template Preview
- Preview with real user data before sending
- Test multiple user profiles
- Check for render errors in preview mode

### 9. Add Fallbacks for Critical Content
```handlebars
{{#if promotionalOffer}}
    Get {{promotionalOffer.discount}}% off!
{{else}}
    Check out our latest products!
{{/if}}
```

### 10. Keep Templates Maintainable
- Use snippets for repeated content
- Document complex logic with HTML comments
- Use descriptive field names
- Break complex templates into smaller snippets

---

## Common Patterns

### Personalized Greeting with Fallback
```handlebars
Hi {{defaultIfEmpty firstName "there"}}!
```

### Conditional VIP Content
```handlebars
{{#ifGte loyaltyPoints 1000}}
    🌟 VIP Member Benefits:
    {{#catalog "vipOffers" featuredOfferId as |offer|}}
        - {{offer.title}}: {{offer.discount}}% off
    {{/catalog}}
{{else}}
    Earn {{math 1000 '-' loyaltyPoints}} more points for VIP status!
{{/ifGte}}
```

### Dynamic Expiration Date
```handlebars
Your offer expires on {{dateFormat (dateMath "now" "+7d") "MMMM d, yyyy"}}
```

### Price Formatting by Locale
```handlebars
{{#each cartItems}}
    {{this.name}}: {{numberFormat this.price "currency" ../userLocale}}
{{/each}}
```

### Secure Profile Link
```handlebars
<a href="https://example.com/profile?id={{hmacSHA256 userId}}">
    View Your Profile
</a>
```

### Birthday Message
```handlebars
{{#ifEq (dateFormat birthDate "MM-dd") (now format="MM-dd")}}
    🎉 Happy Birthday, {{firstName}}! Enjoy 25% off today!
{{/ifEq}}
```

### Product Recommendation Loop
```handlebars
{{#each recommendedProducts}}
    {{#catalog "products" this as |product|}}
        <div>
            <img src="{{product.imageUrl}}" alt="{{product.name}}">
            <h3>{{product.name}}</h3>
            <p>{{abbreviate product.description 100}}</p>
            <span>{{numberFormat product.price "currency" locale}}</span>
        </div>
    {{/catalog}}
{{/each}}
```

### Data Feed Product Recommendations
```handlebars
<!-- Using data feed with merged context -->
{{#each recommendedProducts}}
    <div class="recommendation">
        <img src="{{this.image}}" alt="{{this.title}}">
        <h3>{{this.title}}</h3>
        <p>{{this.description}}</p>
        <div class="pricing">
            {{#ifGt this.discount 0}}
                <span class="original">{{numberFormat this.originalPrice "currency" locale}}</span>
                <span class="sale">{{numberFormat this.price "currency" locale}}</span>
                <span class="badge">{{this.discount}}% OFF</span>
            {{else}}
                <span>{{numberFormat this.price "currency" locale}}</span>
            {{/ifGt}}
        </div>
        <a href="{{this.url}}">Shop Now</a>
    </div>
{{/each}}
```

### Data Feed with User Profile Fallback
```handlebars
<!-- With merged context: uses user data if available, falls back to data feed -->
<div class="personalized-banner">
    <h1>Welcome back, {{firstName}}!</h1>
    <p>Your recommended category: {{favoriteCategory}}</p>

    <!-- If user.favoriteCategory doesn't exist, uses dataFeed.favoriteCategory -->
    {{#each [[categoryProducts]]}}
        <div class="product-card">
            <h3>[[this.name]]</h3>
            <span>{{numberFormat [[this.price]] "currency" userLocale}}</span>
        </div>
    {{/each}}
</div>
```

### Rich HTML Content with Safety
```handlebars
<div class="email-content">
    <!-- Safe: User profile data -->
    <h1>Hi {{firstName}} {{lastName}}!</h1>
    
    <!-- Safe: Plain text that might have special chars -->
    <p>Your message: {{userMessage}}</p>
    
    <!-- Unsafe: Renders HTML from trusted source -->
    <div class="newsletter">
        {{{companyNewsletter}}}
    </div>
    
    <!-- Safe: Catalog data -->
    {{#catalog "products" productId}}
        <h2>{{this.name}}</h2>
        <p>{{this.description}}</p>
        {{{this.richDescription}}}  <!-- Only if you control this field -->
    {{/catalog}}
</div>
```

---

## Troubleshooting

### Tips and Best Practices

#### How Iterable Prioritizes Data Sources

**Event Data vs. User Profile:**
- If a field exists in **both** the user profile and triggering event, Iterable uses the value from the **event**

**Data Feed vs. User Profile** (when "Merge contexts" is enabled):
- If a field exists in **both** the user profile and data feed, Iterable uses the value from the **user profile**
- This allows graceful fallback: prioritize user data, fall back to data feed

**Priority Order:**
1. Triggering event data (highest priority)
2. User profile data
3. Data feed data (lowest priority)

```handlebars
<!-- If "firstName" exists in event, profile, and data feed -->
{{firstName}}  <!-- Uses event data first -->
```

---

#### Don't Name Fields with Periods

**❌ Bad:**
```json
{
  "user.name": "John",
  "product.price": 29.99
}
```

**✅ Good:**
```json
{
  "userName": "John",
  "productPrice": 29.99
}
```

**Why?** Handlebars uses periods for object notation (`{{object.field}}`), so field names with periods cause errors.

See: [Best Practices for Field Names and Event Names](https://support.iterable.com/hc/articles/26821896236948)

---

#### Using Handlebars in WYSIWYG Editor

When using the WYSIWYG editor, **comment out Handlebars logic lines** that don't output values:

**Before:**
```handlebars
{{#if firstName}}
    <div>Hey, {{firstName}}!</div>
{{else}}
    <div>Hey there!</div>
{{/if}}
```

**After:**
```handlebars
<!--{{#if firstName}}-->
    <div>Hey, {{firstName}}!</div>
<!--{{else}}-->
    <div>Hey there!</div>
<!--{{/if}}-->
```

**⚠️ IMPORTANT:** Only comment out logic lines (`{{#if}}`, `{{else}}`, `{{/if}}`), NOT value lines (`{{firstName}}`).

---

#### Removing Whitespace

Whitespace (spaces, tabs, newlines) is preserved by default. This can break URLs and deep links!

**Use tildes (`~`) to trim whitespace:**

| Action | Syntax |
|--------|--------|
| Remove leading whitespace | `{{~tag}}` |
| Remove trailing whitespace | `{{tag~}}` |
| Remove both | `{{~tag~}}` |

**Example (Deep Link):**
```handlebars
{{~#if isSummerCampaign~}}https://example.com/summer-offer?user={{~userId~}}
{{~else~}}https://example.com/winter-offer?user={{~userId~}}
{{~/if~}}
```

---

### Common Issues

#### Can't Save Template Due to Handlebars Error

**Problem:** Missing closing statements in Handlebars blocks.

**Example of Problem:**
```handlebars
{{#if isActiveUser}}
    {{#if madePurchase}}
        <div>Thank you for your purchase!</div>
{{/if}}
```

**Solution:** Add all closing statements:
```handlebars
{{#if isActiveUser}}
    {{#if madePurchase}}
        <div>Thank you for your purchase!</div>
    {{/if}}
{{/if}}
```

Every opening statement (`{{#if}}`, `{{#each}}`, `{{#unless}}`) needs a closing statement.

---

#### Handlebars Code Renders as HTML

**Problem:** Template editor shows Handlebars code instead of executing it.

**Solution:** Comment out logic lines:
```handlebars
<!--{{#if firstName}}-->
    Hi, {{firstName}}!
<!--{{/if}}-->
```

**Don't** comment out value lines like `{{firstName}}` - only structural lines.

---

#### Unexpected Characters in Rendered Content

**Problem:** Apostrophes render as `&#x27;`, quotes as `&quot;`, etc.

**Example:**
```handlebars
{{productName}}
Input: "Jenna's Elixir"
Output: "Jenna&#x27;s Elixir"
```

**Solution:** Use triple braces to prevent HTML encoding:
```handlebars
{{{productName}}}
Output: "Jenna's Elixir"
```

**⚠️ Security Warning:** Only use `{{{}}}` for trusted content you control!

---

#### Data Feed Doesn't Render

**Problem:** Data feed values not showing in template.

**Check Settings:**
- Is "Merge the Data Feed and User Contexts" enabled?
  - **Enabled:** Use `{{}}` or `[[]]`
  - **Disabled:** Must use `[[]]`

**Example (Merged Context Disabled):**
```handlebars
<!--Correct:-->
[[#if firstName]] 
    Hi [[firstName]],
{{else}} 
    Hi friend,
[[/if]]

<!--Wrong - won't work:-->
{{#if firstName}}
    Hi {{firstName}},
{{/if}}
```

---

#### Blank or Missing Values

**Problem:** Referenced values don't appear in template.

**Cause:** Field names with spaces need square brackets.

**Example:**
```handlebars
<!--Wrong:-->
{{First Name}}

<!--Correct:-->
{{[First Name]}}
```

**Better:** Don't use spaces in field names. Use `firstName` instead.

---

#### Template Fails to Render

**Causes:**
- Missing required fields
- Field is null or undefined
- Comparison operators (`gt`, `lt`, etc.) fail when fields are missing

**Solutions:**
- Use `defaultIfEmpty` for fallback values
- Use conditional blocks to check if field exists
- Verify catalog items exist before referencing
- Check that comparison fields are same data type (long/double)

```handlebars
<!--Good:-->
{{defaultIfEmpty firstName "there"}}

<!--Good:-->
{{#if firstName}}
    Hi {{firstName}}!
{{else}}
    Hi there!
{{/if}}
```

---

#### Incorrect Date Display

**Problems:**
- Wrong timezone
- Wrong format
- Date not calculating correctly

**Solutions:**
- Ensure date format matches input format
- Check timezone settings in `dateFormat`
- Verify ISO 8601 format for `dateMath`
- Use `now` for current date/time

```handlebars
<!--Correct date formatting:-->
{{dateFormat lastPurchaseDate format="yyyy-MM-dd" timezone="America/Los_Angeles"}}

<!--Correct date math:-->
{{dateMath (now) "add" "7" "days" format="yyyy-MM-dd"}}
```

---

#### Catalog Items Not Showing

**Causes:**
- Wrong catalog name
- Item ID doesn't exist
- Missing `required` parameter

**Solutions:**
```handlebars
<!--Check with conditional:-->
{{#catalog "products" productId}}
    {{this.name}}
{{else}}
    Product not found
{{/catalog}}

<!--Or use required parameter:-->
{{#catalog "products" productId required=false}}
    {{#if this}}
        {{this.name}}
    {{else}}
        Product not available
    {{/if}}
{{/catalog}}
```

---

#### Math Operations Not Working

**Causes:**
- Fields are different data types
- Null or empty values
- Wrong rounding mode

**Solutions:**
- Ensure fields are same type (long or double)
- Check for null values first
- Use appropriate rounding: `up`, `down`, `half_even`, etc.

```handlebars
<!--Check before math:-->
{{#if totalSpent}}
    {{math totalSpent '+' 10}}
{{else}}
    0
{{/if}}
```

---

#### Data Feed Not Working

**Checklist:**
- ✅ Template configured to accept data feeds
- ✅ Data feed URL returns valid JSON
- ✅ Cache settings appropriate
- ✅ Alias defined if using aliased references
- ✅ Correct bracket syntax: `[[]]` for data feeds, `{{}}` for user data
- ✅ Test data feed URL with actual user parameters

**Debug:**
```handlebars
<!--Test if data feed is loading:-->
{{#if [[feedData]]}}
    Data loaded!
    [[feedData.someField]]
{{else}}
    Data feed not available
{{/if}}
```

---

#### HTML Not Rendering

**Snippets showing Handlebars code:**
```handlebars
<!--Wrong:-->
{{snippet "header"}}

<!--Correct:-->
{{{snippet "header"}}}
```

**HTML showing as text:**
```handlebars
<!--Wrong (escapes HTML):-->
{{htmlContent}}
Output: &lt;b&gt;Bold&lt;/b&gt;

<!--Correct (renders HTML):-->
{{{htmlContent}}}
Output: <b>Bold</b>
```

**Data feeds:**
```handlebars
<!--Escaped (shows HTML as text):-->
[[htmlField]]

<!--Unescaped (renders HTML):-->
[[[htmlField]]]
```

---

#### Whitespace Breaking URLs

**Problem:**
```handlebars
{{#if isSummer}}
    https://example.com/summer
{{else}}
    https://example.com/winter
{{/if}}
```

**Result:** URL has newlines/spaces, breaks the link.

**Solution:** Use tildes:
```handlebars
{{~#if isSummer~}}https://example.com/summer{{~else~}}https://example.com/winter{{~/if~}}
```

---

#### Journey Split Not Working

**Causes:**
- Event field missing from triggering event
- Field name mismatch (case-sensitive)
- Data type mismatch

**Solutions:**
- Verify event includes field: `{{minimumOrderFromEvent}}`
- Check exact spelling and case
- Test with known user data first

---

#### Dynamic List Not Filtering

**Causes:**
- List not saved as "Dynamic" type
- Field name doesn't match event field
- Event doesn't include the field

**Solutions:**
- Ensure list is "Dynamic" not "Static"
- Check field names match exactly (case-sensitive)
- Verify event contains all required fields

---

### Security Concerns

#### XSS (Cross-Site Scripting) Prevention

**⚠️ Never use triple braces with user input:**
```handlebars
<!--DANGEROUS - allows script injection:-->
{{{userComment}}}

<!--SAFE - escapes malicious code:-->
{{userComment}}
```

**Test with malicious input:**
```
<script>alert('XSS')</script>
```

#### When to Use Each

**Use `{{}}` (escaped) for:**
- User-generated content
- Comments, reviews, feedback
- Names, emails, addresses
- Any untrusted data

**Use `{{{}}}` (unescaped) for:**
- Your own HTML content
- Snippets
- Trusted CMS content
- Pre-sanitized data

---

### HandlebarsExecutionError

When a template fails due to Handlebars errors, a `HandlebarsExecutionError` send skip event is logged on the user's profile.

**Common causes:**
- Comparison operators with null/missing fields
- Invalid catalog item IDs
- Math operations on wrong data types
- Missing required fields in blocks

**Prevention:**
- Use `defaultIfEmpty` for critical fields
- Add `{{else}}` blocks for fallbacks
- Check field existence before operations
- Use `required=false` in catalog helpers

---

### Debugging Tips

**1. Test with Preview:**
- Use template preview with real user data
- Test edge cases (missing fields, null values)
- Check multiple users with different data

**2. Simplify:**
- Comment out sections to isolate issues
- Test Handlebars expressions one at a time
- Remove complex nested logic temporarily

**3. Check Data Types:**
- Verify field types match expectations
- Use console logs (if available) to inspect values
- Test math operations with known values

**4. Review Documentation:**
- Check helper syntax in reference docs
- Verify parameter order and requirements
- Look for similar examples

**5. Use Fallbacks:**
```handlebars
{{defaultIfEmpty field "fallback"}}

{{#if field}}
    {{field}}
{{else}}
    Default value
{{/if}}
```

---

## Additional Resources

- [Iterable Handlebars Documentation](https://support.iterable.com/hc/en-us/sections/35601631605780-Handlebars)
- [Iterable Academy - Handlebars 101](https://academy.iterable.com/handlebars-101)
- [Iterable Academy - Advanced Handlebars](https://academy.iterable.com/advanced-handlebars)
- [Handlebars.js Official Documentation](https://handlebarsjs.com/)

---

*Last Updated: October 27, 2025*

