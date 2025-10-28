# Plugin Rebalancing 2025 - Distance-Based System

## Overview

This rebalancing shifts from airport-type-based plugins to **distance-based plugins** that trust the geoscore system to allocate demand naturally.

## Philosophy

**Old System**: Plugins restricted by airport type (large/medium/small) + distance
**New System**: Plugins restricted by distance only, geoscores handle airport selection

**Why**:
- Geoscores already encode population, tourism, economy, entity influence
- Airport type restrictions fight against the scoring system
- Distance defines mission profile, geoscore defines where

## Problems Solved

### 1. Island Nation Concentration
- **Problem**: Fiji had 2000+ passengers on single 66nm route (only 2 airports)
- **Solution**:
  - Reduced Island Hopper weight (40 → 25)
  - Added Regional Island Network (international feeders)
  - Added commodity limits to prevent saturation
- **Expected**: 60% reduction in single-route concentration

### 2. Business Jet Full Charters
- **Problem**: Max 6 pax generated, Challenger 350 (14 seats) flies 43% empty
- **Solution**: Executive Group Charter (8-15 pax, weight 60)
- **Expected**: 2-3 full charter opportunities per hour, 75-85% load factor

## New Active Weights (Total: 730)

| Plugin | Weight | % | Pax | Distance | Purpose |
|--------|--------|---|-----|----------|---------|
| **Long Haul** | 350 | 47.9% | 1-6 | 300+ nm | Jets, major routes |
| **Medium Haul** | 150 | 20.5% | 1-6 | 100-300 nm | Turboprops, regional |
| **Short Haul** | 120 | 16.4% | 1-4 | 30-100 nm | Light twins |
| **Executive Group Charter** | 60 | 8.2% | 8-12 | 200-2000 nm | Large biz jets |
| **Regional Island Network** | 60 | 8.2% | 2-6 | 100-2500 nm | Island feeders |
| **Corporate** | 50 | 6.8% | 1-6 | 100-1500 nm | Small biz jets |
| **Ski Resort** | 30 | 4.1% | 2-6 | 50-300 nm | Seasonal |
| **Military Flights** | 25 | 3.4% | 1-6 | 50-6000 nm | Government |
| **Military Transfers** | 25 | 3.4% | 1-6 | 50-250 nm | Base transfers |
| **Bush Taxi** | 20 | 2.7% | 1-2 | 5-30 nm | Soft runways |
| **Water Taxi** | 20 | 2.7% | 1-2 | 5-30 nm | Water runways |
| **Helicharter** | 20 | 2.7% | 1-4 | 5-100 nm | Helipads |
| **Island Hopper** | 15 | 2.1% | 2-4 | 20-150 nm | Multi-hop |
| **Ultra-Short** | 15 | 2.1% | 1-2 | 1-10 nm | City shuttles |

**TOTAL ACTIVE: 730** (down from 920, but more focused)

## Disabled Plugins

| Plugin | Old Weight | Status |
|--------|------------|--------|
| Metropolitan | 500 | 0 [DISABLED - Replaced by Long/Medium/Short Haul] |
| City Hopper | 100 | 0 [DISABLED - Replaced by Short Haul] |
| Local Airfield | 80 | 0 [DISABLED - Replaced by Short Haul] |
| International | 0 | 0 [Already disabled] |
| National | 0 | 0 [Already disabled] |
| Regional | 0 | 0 [Already disabled] |
| Real World Routes | 0 | 0 [Already disabled] |
| US Long Distance | 0 | 0 [Already disabled] |
| Test plugins | 0 | 0 [Already disabled] |

## New Plugin Files Created

1. **ultra_short.jsonplugin** - 1-10nm city shuttles (Weight: 15)
2. **short_haul.jsonplugin** - 30-100nm regional (Weight: 120)
3. **medium_haul.jsonplugin** - 100-300nm turboprops (Weight: 150)
4. **long_haul.jsonplugin** - 300+ nm jets (Weight: 350)
5. **regional_island_network.jsonplugin** - Island → Hub routes (Weight: 60)
6. **executive_group_charter.jsonplugin** - Large biz jets (Weight: 60)

## Modified Plugins

1. **island_hopper.jsonplugin**: 40 → 15 weight, 6 → 4 max pax, TTL 3 → 2, removed FJ from countries, removed pluginCommodityLimit
2. **executive_group_charter.jsonplugin**: 15 → 12 max pax, added ignoreCapacityLimits, removed pluginCommodityLimit
3. **corporate.jsonplugin**: 80 → 50 weight
4. **metropolitan.jsonplugin**: 500 → 0 [DISABLED]
5. **city_hopper.jsonplugin**: 100 → 0 [DISABLED]
6. **local_airfield.jsonplugin**: 80 → 0 [DISABLED]

## Aircraft Coverage

| Aircraft Type | Suitable Plugins | Weight | % |
|---------------|------------------|--------|---|
| Singles (1-3 pax) | Ultra-Short, Bush, Water, Heli | 70 | 9.5% |
| Light Twins (4-6 pax) | Short Haul, Ski | 150 | 20.3% |
| Turboprops (6-19 pax) | Medium Haul, Regional Island | 210 | 28.4% |
| Regional Jets | Long Haul, Medium Haul | 500 | 67.6% |
| Small Biz Jets (4-8) | Corporate, Long Haul | 400 | 54.1% |
| Large Biz Jets (8-15) | Executive Group, Long Haul | 410 | 55.4% |

## Benefits

✅ **Simplicity**: Clear distance bands, easy mental model
✅ **Geoscore Synergy**: System works as designed, no fighting restrictions
✅ **Self-Maintaining**: New airports automatically integrated
✅ **Player Freedom**: "Fly where you want" within distance bands
✅ **Solves Both Problems**: Island concentration + business jet charters

## Rollout Strategy

1. **Phase 1**: Deploy to beta servers, monitor telemetry
2. **Metrics**:
   - Fiji domestic volume (target: -60%)
   - Corporate 8-15 pax generation (target: 2-3/hour)
   - GA operations stability (target: ±10%)
3. **Phase 2**: Fine-tune weights based on real data
4. **Phase 3**: Production deployment

## Rollback Plan

If issues arise:
1. Set new plugins to weight 0
2. Re-enable Metropolitan (500), City Hopper (100), Local Airfield (80)
3. Revert Island Hopper and Corporate changes
4. All changes are in version control

## Expected Player Impact

**Positive**:
- More realistic demand patterns
- Full charter opportunities for large jets
- Better aircraft utilization across all types
- Clearer progression: short → medium → long haul

**Neutral**:
- Total demand slightly reduced (740 vs 920) - geoscores will compensate
- Distance bands may feel different initially (adaptation period)

**Monitoring**:
- Player feedback on route availability
- Economic balance (revenue per flight hour)
- Aircraft market health (utilization rates)

---

## Critical Fixes Applied (Post-Initial Deployment)

### Issue 1: Database Performance - `pluginCommodityLimit`

**Problem**: Simon identified that `pluginCommodityLimit` causes database performance issues. Every 500ms generation tick requires checking commodity counts across the entire database.

**Fix**: Removed `pluginCommodityLimit` from both plugins:
- Island Hopper: Removed limit of 30
- Executive Group Charter: Removed limit of 25

**Rationale**: Trust the built-in geoscore capacity system (origin × 0.8, destination × 0.4). Artificial plugin limits fight the capacity system and cause performance overhead.

### Issue 2: Executive Group Charter Validation Failure

**Problem**: "Passenger group size validation failed" error - corporate airports (low geoscores) cannot support 8-15 passenger groups under normal capacity constraints.

**Fix**:
- Added `ignoreCapacityLimits: true` to Executive Group Charter
- Reduced max passengers from 15 to 12 (safer range for Challenger 350)

**Rationale**: Executive charters are premium, low-frequency events that shouldn't be constrained by general capacity rules. This is a special exception that makes logical sense for full aircraft charters.

### Issue 3: Fiji Island Concentration Persistence

**Problem**: Even with reduced weight, Fiji still concentrated 2000+ passengers on single 66nm route due to only 2 high-geoscore airports and `sameCountry: true` restriction.

**Fix**:
- Removed Fiji (FJ) from Island Hopper country list
- Reduced weight further: 25 → 15 (from original 40)

**Rationale**: Fiji domestic traffic is better served by Regional Island Network (FJ → AU/NZ/SG/TH international feeders) which provides more realistic traffic patterns. Other archipelagos (Philippines, Indonesia, Japan islands, Greece, Croatia, Marshall Islands) remain served.

### Weight Impact

**Before Fixes**: 740 total weight
**After Fixes**: 730 total weight (-10)

**Changed Weights**:
- Island Hopper: 25 → 15 (-10 weight, -1.3%)

**Key Benefits**:
✅ Eliminates database performance overhead from commodity limit checks
✅ Fixes Executive Group Charter validation - business jets can now generate 8-12 passenger groups
✅ Prevents Fiji concentration while maintaining archipelago service
✅ Trusts geoscore capacity system as designed
✅ Creates appropriate exception for premium charter events

---

## Passenger Class Distribution Rebalancing

### Target Distribution
- Economy: 60%
- Business: 25%
- First: 15%

### Problem Identified

After initial distance-based rebalancing, the passenger class distribution was skewed:
- Economy: 57.5% (target: 60%) - **-2.5%**
- Business: 21.9% (target: 25%) - **-3.1%**
- First: 20.6% (target: 15%) - **+5.6%**

**Root Causes:**
1. Too many first-class-only plugins (Executive Group Charter 60 + Corporate 50 = 110 weight)
2. Missing first class on medium haul and ski resort (unrealistic - wealthy passengers fly these routes too)
3. Helicharter incorrectly set to economy (helicopter charters are premium-only)
4. Class restrictions fighting narrative realism

### Solution: Expand to [E/B/F] + Weight Rebalancing

**Core Principle:** Use `["economy", "business", "first"]` as the DEFAULT for most plugins. The demand engine automatically applies 60/25/15 weighting when all three classes are present.

**Only restrict classes where narrative demands it:**
- Bush Taxi → economy only (remote basic transport)
- Corporate → first only (C-suite private jets)
- Helicharter → business/first only (premium service)
- Ultra-Short → business/first only (city VIP shuttles)

### Changes Applied

| Plugin | Before | After | Rationale |
|--------|--------|-------|-----------|
| **medium_haul** | [E/B] 150 | [E/B/F] 123 | Wealthy passengers fly 100-300nm routes (London-Athens, NYC-Denver) |
| **ski_resort** | [E/B] 30 | [E/B/F] 25 | Luxury ski resorts exist (Aspen, Courchevel, St. Moritz) |
| **executive_group_charter** | [F] 60 | [E/B/F] 25 | Not all group charters are first class (sports teams, wedding parties, corporate retreats) |
| **helicharter** | [E] 20 | [B/F] 15 | Helicopter charters are NEVER economy - premium service only |
| **corporate** | [F] 50 | [F] 19 | Small business jets stay first-only, but reduced weight to fix distribution |
| **water_taxi** | [E/F] 20 | [E/B] 10 | Consistency with other regional plugins |
| **military_flights** | (not specified) 25 | [E] 25 | Military transport is economy-only |
| **military_transfers** | (not specified) 25 | 0 [DISABLED] | Merged into military_flights for simplicity |
| **short_haul** | [E/B/F] 120 | [E/B/F] 95 | Weight reduction for balance |
| **long_haul** | [E/B/F] 350 | [E/B/F] 298 | Weight reduction for balance |
| **bush_taxi** | [E] 20 | [E] 15 | Weight reduction for balance |
| **regional_island_network** | [E/B] 60 | [E/B] 50 | Weight reduction for balance |

### Final Distribution

**Total Active Weight: 730** (unchanged)

| Class | Target | Achieved | Delta |
|-------|--------|----------|-------|
| Economy | 60.0% | 59.8% | -0.2% ✅ |
| Business | 25.0% | 24.9% | -0.1% ✅ |
| First | 15.0% | 15.3% | +0.3% ✅ |

**Result: ±0.3% accuracy - excellent balance achieved!**

### Key Benefits

1. **Mathematical Precision:** Within ±0.3% of target distribution
2. **Narrative Realism:** Wealthy passengers can now access all route types (skiing, medium haul, group charters)
3. **Gameplay Variety:** More aircraft types have appropriate demand across all classes
4. **Simplicity:** Fewer special cases, clearer mental model
5. **Business Class Fix:** Increased from 21.9% to 24.9% (closer to 25% target)
6. **Premium Service Fix:** Helicopters now correctly positioned as business/first-class service

### Real-World Alignment

- ✅ First-class passengers DO ski at luxury resorts (Aspen costs $15k/week)
- ✅ Wealthy passengers DO fly medium haul (Emirates first class on 3-hour routes)
- ✅ Group charters AREN'T always first class (sports teams, corporate retreats)
- ✅ Helicopter charters are NEVER economy (they're executive transport)
- ✅ Military flights are economy-only (service personnel)
- ✅ Corporate jets stay first-only (C-suite transport)

---

**Date**: October 2025
**Branch**: feature/distance-based-rebalancing
**Status**: Ready for testing (distance-based + class distribution fixes applied)
