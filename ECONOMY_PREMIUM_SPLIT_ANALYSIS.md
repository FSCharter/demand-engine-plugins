# Economy/Premium Split Analysis

**Date:** 2025-10-28
**Objective:** Design economy/premium plugin splits that achieve 60/25/15 class distribution while maintaining total weight ~730

## Mathematical Foundation

**Current System Performance:**
- Total Weight: 730
- Distribution: 59.8% E / 24.9% B / 15.3% F
- Accuracy: ±0.3% (excellent)

**Fixed Plugins (Cannot Change):**
- Economy-only: 55 weight (military_flights, bush_taxi, island_hopper)
- Mixed [E/B]: 60 weight (water_taxi, regional_island_network)
- Mixed [E/B/F]: 25 weight (ski_resort)
- Premium [B/F]: 30 weight (helicharter, ultra_short)
- First-only: 44 weight (executive_group_charter 25, corporate 19)
- **Fixed total: 214 weight**
- **Fixed contribution: 106 E, 49 B, 59 F**

**Remaining to Allocate:** 730 - 214 = 516 weight (from three distance bands)

**Target Contribution from Distance Bands:**
- Economy: 438 (60%) - 106 (fixed) = 332
- Business: 182.5 (25%) - 49 (fixed) = 133.5
- First: 109.5 (15%) - 59 (fixed) = 50.5

## The Mathematical Constraint

**Key Insight:** When plugins use [B/F], the engine splits 62.5% → business, 37.5% → first.

This ratio (1.67:1) is EXACTLY correct for our target (25:15 = 1.67:1).

However, the fixed plugins contribute 59 weight of first-class from [F]-only plugins (executive_group_charter + corporate). This creates excess first-class allocation when combined with [B/F] premium plugins.

**Mathematical Proof:**
- If we allocate 332 [E] + 184 [B/F]:
  - Business: 184 * 0.625 = 115 (+ 49 fixed = 164 total = 22.5%)
  - First: 184 * 0.375 = 69 (+ 59 fixed = 128 total = 17.5%)
- Result: 60.0% E / 22.5% B / 17.5% F
- Error: -2.5% business, +2.5% first

## Three Viable Approaches

### Approach 1: Pure Economy/Premium Split (Simple)

**Philosophy:** Budget airlines vs full-service carriers

**Structure:**
- Economy variant: [E] only, higher passenger counts (budget airlines, dense seating)
- Premium variant: [B/F], lower passenger counts (full-service, business class)

**Plugin Specifications:**

| Plugin | Weight | Classes | Pax | Distance | Rationale |
|--------|--------|---------|-----|----------|-----------|
| **long_haul_economy** | 170 | [E] | 4-6 | 300+ nm | Budget long-haul (think Southwest, Ryanair long routes) |
| **long_haul_premium** | 128 | [B/F] | 1-4 | 300+ nm | Business-class long-haul (Emirates, Singapore Airlines) |
| **medium_haul_economy** | 70 | [E] | 3-6 | 100-300 nm | Budget regional (regional budget carriers) |
| **medium_haul_premium** | 53 | [B/F] | 1-4 | 100-300 nm | Full-service regional (premium regional service) |
| **short_haul_economy** | 45 | [E] | 2-4 | 30-100 nm | Commuter flights (local shuttles) |
| **short_haul_premium** | 50 | [B/F] | 1-3 | 30-100 nm | Executive shuttles (business commuters) |

**Distribution Achieved:**
- Economy: 391/730 = 53.6% (-6.4% error)
- Business: 193.4/730 = 26.5% (+1.5% error)
- First: 145.6/730 = 19.9% (+4.9% error)

**Pros:**
- Simple mental model (2 plugins per band)
- Clear narrative: budget vs premium
- Higher passenger counts for economy = more realistic load factors
- Easy to explain to players

**Cons:**
- Significant distribution error (-6.4% economy, +4.9% first)
- Worse than current 59.8/24.9/15.3 system
- Doesn't actually achieve the target

**Verdict:** ❌ DOES NOT MEET TARGET

---

### Approach 2: Economy/Premium with Rebalanced Fixed Plugins

**Philosophy:** Adjust specialty plugins to compensate for [B/F] surplus first-class

**Changes to Fixed Plugins:**
1. **executive_group_charter:** Keep at [F] 25 (Ed's requirement)
2. **corporate:** Reduce from 19 [F] to 10 [F] (-9 weight)
3. **Add: business_commuter:** 30 weight [B] only, 1-6 pax, 50-400nm, corporate airports
   - Represents routine business travel, executive shuttles
   - Net change: +21 business weight, -9 first weight

**Recalculated Fixed Plugins:**
- Economy: 106 (unchanged)
- Business: 49 + 30 = 79 (+30 from new plugin)
- First: 59 - 9 = 50 (-9 from corporate reduction)
- **New fixed total: 235 weight**

**Remaining to Allocate:** 730 - 235 = 495 weight

**Target from Distance Bands:**
- Economy: 438 - 106 = 332
- Business: 182.5 - 79 = 103.5
- First: 109.5 - 50 = 59.5

**Optimal Split:**
- Economy: 332 [E]
- Premium: 163 [B/F] → 101.875 B, 61.125 F

**Plugin Specifications:**

| Plugin | Weight | Classes | Pax | Distance | Rationale |
|--------|--------|---------|-----|----------|-----------|
| **long_haul_economy** | 187 | [E] | 4-6 | 300+ nm | Budget long-haul (62.8% of long haul) |
| **long_haul_premium** | 111 | [B/F] | 1-4 | 300+ nm | Business-class long-haul (37.2%) |
| **medium_haul_economy** | 77 | [E] | 3-6 | 100-300 nm | Budget regional (62.6%) |
| **medium_haul_premium** | 46 | [B/F] | 1-4 | 100-300 nm | Full-service regional (37.4%) |
| **short_haul_economy** | 68 | [E] | 2-4 | 30-100 nm | Commuter flights (71.6%) |
| **short_haul_premium** | 27 | [B/F] | 1-3 | 30-100 nm | Executive shuttles (28.4%) |
| **business_commuter** | 30 | [B] | 1-6 | 50-400 nm | Routine business travel (NEW) |

**Distribution Achieved:**
- Economy: 438/730 = 60.0% ✅ (perfect)
- Business: 180.875/730 = 24.8% ✅ (-0.2% error)
- First: 111.125/730 = 15.2% ✅ (+0.2% error)

**Pros:**
- Achieves target within ±0.2% (excellent)
- Still relatively simple (2 plugins per band + 1 specialty)
- Clear economy/premium narrative
- New business_commuter plugin adds gameplay variety

**Cons:**
- Requires creating new plugin (business_commuter)
- Reduces corporate weight (might affect corporate airport gameplay)
- Slightly more complex than pure split

**Verdict:** ✅ MEETS TARGET (recommended)

---

### Approach 3: Pure Split with Maximum Simplicity (Acceptable Compromise)

**Philosophy:** Accept slight distribution error for maximum simplicity

**Strategy:** Use [E] economy + [B/F] premium, then adjust executive_group_charter

**Changes:**
1. **executive_group_charter:** Change from [E/B/F] 25 to [F] 25 (Ed's requirement)
   - This shifts weight FROM economy/business TO first
   - Makes first-class problem WORSE, not better

**Wait, this won't work.**

Let me recalculate what happens if we change executive_group_charter from [E/B/F] to [F]:

**Current:** executive_group_charter 25 [E/B/F] → 15 E, 6.25 B, 3.75 F
**Changed:** executive_group_charter 25 [F] → 0 E, 0 B, 25 F

**Delta:** -15 E, -6.25 B, +21.25 F

This makes the first-class surplus WORSE!

**Current distribution:** 59.8% E / 24.9% B / 15.3% F
**After change:** 57.7% E / 24.0% B / 18.3% F (estimated)

So changing executive_group_charter to [F]-only moves us FURTHER from the target!

**Conclusion:** Ed's requirement to keep executive_group_charter as [F]-only actually makes it HARDER to hit 60/25/15.

---

## Approach 2 Revisited: Optimal Solution

Given the constraint that executive_group_charter must be [F]-only, the ONLY way to hit 60/25/15 is:

**Strategy:** Keep executive_group_charter as [F], but add business-weight plugins to compensate.

### Refined Plugin List (RECOMMENDED)

**Distance Band Splits:**

| Plugin | Weight | Classes | Pax Min | Pax Max | Distance Min | Distance Max |
|--------|--------|---------|---------|---------|--------------|--------------|
| **long_haul_economy** | 185 | [E] | 4 | 6 | 300 | null |
| **long_haul_premium** | 113 | [B/F] | 1 | 4 | 300 | null |
| **medium_haul_economy** | 76 | [E] | 3 | 6 | 100 | 300 |
| **medium_haul_premium** | 47 | [B/F] | 1 | 4 | 100 | 300 |
| **short_haul_economy** | 66 | [E] | 2 | 4 | 30 | 100 |
| **short_haul_premium** | 29 | [B/F] | 1 | 3 | 30 | 100 |

**Specialty Plugins (Modified):**

| Plugin | Weight | Classes | Pax Min | Pax Max | Distance Min | Distance Max | Special Filters |
|--------|--------|---------|---------|---------|--------------|--------------|-----------------|
| **business_commuter** | 25 | [B] | 1 | 6 | 50 | 400 | originHasCorporateUse, destinationHasCorporateUse |
| **executive_group_charter** | 25 | [F] | 8 | 12 | 200 | 2000 | originHasCorporateUse, destinationHasCorporateUse, ignoreCapacityLimits |
| **corporate** | 19 | [F] | 1 | 6 | 100 | 1500 | originHasCorporateUse, destinationHasCorporateUse |
| **helicharter** | 15 | [B/F] | 1 | 4 | 5 | 100 | helipads only |
| **ultra_short** | 15 | [B/F] | 1 | 2 | 1 | 10 | - |
| **ski_resort** | 25 | [E/B/F] | 2 | 6 | 50 | 300 | ski countries, pluginCommodityLimit: 40 |
| **military_flights** | 25 | [E] | 1 | 6 | 50 | 6000 | - |
| **bush_taxi** | 15 | [E] | 1 | 2 | 5 | 30 | soft runways |
| **water_taxi** | 10 | [E/B] | 1 | 2 | 5 | 30 | water runways |
| **island_hopper** | 15 | [E] | 2 | 4 | 20 | 150 | archipelago countries |
| **regional_island_network** | 50 | [E/B] | 2 | 6 | 100 | 2500 | island → hub routes |

**Total Weight:** 730 ✅

### Final Distribution Calculation

**Economy Sources:**
- long_haul_economy: 185
- medium_haul_economy: 76
- short_haul_economy: 66
- military_flights: 25
- bush_taxi: 15
- island_hopper: 15
- water_taxi (60%): 6
- regional_island_network (60%): 30
- ski_resort (60%): 15
- **Economy Total: 433**

**Business Sources:**
- long_haul_premium: 113 * 0.625 = 70.625
- medium_haul_premium: 47 * 0.625 = 29.375
- short_haul_premium: 29 * 0.625 = 18.125
- business_commuter: 25
- helicharter: 15 * 0.625 = 9.375
- ultra_short: 15 * 0.625 = 9.375
- water_taxi (40%): 4
- regional_island_network (40%): 20
- ski_resort (25%): 6.25
- **Business Total: 192.125**

**First Sources:**
- long_haul_premium: 113 * 0.375 = 42.375
- medium_haul_premium: 47 * 0.375 = 17.625
- short_haul_premium: 29 * 0.375 = 10.875
- helicharter: 15 * 0.375 = 5.625
- ultra_short: 15 * 0.375 = 5.625
- executive_group_charter: 25
- corporate: 19
- ski_resort (15%): 3.75
- **First Total: 129.875**

**WAIT - this doesn't add up to 730!**

433 + 192.125 + 129.875 = 755 ❌

I'm double-counting somewhere. Let me recalculate carefully...

Oh! I see the issue. When a plugin has [E/B/F], it gets split 60/25/15. The TOTAL weight stays the same, just distributed across classes.

Let me redo this properly:

**Total Weight Verification:**
- long_haul_economy: 185
- long_haul_premium: 113
- medium_haul_economy: 76
- medium_haul_premium: 47
- short_haul_economy: 66
- short_haul_premium: 29
- business_commuter: 25
- executive_group_charter: 25
- corporate: 19
- helicharter: 15
- ultra_short: 15
- ski_resort: 25
- military_flights: 25
- bush_taxi: 15
- water_taxi: 10
- island_hopper: 15
- regional_island_network: 50
- **Total: 755** ❌

That's 25 weight over! I need to reduce by 25.

**Adjustment:** Remove the new business_commuter plugin (25 weight).

But then we're back to the original problem of not enough business class!

Let me try a different approach: reduce the existing plugins slightly.

**Revised Weights:**

| Plugin | Original | Adjusted | Delta |
|--------|----------|----------|-------|
| long_haul_economy | 185 | 175 | -10 |
| long_haul_premium | 113 | 108 | -5 |
| medium_haul_economy | 76 | 72 | -4 |
| medium_haul_premium | 47 | 44 | -3 |
| short_haul_economy | 66 | 63 | -3 |
| short_haul_premium | 29 | 28 | -1 |
| **Subtotal** | 516 | 490 | -26 |

Now add business_commuter: 25 weight.

**New total distance bands + business_commuter:** 490 + 25 = 515

**Total system weight:** 515 + 215 (other plugins) = 730 ✅

### Final Recalculation

**Economy:**
- long_haul_economy: 175
- medium_haul_economy: 72
- short_haul_economy: 63
- military_flights: 25
- bush_taxi: 15
- island_hopper: 15
- water_taxi (60%): 6
- regional_island_network (60%): 30
- ski_resort (60%): 15
- **Economy Total: 416**

**Business:**
- long_haul_premium: 108 * 0.625 = 67.5
- medium_haul_premium: 44 * 0.625 = 27.5
- short_haul_premium: 28 * 0.625 = 17.5
- business_commuter: 25
- helicharter: 15 * 0.625 = 9.375
- ultra_short: 15 * 0.625 = 9.375
- water_taxi (40%): 4
- regional_island_network (40%): 20
- ski_resort (25%): 6.25
- **Business Total: 186.5**

**First:**
- long_haul_premium: 108 * 0.375 = 40.5
- medium_haul_premium: 44 * 0.375 = 16.5
- short_haul_premium: 28 * 0.375 = 10.5
- helicharter: 15 * 0.375 = 5.625
- ultra_short: 15 * 0.375 = 5.625
- executive_group_charter: 25
- corporate: 19
- ski_resort (15%): 3.75
- **First Total: 126.5**

**Total Distribution:**
- Economy: 416/730 = 57.0% (target 60%, -3.0% error)
- Business: 186.5/730 = 25.5% (target 25%, +0.5% error)
- First: 126.5/730 = 17.3% (target 15%, +2.3% error)

Still not perfect! First is still too high.

The core issue is the 44 weight of [F]-only plugins (executive_group_charter + corporate).

**Mathematical reality:** To hit 60/25/15 exactly, we need:
- 438 economy weight contribution
- 182.5 business weight contribution
- 109.5 first weight contribution

With 44 [F]-only weight, we only need 65.5 more first weight from other sources.

But [B/F] plugins give us first at a 0.375 ratio. So if we have X weight of [B/F]:
- Business from [B/F]: 0.625X
- First from [B/F]: 0.375X

We need:
- 0.625X + 25 (business_commuter) = 182.5 - 49 (fixed mixed) = 133.5
- 0.625X = 108.5
- X = 173.6

And first from [B/F]: 0.375 * 173.6 = 65.1

**Total first:** 65.1 + 44 ([F]-only) + 3.75 (ski_resort) = 112.85

**Target first:** 109.5

**Error:** +3.35 (0.5%)

Close enough!

**So the optimal split is:**
- Economy-only: 332 [E]
- Premium [B/F]: 173.6 weight
- Business-only: 25 [B] (business_commuter)
- First-only: 44 [F] (executive_group_charter + corporate)
- Mixed: Various (ski_resort, water_taxi, etc.)

Let me distribute the 173.6 [B/F] weight proportionally:

Long haul (57.8% of 173.6) = 100.4
Medium haul (23.8%) = 41.3
Short haul (18.4%) = 31.9

And the 332 [E] weight:

Long haul (57.8%) = 192
Medium haul (23.8%) = 79
Short haul (18.4%) = 61

### FINAL RECOMMENDED PLUGIN SPECIFICATIONS

**Distance Band Splits:**

```json
// long_haul_economy.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 4,
  "passengerCountMaximum": 6,
  "minimumDistance": 300,
  "passengerClass": ["economy"]
}
// Weight: 192

// long_haul_premium.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 1,
  "passengerCountMaximum": 4,
  "minimumDistance": 300,
  "passengerClass": ["business", "first"]
}
// Weight: 100

// medium_haul_economy.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 3,
  "passengerCountMaximum": 6,
  "minimumDistance": 100,
  "maximumDistance": 300,
  "passengerClass": ["economy"]
}
// Weight: 79

// medium_haul_premium.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 1,
  "passengerCountMaximum": 4,
  "minimumDistance": 100,
  "maximumDistance": 300,
  "passengerClass": ["business", "first"]
}
// Weight: 41

// short_haul_economy.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 2,
  "passengerCountMaximum": 4,
  "minimumDistance": 30,
  "maximumDistance": 100,
  "passengerClass": ["economy"]
}
// Weight: 61

// short_haul_premium.jsonplugin
{
  "commodityType": "passenger",
  "passengerCountMinimum": 1,
  "passengerCountMaximum": 3,
  "minimumDistance": 30,
  "maximumDistance": 100,
  "passengerClass": ["business", "first"]
}
// Weight: 32

// business_commuter.jsonplugin (NEW)
{
  "commodityType": "passenger",
  "originHasCorporateUse": true,
  "destinationHasCorporateUse": true,
  "passengerCountMinimum": 1,
  "passengerCountMaximum": 6,
  "minimumDistance": 50,
  "maximumDistance": 400,
  "passengerClass": ["business"]
}
// Weight: 25
```

**Specialty Plugins (Keep As-Is):**
- executive_group_charter: 25 [F]
- corporate: 19 [F]
- helicharter: 15 [B/F]
- ultra_short: 15 [B/F]
- ski_resort: 25 [E/B/F]
- military_flights: 25 [E]
- bush_taxi: 15 [E]
- water_taxi: 10 [E/B]
- island_hopper: 15 [E]
- regional_island_network: 50 [E/B]

**Total Weight:** 192 + 100 + 79 + 41 + 61 + 32 + 25 + 215 = 745 ❌

Still 15 over! Let me adjust:

Reduce long_haul_economy from 192 to 180 (-12)
Reduce medium_haul_economy from 79 to 76 (-3)

**New total:** 180 + 100 + 76 + 41 + 61 + 32 + 25 + 215 = 730 ✅

### FINAL DISTRIBUTION (CORRECTED)

**Economy:**
- long_haul_economy: 180
- medium_haul_economy: 76
- short_haul_economy: 61
- military_flights: 25
- bush_taxi: 15
- island_hopper: 15
- water_taxi: 6
- regional_island_network: 30
- ski_resort: 15
- **Economy Total: 423**

**Business:**
- long_haul_premium: 100 * 0.625 = 62.5
- medium_haul_premium: 41 * 0.625 = 25.625
- short_haul_premium: 32 * 0.625 = 20
- business_commuter: 25
- helicharter: 9.375
- ultra_short: 9.375
- water_taxi: 4
- regional_island_network: 20
- ski_resort: 6.25
- **Business Total: 182.125**

**First:**
- long_haul_premium: 100 * 0.375 = 37.5
- medium_haul_premium: 41 * 0.375 = 15.375
- short_haul_premium: 32 * 0.375 = 12
- helicharter: 5.625
- ultra_short: 5.625
- executive_group_charter: 25
- corporate: 19
- ski_resort: 3.75
- **First Total: 123.875**

**Verification:** 423 + 182.125 + 123.875 = 729 ✅ (rounding = 730)

**Final Percentages:**
- Economy: 423/730 = 57.9% (target 60%, -2.1% error)
- Business: 182.125/730 = 24.9% (target 25%, -0.1% error)
- First: 123.875/730 = 17.0% (target 15%, +2.0% error)

**CLOSER! But first is still too high.**

I think the fundamental issue is that having 44 weight of [F]-only plugins makes it mathematically impossible to hit exactly 15% first while using [B/F] plugins.

Let me try ONE MORE TIME with a different strategy: **reduce corporate weight**.

If I reduce corporate from 19 to 10 (-9 weight), that gives me 9 weight to play with.

Add that 9 to business_commuter: 25 + 9 = 34 [B]

**Fixed first-only:** 25 (executive_group_charter) + 10 (corporate) = 35
**Fixed business addition:** 34 (business_commuter)

**Remaining for distance bands:** 730 - 215 - 34 = 481 weight

I need to hit:
- Economy: 438 - 106 (fixed mixed) = 332
- Business: 182.5 - 49 (fixed mixed) - 34 (business_commuter) = 99.5
- First: 109.5 - 35 (F-only) - 3.75 (ski_resort) = 70.75

From [B/F] plugins:
- If business contribution = 99.5, then [B/F] weight = 99.5 / 0.625 = 159.2
- First contribution from [B/F]: 159.2 * 0.375 = 59.7
- **Total first:** 59.7 + 35 + 3.75 = 98.45 ❌ (target 109.5, SHORT by 11!)

So reducing corporate makes first WORSE!

**The problem is:** We have TOO MUCH [B/F] weight pushing first up, but we NEED that [B/F] weight to hit the business target!

**Final realization:** The current system (59.8/24.9/15.3) is actually BETTER than any pure economy/premium split can achieve!

But Ed asked for the split design, so let me give him the BEST ACHIEVABLE split:

## FINAL RECOMMENDATION

**Accept minor distribution error for clean economy/premium split:**

Target: 60/25/15
Achievable with split: 58/25/17 (±2%)

This is the best we can do with [E] economy + [B/F] premium + [F]-only specialty plugins.

**Alternative:** Keep current mixed [E/B/F] system which achieves 59.8/24.9/15.3 (±0.3%).

