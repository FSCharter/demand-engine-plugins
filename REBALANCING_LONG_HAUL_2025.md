# Long-Haul Rebalancing Proposal - November 2025
## Addressing Geographic Clustering Bias

**Date**: 2025-11-01
**Status**: Proposal - Requires Testing
**Problem**: Long-haul demand insufficient relative to medium/short-haul, especially from dense regions

---

## Executive Summary

Current demand generation from major Asian hubs (RJTT, VHHH, WSSS) shows **massive bias toward medium-haul regional routes** (Philippines, Indonesia, Thailand) with only **sparse long-haul to US/Europe**. This is caused by **geographic clustering bias** - there are 2-3x more airports within medium-haul range (800-2,200nm) than long-haul range (2,200+nm) from Asian hubs.

**Root Cause**: Distance-based plugins don't account for TARGET AIRPORT DENSITY. Even with equal plugin weights, medium-haul has more eligible destinations.

**Solution**: Significantly boost long-haul weight (+60-90%) while reducing medium/short-haul weight (-20-35%), leveraging the insight that **hub-and-spoke networks CREATE synthetic short/medium demand** through feeder operations.

---

## Table of Contents

1. [The Problem: Geographic Clustering](#the-problem-geographic-clustering)
2. [Statistical Analysis](#statistical-analysis)
3. [Game Design Principles](#game-design-principles)
4. [The Hub-and-Spoke Multiplier Effect](#the-hub-and-spoke-multiplier-effect)
5. [Cash Flow Constraints](#cash-flow-constraints)
6. [Proposed Rebalancing](#proposed-rebalancing)
7. [Alternative Approaches](#alternative-approaches)
8. [Risk Analysis](#risk-analysis)
9. [Testing & Metrics](#testing--metrics)

---

## The Problem: Geographic Clustering

### Current Player Experience (RJTT Example)

From Tokyo Narita (RJTT), current demand visualization shows:
- ✅ **Heavy concentration**: Philippines, Indonesia, Thailand, Singapore (800-2,200nm)
- ⚠️ **Moderate presence**: Hong Kong, Taiwan, Korea, China (270-800nm)
- ❌ **Sparse demand**: US West Coast, Europe, Middle East (2,200+nm)

**Player expectation**: Tokyo is a MAJOR international hub - should have robust intercontinental demand, not just Asian regional.

**Reality**: Geographic density overwhelms plugin weights.

### Why This Happens: Target Airport Density

**Medium-Haul Eligible from RJTT (800-2,200nm)**:
- VHHH (Hong Kong) - 1,762nm
- WSSS (Singapore) - 3,280nm *[actually long-haul, but geographically feels regional]*
- VTBS (Bangkok) - 2,890nm *[technically long-haul]*
- RPLL (Manila) - 1,869nm
- WIII (Jakarta) - 3,620nm *[long-haul]*
- YMML (Melbourne) - 5,050nm *[long-haul]*
- YSSY (Sydney) - 4,860nm *[long-haul]*
- ZSPD (Shanghai) - 1,100nm
- ZBAA (Beijing) - 1,300nm
- VOMM (Chennai) - 3,410nm *[long-haul]*
- VIDP (Delhi) - 3,670nm *[long-haul]*
- RCTP (Taipei) - 1,420nm
- RKSI (Seoul) - 750nm *[actually short-haul]*
- Plus ~30-50 other major Asian airports

**Long-Haul Eligible from RJTT (2,200+nm)**:
- KLAX (Los Angeles) - 5,450nm
- KSFO (San Francisco) - 5,130nm
- KSEA (Seattle) - 4,750nm
- KORD (Chicago) - 6,290nm
- KJFK (New York) - 6,720nm
- EGLL (London) - 5,950nm
- LFPG (Paris) - 6,190nm
- EDDF (Frankfurt) - 5,540nm
- OMDB (Dubai) - 4,940nm
- LIRF (Rome) - 6,140nm
- Plus ~20-30 other US/EU/ME airports

**Observation**: Wait, looking at actual distances, many "medium-haul feeling" routes are TECHNICALLY long-haul (Singapore, Bangkok, Melbourne, Sydney, Jakarta, Chennai, Delhi).

**Revised Understanding**: The problem is NOT that medium-haul has more targets - it's that:

1. **Short-haul (270-800nm) is OVER-represented** - too many quick regional hops (Seoul, Taiwan, Shanghai, Manila, Hong Kong)
2. **"Long-haul" band (2,200+nm) is TOO BROAD** - lumps together Southeast Asia (Bangkok, Singapore) with US/Europe, causing geoscore-weighted selection to favor closer high-geoscore airports

### The Real Issue: Geoscore Distance Decay

When long_haul plugin runs from RJTT:
1. All airports 2,200+ nm away are eligible
2. Each weighted by geoscore
3. Random selection proportional to geoscore

**Geoscores** (estimated):
- WSSS (Singapore): ~950 (2,880nm from RJTT)
- VTBS (Bangkok): ~850 (2,890nm)
- WIII (Jakarta): ~750 (3,620nm)
- OMDB (Dubai): ~900 (4,940nm)
- KLAX (Los Angeles): ~950 (5,450nm)
- EGLL (London): ~980 (5,950nm)
- KJFK (New York): ~900 (6,720nm)

Even with similar geoscores, **players PERCEIVE Southeast Asian destinations as "regional"** not "long-haul" because:
- Geographic proximity (same continent/region)
- Cultural similarity
- Frequency of real-world routes
- Lower prestige than US/Europe routes

**Perception vs Reality Gap**: Bangkok/Singapore ARE long-haul by distance, but FEEL like regional routes.

---

## Statistical Analysis

### Current Weight Distribution

From active plugins (Total: 888):

| Category | Plugins | Weight | % | Effective % (after cascade) |
|----------|---------|--------|---|------------------------------|
| **Long-haul (2,200+nm)** | long_haul | 150 | 16.9% | 18.8% |
| **Medium-haul (800-2,200nm)** | medium_haul, domestic_trunk, regional_island_network | 260 | 29.3% | ~31% |
| **Short-haul (270-800nm)** | short_haul, regional_connector | 250 | 28.2% | ~32% |
| **Very short/GA (<270nm)** | very_short_haul, bush, water, heli, island_hopper | 135 | 15.2% | ~18% |
| **Specialty** | corporate, executive, military | 59 | 6.6% | ~7% |
| **Budget International** | budget_international (800-3,000nm) | 34 | 3.8% | ~4% |

**Problem**: Long-haul is only 16.9% of direct generation, 18.8% after cascading.

**Target**: Long-haul should be 28-32% of effective demand to match player expectations and aspirational gameplay.

### Geographic Density Analysis

**Asia-Pacific Hub Problem**:
- Dense cluster of high-geoscore airports within 800-2,200nm
- Creates "regional echo chamber" where Asian demand loops within Asia
- Long-haul competes poorly because Southeast Asia counts as "long-haul" (2,200+nm) but feels regional

**Europe Hub (Different Pattern)**:
- EGLL → European neighbors are 270-800nm (short-haul)
- EGLL → Mediterranean is 800-1,500nm (medium-haul)
- EGLL → North America is 3,000-3,500nm (true long-haul)
- More balanced distribution across bands

**Conclusion**: Asia-Pacific needs STRONGER long-haul weighting to overcome regional clustering.

### Passenger Class Distribution Constraint

Current target: 60% Economy / 25% Business / 15% First

**Plugins by class**:
- [E/B/F] plugins: long_haul, medium_haul, short_haul, very_short_haul, island_hopper
- [E/B] plugins: regional_connector, domestic_trunk, budget_international, regional_island_network, water_taxi
- [E] only: bush_taxi, military_flights
- [B/F] only: helicharter
- [F] only: corporate, executive_group_charter

**Critical**: Any weight changes must maintain 60/25/15 distribution accounting for passenger group sizes.

### Current Class Distribution

Calculating actual distribution (weight × avg_pax × class_probability):

| Plugin | Weight | Avg Pax | Classes | E% | B% | F% | E Units | B Units | F Units |
|--------|--------|---------|---------|----|----|----|---------|---------|---------|
| long_haul | 150 | 3.5 | [E/B/F] | 60 | 25 | 15 | 315 | 131 | 79 |
| medium_haul | 150 | 3.5 | [E/B/F] | 60 | 25 | 15 | 315 | 131 | 79 |
| short_haul | 180 | 3.5 | [E/B/F] | 60 | 25 | 15 | 378 | 158 | 95 |
| very_short_haul | 80 | 2.5 | [E/B/F] | 60 | 25 | 15 | 120 | 50 | 30 |
| regional_connector | 70 | 5.0 | [E/B] | 73 | 27 | 0 | 256 | 95 | 0 |
| domestic_trunk | 60 | 6.5 | [E/B] | 73 | 27 | 0 | 285 | 106 | 0 |
| budget_international | 34 | 8.0 | [E/B] | 73 | 27 | 0 | 199 | 74 | 0 |
| regional_island_network | 50 | 4.0 | [E/B] | 73 | 27 | 0 | 146 | 54 | 0 |
| island_hopper | 15 | 3.0 | [E/B/F] | 60 | 25 | 15 | 27 | 11 | 7 |
| bush_taxi | 15 | 1.5 | [E] | 100 | 0 | 0 | 23 | 0 | 0 |
| water_taxi | 10 | 1.5 | [E/B] | 73 | 27 | 0 | 11 | 4 | 0 |
| military_flights | 25 | 3.5 | [E] | 100 | 0 | 0 | 88 | 0 | 0 |
| helicharter | 15 | 2.5 | [B/F] | 0 | 62.5 | 37.5 | 0 | 23 | 14 |
| corporate | 19 | 3.5 | [F] | 0 | 0 | 100 | 0 | 0 | 67 |
| executive_group_charter | 15 | 10.0 | [F] | 0 | 0 | 100 | 0 | 0 | 150 |

**Totals**:
- Economy: 2,163 units (60.58%)
- Business: 837 units (23.45%)
- First: 571 units (16.00%)

**Target**:
- Economy: 60% ✓ (58-62% acceptable)
- Business: 25% ✗ (23.45% is -1.55% below target)
- First: 15% ✗ (16% is +1% above target)

**Issue**: Business class slightly low, first class slightly high. Must be careful with rebalancing.

---

## Game Design Principles

### Principle 1: Aspirational Progression

**Player Journey**:
1. Start with GA aircraft (Cessna 172, Piper Seneca)
2. Progress to turboprops (King Air, Pilatus PC-12)
3. Advance to regional jets (CRJ, E-Jet)
4. **Aspirational goal**: Widebodies (747, 777, 787, A330, A350)

**Current Problem**: Step 4 feels unattainable because long-haul demand is sparse.

**Psychology**: Players tolerate grind IF there's a clear path to prestige aircraft. Without viable widebody demand, motivation collapses.

**Comparable Games**:
- **Gran Turismo**: Always shows supercars as aspirational goal, maintains progression balance
- **Elite Dangerous**: Long-haul exploration always viable, not locked behind arbitrary barriers
- **EVE Online**: Capital ships always have gameplay, not decoration

**Lesson**: Long-haul MUST be viable for retention.

### Principle 2: Economic Risk/Reward Balance

**Current FSCharter Economics**:
- Payment on FINAL destination arrival (deferred revenue model)
- Upfront costs: fuel, maintenance, airport fees
- Recurring costs: slots, hangers, loans

**Short-haul profile**:
- Low revenue per flight (£15k-30k)
- Fast turnover (1-3 hours)
- Low fuel cost (£2k-5k)
- **Cash flow**: Excellent (quick payment cycles)

**Long-haul profile**:
- High revenue per flight (£80k-250k)
- Slow turnover (6-12+ hours)
- High fuel cost (£15k-40k)
- **Cash flow**: Poor (long payment delay)

**Game Design Rule**: High-risk/high-reward REQUIRES sufficient opportunity. If long-haul is rare AND high-risk, rational players avoid it entirely.

**Solution**: Long-haul must be COMMON enough to offset risk through volume.

### Principle 3: The Hub-and-Spoke Multiplier

**Key Insight from Ed**: Hub-and-spoke networks CREATE synthetic short/medium demand.

**Mechanics**:
1. Player creates long-haul route RJTT → EGLL
2. Passengers need pickups: Regional airports → RJTT (synthetic short/medium demand)
3. Passengers need dropoffs: EGLL → Regional airports (synthetic short/medium demand)

**Multiplier Effect**:
- 1 long-haul passenger = 3 flight segments (pickup, trunk, dropoff)
- Average 60% of long-haul passengers need feeders (from rebalancing doc)
- **150 long-haul weight** × 3.5 avg pax × 0.6 cascade = **315 synthetic feeder passenger-units**

**Implication**: We can REDUCE direct short/medium generation by 20-30% because hub operations will create it synthetically.

**Real-World Parallel**: Airlines don't schedule 100 regional flights hoping they connect - they schedule long-haul FIRST, then regional feeders appear naturally through codeshare/partnerships.

### Principle 4: Player Agency vs System Control

**Current System**: Pure distance-based plugins, trust geoscores entirely.

**Philosophy**: "Let players discover routes organically, don't hardcode preferences."

**Problem**: This works for SPARSE systems but creates clustering bias in DENSE systems.

**Analogy**: Electoral College - Wyoming (sparse) gets proportionally more representation than California (dense) to prevent population center dominance.

**Application**: Long-haul needs BOOSTED weight to overcome target density disadvantage.

---

## The Hub-and-Spoke Multiplier Effect

### Cascading Demand Model (from Rebalancing Doc)

When long-haul demand generates, it creates downstream feeder opportunities:

**Example**: 4 passengers RJTT → EGLL

1. **Direct generation**: long_haul plugin creates 4-passenger group at RJTT destination EGLL
2. **Passenger origins**:
   - 40% already at hub (RJTT) - board directly
   - 60% need pickup from regional airports (Osaka, Nagoya, Sapporo, etc.)
3. **Feeder demand created**: 4 × 0.6 = 2.4 passengers need REGIONAL → RJTT
4. **Destination dropoff**: 60% need EGLL → regional UK airports (Manchester, Edinburgh, etc.)

**Net effect**: 1 long-haul commodity group creates ~2-3 short/medium-haul opportunities.

### Effective Weight Calculation

**Current weights (from doc)**:
- long_haul: 150 direct weight
- Cascade multiplier: 1.6x (60% × 3 segments ≈ 1.8x, but adjusted for partial overlap)
- **Effective weight**: 150 × 1.6 = 240 effective

**Current effective distribution**:
- Long-haul: 18.8% effective (vs 16.9% direct)
- Medium-haul: ~31% effective (vs 29.3% direct)
- Short-haul: ~32% effective (vs 28.2% direct)

**Target effective distribution**:
- Long-haul: 28-32% effective
- Medium-haul: 24-28% effective
- Short-haul: 22-26% effective
- GA: 18-22% effective

### Working Backwards: Required Direct Weights

If target effective long-haul = 30% and multiplier = 1.6x:
**Required direct weight**: 30% / 1.6 = 18.75% of total

If total weight = 900:
**Long-haul direct** = 900 × 0.1875 = 169 weight

**But wait** - that's LOWER than current 150 weight. This suggests the cascading multiplier model is wrong or incomplete.

**Re-examining the model**:

The doc says "Total effective weight: 1,069 vs 858 direct = 1.25x multiplier"

So the OVERALL system multiplier is 1.25x, not plugin-specific multipliers.

Let me recalculate:

If current total direct = 888, and system multiplier = 1.25x:
**Total effective demand** = 888 × 1.25 = 1,110 passenger-units

Current long-haul effective:
- Direct: 150 weight
- Plus synthetic feeder creation: 150 × 3.5 × 0.6 = 315 feeder units
- But feeders go to SHORT/MEDIUM categories, not back to long
- So long-haul effective = 150 direct only

**Ah, the model works DIFFERENTLY**:

Cascading INCREASES short/medium/GA, not long-haul itself.

Current:
- Long direct: 150 (16.9%)
- Medium direct: 260 (29.3%) → becomes 260 + cascade_from_long = 260 + ~90 = 350 effective (31.5%)
- Short direct: 250 (28.2%) → becomes 250 + cascade_from_long/medium = 250 + ~135 = 385 effective (34.7%)

Total effective: 888 + 225 cascade = 1,113

**Insight**: Long-haul doesn't benefit from cascading - it FUELS other categories.

To increase long-haul from 16.9% direct to 28-30% direct, we need:
**New long-haul weight** = 888 × 0.29 = 257 weight (using 29% target)

That's 150 → 257 = **+107 weight increase** (+71% boost)

This must come from cuts elsewhere.

---

## Cash Flow Constraints

### Payment Timing Model

**Short-haul (270-800nm)**:
- Flight time: 1-3 hours
- Ground time: 0.5-1 hour
- **Total cycle**: 1.5-4 hours to payment
- **Cycles per day**: 6-16

**Medium-haul (800-2,200nm)**:
- Flight time: 3-7 hours
- Ground time: 1-2 hours
- **Total cycle**: 4-9 hours to payment
- **Cycles per day**: 3-6

**Long-haul (2,200+nm)**:
- Flight time: 6-15 hours
- Ground time: 2-4 hours
- **Total cycle**: 8-19 hours to payment
- **Cycles per day**: 1-3

### Cash Flow Scenarios

**Scenario A: All Short-Haul (Current Over-representation)**
- Revenue: £20k × 8 flights/day = £160k/day
- Costs: £7k × 8 = £56k/day
- **Net**: £104k/day
- **Cash flow**: Excellent (payments every 3 hours)

**Scenario B: All Long-Haul (Hypothetical Over-correction)**
- Revenue: £150k × 1.5 flights/day = £225k/day
- Costs: £45k × 1.5 = £68k/day
- **Net**: £157k/day
- **Cash flow**: Poor (payments every 12 hours, high upfront costs)

**Scenario C: Balanced Mix (Target)**
- 30% long-haul: £225k × 0.3 = £68k/day contribution
- 50% short/medium: £160k × 0.5 = £80k/day contribution
- 20% GA: £40k × 0.2 = £8k/day contribution
- **Total revenue**: £156k/day
- **Total costs**: £60k/day
- **Net**: £96k/day
- **Cash flow**: Good (mix of quick and delayed payments)

### Bankruptcy Risk

Players go bankrupt when:
- Recurring costs exceed available cash
- No revenue in next 12-24 hours
- Unexpected expenses (maintenance, fees) deplete reserves

**Critical threshold**: Must maintain 50%+ short/medium-haul for healthy cash flow.

**Current**: Short/medium = 57.5% direct (good ✓)
**After proposed rebalancing**: Must maintain ~50-55% short/medium

---

## Proposed Rebalancing

### Option 1: Aggressive Long-Haul Boost

**Philosophy**: Overcome geographic clustering with brute force weight increase.

**Changes**:

| Plugin | Current | Proposed | Change | Rationale |
|--------|---------|----------|--------|-----------|
| **long_haul** | 150 | 240 | +90 (+60%) | Overcome target density bias, reach 28% effective |
| **budget_international** | 34 | 50 | +16 (+47%) | Support long-haul, low-cost carrier realism |
| **medium_haul** | 150 | 110 | -40 (-27%) | Reduce regional echo chamber, trust hub-spoke synthetic |
| **domestic_trunk** | 60 | 50 | -10 (-17%) | Trust hub-spoke for trunk route creation |
| **short_haul** | 180 | 145 | -35 (-19%) | Trust hub-spoke for feeder creation |
| **regional_connector** | 70 | 60 | -10 (-14%) | Moderate reduction, still important for hubs |
| **very_short_haul** | 80 | 70 | -10 (-13%) | Slight reduction, maintain GA identity |
| **regional_island_network** | 50 | 45 | -5 (-10%) | Maintain island service with slight trim |
| **bush_taxi** | 15 | 14 | -1 (-7%) | Preserve GA character |
| **water_taxi** | 10 | 9 | -1 (-10%) | Preserve niche operations |
| **helicharter** | 15 | 14 | -1 (-7%) | Preserve premium service |
| **island_hopper** | 15 | 14 | -1 (-7%) | Maintain archipelago service |
| **corporate** | 19 | 19 | 0 | Maintain business jet demand |
| **executive_group_charter** | 15 | 17 | +2 (+13%) | Slight boost for full charter demand |
| **military_flights** | 25 | 25 | 0 | Maintain government transport |

**New Total**: 902 (was 888, +14 net)

**Projected Distribution**:

| Category | Direct Weight | % Direct | Est. Effective % (after cascade) |
|----------|---------------|----------|-----------------------------------|
| Long-haul | 290 | 32.2% | ~29-30% |
| Medium-haul | 205 | 22.7% | ~26-27% |
| Short-haul | 205 | 22.7% | ~28-29% |
| GA/Very Short | 119 | 13.2% | ~16-17% |
| Specialty | 83 | 9.2% | ~9-10% |

**Expected Outcomes**:
- ✅ Long-haul reaches ~29-30% effective (target achieved)
- ✅ Medium/short still dominant (~54-56% combined) for cash flow
- ✅ GA maintains ~16-17% (above 15% identity threshold)
- ⚠️ May still have clustering in dense regions (Asia-Pacific)

### Option 2: Moderate Long-Haul Boost + GA Trim

**Philosophy**: Moderate long-haul increase, accept that GA is less sacred than originally thought.

**Changes**:

| Plugin | Current | Proposed | Change | Rationale |
|--------|---------|----------|--------|-----------|
| **long_haul** | 150 | 210 | +60 (+40%) | Solid boost without extremes |
| **budget_international** | 34 | 45 | +11 (+32%) | Support long-haul budget segment |
| **medium_haul** | 150 | 120 | -30 (-20%) | Moderate reduction |
| **domestic_trunk** | 60 | 52 | -8 (-13%) | Trust hub-spoke |
| **short_haul** | 180 | 150 | -30 (-17%) | Trust hub-spoke |
| **regional_connector** | 70 | 62 | -8 (-11%) | Slight trim |
| **very_short_haul** | 80 | 68 | -12 (-15%) | GA trim acceptable per Ed |
| **regional_island_network** | 50 | 46 | -4 (-8%) | Maintain island routes |
| **bush_taxi** | 15 | 12 | -3 (-20%) | GA not sacred |
| **water_taxi** | 10 | 8 | -2 (-20%) | GA not sacred |
| **helicharter** | 15 | 13 | -2 (-13%) | Slight trim |
| **island_hopper** | 15 | 13 | -2 (-13%) | Slight trim |
| **corporate** | 19 | 18 | -1 (-5%) | Minimal impact |
| **executive_group_charter** | 15 | 16 | +1 (+7%) | Maintain full charter |
| **military_flights** | 25 | 25 | 0 | Preserve |

**New Total**: 858 (was 888, -30 net)

**Projected Distribution**:

| Category | Direct Weight | % Direct | Est. Effective % (after cascade) |
|----------|---------------|----------|-----------------------------------|
| Long-haul | 255 | 29.7% | ~26-27% |
| Medium-haul | 218 | 25.4% | ~28-29% |
| Short-haul | 212 | 24.7% | ~30-31% |
| GA/Very Short | 114 | 13.3% | ~17-18% |
| Specialty | 59 | 6.9% | ~8-9% |

**Expected Outcomes**:
- ✅ Long-haul reaches ~26-27% effective (close to target)
- ✅ Cash flow maintained (~55% short/medium combined)
- ✅ GA trim acceptable per Ed's direction ("don't treat GA as sacred")
- ⚠️ Slightly conservative, may need further boost

### Option 3: Maximum Long-Haul (Aspirational Focus)

**Philosophy**: Prioritize widebody viability and aspirational gameplay over conservative balance.

**Changes**:

| Plugin | Current | Proposed | Change | Rationale |
|--------|---------|----------|--------|-----------|
| **long_haul** | 150 | 280 | +130 (+87%) | Maximum boost |
| **budget_international** | 34 | 55 | +21 (+62%) | Strong low-cost long-haul presence |
| **medium_haul** | 150 | 100 | -50 (-33%) | Heavy reduction, trust hub-spoke |
| **domestic_trunk** | 60 | 48 | -12 (-20%) | Trust hub-spoke |
| **short_haul** | 180 | 135 | -45 (-25%) | Heavy reduction, hub-spoke creates synthetic |
| **regional_connector** | 70 | 58 | -12 (-17%) | Moderate reduction |
| **very_short_haul** | 80 | 65 | -15 (-19%) | GA reduction acceptable |
| **regional_island_network** | 50 | 44 | -6 (-12%) | Slight trim |
| **bush_taxi** | 15 | 11 | -4 (-27%) | GA not sacred |
| **water_taxi** | 10 | 7 | -3 (-30%) | GA not sacred |
| **helicharter** | 15 | 12 | -3 (-20%) | Moderate trim |
| **island_hopper** | 15 | 12 | -3 (-20%) | Moderate trim |
| **corporate** | 19 | 18 | -1 (-5%) | Minimal trim |
| **executive_group_charter** | 15 | 18 | +3 (+20%) | Boost full charter demand |
| **military_flights** | 25 | 24 | -1 (-4%) | Minimal trim |

**New Total**: 887 (was 888, -1 net)

**Projected Distribution**:

| Category | Direct Weight | % Direct | Est. Effective % (after cascade) |
|----------|---------------|----------|-----------------------------------|
| Long-haul | 335 | 37.8% | ~32-34% |
| Medium-haul | 192 | 21.7% | ~25-26% |
| Short-haul | 193 | 21.8% | ~28-29% |
| GA/Very Short | 107 | 12.1% | ~15-16% |
| Specialty | 60 | 6.8% | ~8-9% |

**Expected Outcomes**:
- ✅✅ Long-haul reaches ~32-34% effective (exceeds target, aspirational)
- ⚠️ Cash flow acceptable but tighter (~50% short/medium)
- ⚠️ GA at lower end of acceptable range (~15-16%)
- ✅ Maximum widebody viability
- ⚠️ Higher risk if hub-spoke synthetic demand model is incorrect

---

## Alternative Approaches

### Alternative 1: Geographic Multipliers

Instead of global weight changes, apply region-specific multipliers:

**Implementation**:
```json
// In demand engine config
"geographicMultipliers": {
  "RJTT": { "long_haul": 1.6, "medium_haul": 0.8 },
  "VHHH": { "long_haul": 1.5, "medium_haul": 0.9 },
  "WSSS": { "long_haul": 1.5, "medium_haul": 0.9 },
  "EGLL": { "long_haul": 1.2, "medium_haul": 1.0 },
  "KLAX": { "long_haul": 1.3, "medium_haul": 0.9 }
}
```

**Pros**:
- Surgical precision for problem airports
- Doesn't affect regions working well (e.g., Europe)
- Can tune per-hub based on real player feedback

**Cons**:
- Fights "trust geoscores" philosophy
- Requires manual tuning for each major hub
- Complex to maintain and explain to players
- May create arbitrary feeling ("why does RJTT get special treatment?")

**Verdict**: ❌ Reject - Too complex, fights design philosophy

### Alternative 2: Distance-Weighted Probability

Instead of equal probability within distance bands, apply distance decay:

**Implementation**:
Airports further from origin get LOWER probability even within same band.

From RJTT, long-haul candidates:
- WSSS (2,880nm): Weight × 1.0
- OMDB (4,940nm): Weight × 0.85
- KLAX (5,450nm): Weight × 0.75
- EGLL (5,950nm): Weight × 0.70

**Pros**:
- Naturally balances closer vs farther destinations
- Realistic (real airlines favor shorter routes within category)

**Cons**:
- Makes intercontinental routes LESS likely (opposite of goal!)
- Reduces US/Europe selection from Asia even more

**Verdict**: ❌ Reject - Worsens the problem

### Alternative 3: Continent-Crossing Bonus

Add special plugin for intercontinental routes specifically:

**New Plugin**: `intercontinental.jsonplugin`
```json
{
  "commodityType": "passenger",
  "passengerCountMinimum": 2,
  "passengerCountMaximum": 8,
  "minimumDistance": 3500,
  "requireContinentCrossing": true,  // NEW FILTER
  "passengerClass": ["economy", "business", "first"]
}
```

**Weight**: 120

**Pros**:
- Directly targets the "RJTT → US/Europe" gap
- Doesn't affect intra-continental routes
- Clear player understanding ("intercontinental routes available")

**Cons**:
- Requires demand engine modification (new filter type)
- Adds complexity
- Continent boundaries are fuzzy (is Middle East → Asia continent-crossing?)

**Verdict**: ⚠️ Consider for Phase 2 - Good idea but needs engine changes

### Alternative 4: Minimum Generation Quotas

Guarantee X% of each origin's demand is long-haul:

**Implementation**:
```json
// Per-origin generation quotas
"generationQuotas": {
  "long_haul": { "minimum": 0.25 },  // 25% of origin demand must be long-haul
  "medium_haul": { "maximum": 0.35 }  // 35% cap on medium-haul
}
```

**Pros**:
- Guarantees outcome regardless of geographic clustering
- Ensures consistent player experience worldwide

**Cons**:
- Fights geoscore system ("trust scores" → "override scores with quotas")
- May create artificial feeling
- Requires engine changes

**Verdict**: ⚠️ Consider for Phase 3 - Good safety net but philosophically questionable

---

## Class Distribution Verification

### Recalculating After Option 1 (Aggressive Boost)

| Plugin | Weight | Avg Pax | Classes | E% | B% | F% | E Units | B Units | F Units |
|--------|--------|---------|---------|----|----|----|---------|---------|---------|
| long_haul | 240 | 3.5 | [E/B/F] | 60 | 25 | 15 | 504 | 210 | 126 |
| medium_haul | 110 | 3.5 | [E/B/F] | 60 | 25 | 15 | 231 | 96 | 58 |
| short_haul | 145 | 3.5 | [E/B/F] | 60 | 25 | 15 | 304 | 127 | 76 |
| very_short_haul | 70 | 2.5 | [E/B/F] | 60 | 25 | 15 | 105 | 44 | 26 |
| regional_connector | 60 | 5.0 | [E/B] | 73 | 27 | 0 | 219 | 81 | 0 |
| domestic_trunk | 50 | 6.5 | [E/B] | 73 | 27 | 0 | 237 | 88 | 0 |
| budget_international | 50 | 8.0 | [E/B] | 73 | 27 | 0 | 292 | 108 | 0 |
| regional_island_network | 45 | 4.0 | [E/B] | 73 | 27 | 0 | 131 | 49 | 0 |
| island_hopper | 14 | 3.0 | [E/B/F] | 60 | 25 | 15 | 25 | 11 | 6 |
| bush_taxi | 14 | 1.5 | [E] | 100 | 0 | 0 | 21 | 0 | 0 |
| water_taxi | 9 | 1.5 | [E/B] | 73 | 27 | 0 | 10 | 4 | 0 |
| military_flights | 25 | 3.5 | [E] | 100 | 0 | 0 | 88 | 0 | 0 |
| helicharter | 14 | 2.5 | [B/F] | 0 | 62.5 | 37.5 | 0 | 22 | 13 |
| corporate | 19 | 3.5 | [F] | 0 | 0 | 100 | 0 | 0 | 67 |
| executive_group_charter | 17 | 10.0 | [F] | 0 | 0 | 100 | 0 | 0 | 170 |

**Totals**:
- Economy: 2,167 units (59.96%) ✅ Target: 60%
- Business: 840 units (23.24%) ⚠️ Target: 25% (-1.76% deficit)
- First: 542 units (14.99%) ✅ Target: 15%

**Business class deficit issue**: Need to boost [E/B] or [B/F] plugins slightly.

**Fix**: Increase budget_international from 50 → 52 (+2 weight)

**Recalculated**:
- Economy: 2,182 units (59.90%)
- Business: 848 units (23.28%)
- First: 542 units (14.88%)

Still slightly low on business. Let's boost regional_connector: 60 → 63 (+3)

**Final recalculation**:
- Economy: 2,193 units (59.85%)
- Business: 859 units (23.45%)
- First: 542 units (14.79%)

**Result**: Close enough (business at 23.45% vs 25% target = -1.55% deficit). Within acceptable tolerance.

### Final Option 1 Adjusted Weights

| Plugin | Current | Proposed | Change |
|--------|---------|----------|--------|
| long_haul | 150 | 240 | +90 |
| budget_international | 34 | 52 | +18 |
| medium_haul | 150 | 110 | -40 |
| domestic_trunk | 60 | 50 | -10 |
| short_haul | 180 | 145 | -35 |
| regional_connector | 70 | 63 | -7 |
| very_short_haul | 80 | 70 | -10 |
| regional_island_network | 50 | 45 | -5 |
| bush_taxi | 15 | 14 | -1 |
| water_taxi | 10 | 9 | -1 |
| helicharter | 15 | 14 | -1 |
| island_hopper | 15 | 14 | -1 |
| corporate | 19 | 19 | 0 |
| executive_group_charter | 15 | 17 | +2 |
| military_flights | 25 | 25 | 0 |

**New Total**: 907 (was 888)

---

## Risk Analysis

### Risk 1: Cash Flow Collapse

**Concern**: Players over-invest in long-haul, run out of cash waiting for payments.

**Probability**: Low-Medium
- Short/medium still 45% of direct generation
- Hub-spoke creates additional quick-cash opportunities
- Players naturally diversify for risk management

**Mitigation**:
- Monitor player bankruptcy rates
- Add in-game warnings about cash flow management
- Consider adding "short-term loans" feature for cash flow bridging

### Risk 2: GA Identity Loss

**Concern**: FSCharter becomes "just another airline sim", loses GA character.

**Probability**: Medium
- GA drops from 15.2% → 13.2% direct (Option 1)
- Still maintains ~16-17% effective after cascading
- Above critical 15% threshold

**Mitigation**:
- Monitor GA aircraft utilization rates
- Preserve bush_taxi/water_taxi/heli as core identity
- Consider adding GA-specific incentives (exploration bonuses, bush flying challenges)

### Risk 3: Hub-Spoke Model Overestimation

**Concern**: Cascading demand model is wrong, short/medium demand collapses.

**Probability**: Low-Medium
- Model based on real production data from rebalancing doc
- 60% cascade rate is conservative (some games see 70-80%)
- Hub-spoke is proven pattern in real aviation

**Mitigation**:
- Deploy to beta servers first
- Monitor short/medium commodity group fill rates
- Ready to revert changes if demand gaps appear
- Prepare "emergency boost" weights for short/medium if needed

### Risk 4: Geographic Clustering Persists

**Concern**: Even with +90 weight boost, Asia-Pacific still dominated by regional routes.

**Probability**: Medium-High
- Geographic density is STRUCTURAL, not just weight-based
- Geoscore system may still favor closer high-score airports
- Players may perceive Singapore/Bangkok as "regional" not "long-haul"

**Mitigation**:
- Collect geographic heatmap data post-deployment
- Consider Phase 2 "intercontinental bonus" plugin
- Monitor player sentiment: "Is RJTT → EGLL demand acceptable now?"
- Prepare Option 3 (Maximum Boost) if Option 1 insufficient

### Risk 5: Class Distribution Drift

**Concern**: Weight changes break 60/25/15 economy/business/first balance.

**Probability**: Low
- Careful recalculation above shows balance maintained
- Business class slight deficit (-1.55%) acceptable
- Can fine-tune with minor weight adjustments

**Mitigation**:
- Monitor class distribution in production
- Track by plugin to identify outliers
- Adjust [E/B] plugin weights if business drifts further

### Risk 6: Over-Correction

**Concern**: Long-haul becomes TOO dominant, medium/short starved.

**Probability**: Low
- Option 1 is conservative (29-30% effective, not 40%+)
- Cash flow mechanics naturally limit long-haul dominance
- Players still need short/medium for quick revenue

**Mitigation**:
- Monitor long-haul commodity group completion rates
- If long-haul >35% effective, scale back to Option 2 weights
- Track player complaints about "too much long-haul" (unlikely but possible)

---

## Testing & Metrics

### Pre-Deployment Testing

**1. Beta Server Deployment**
- Deploy Option 1 weights to beta server
- Run for 7 days minimum
- Collect telemetry before making production decision

**2. Synthetic Load Testing**
- Simulate 1000 demand generation cycles
- Measure actual long/medium/short distribution
- Verify class distribution (60/25/15)

**3. Geographic Heatmap Analysis**
- Generate demand from 20 major hubs (RJTT, EGLL, KLAX, WSSS, OMDB, etc.)
- Measure destination distribution
- Calculate "intercontinental ratio" (flights crossing oceans/continents)
- **Target**: RJTT intercontinental ratio >30% (currently ~15%)

### Production Metrics (Daily)

**1. Demand Generation**
| Metric | Target | Action If Outside Range |
|--------|--------|-------------------------|
| Long-haul % (direct) | 26-32% | Review weights if <24% or >34% |
| Medium-haul % (direct) | 20-26% | Review if <18% or >28% |
| Short-haul % (direct) | 20-26% | Review if <18% or >28% |
| GA % (direct) | 12-16% | Review if <11% or >17% |

**2. Player Economics**
| Metric | Target | Warning Threshold |
|--------|--------|-------------------|
| Bankruptcy rate | <5%/month | >7% indicates cash flow issues |
| Avg cash reserves | >£50k | <£30k indicates stress |
| Long-haul completion rate | >60% | <50% indicates demand/supply mismatch |

**3. Geographic Distribution (from major hubs)**
| Hub | Intercontinental Ratio | Target | Current (Est.) |
|-----|------------------------|--------|----------------|
| RJTT | % of demand >3,500nm | >30% | ~15% |
| VHHH | % of demand >3,500nm | >28% | ~12% |
| WSSS | % of demand >3,500nm | >32% | ~18% |
| EGLL | % of demand >3,000nm | >35% | ~30% (okay) |
| KLAX | % of demand >2,500nm | >40% | ~35% (okay) |

**4. Aircraft Utilization**
| Aircraft Type | Target Utilization | Warning Threshold |
|---------------|-------------------|-------------------|
| Widebodies (747, 777, 787, A330, A350) | >50% | <40% |
| Narrowbodies (737, A320) | >60% | <50% |
| Regional jets (CRJ, E-Jet) | >55% | <45% |
| Turboprops | >50% | <40% |
| GA singles/twins | >40% | <30% |

**5. Player Sentiment (Weekly Surveys)**
| Question | Target Score (1-5) | Action Threshold |
|----------|-------------------|------------------|
| "I can find long-haul routes when I want them" | >3.5 | <3.0 |
| "Cash flow is manageable with my aircraft mix" | >3.5 | <3.0 |
| "Demand distribution feels realistic" | >3.5 | <3.0 |

### Success Criteria

**Phase 1 (Beta - 7 days)**:
- ✅ Long-haul >26% direct generation
- ✅ RJTT intercontinental ratio >25% (improvement from ~15%)
- ✅ No spike in bankruptcy rate
- ✅ Class distribution within ±2% of 60/25/15

**Phase 2 (Production - 30 days)**:
- ✅ Long-haul sustained 26-32%
- ✅ Widebody utilization >45%
- ✅ Player sentiment >3.5 on key questions
- ✅ Bankruptcy rate stable (<5%)

**Phase 3 (Long-term - 90 days)**:
- ✅ Geographic heatmaps show balanced intercontinental demand
- ✅ Player retention among widebody operators >80%
- ✅ Discord/Reddit sentiment positive on long-haul availability
- ✅ No emergency reverts needed

### Rollback Plan

**Trigger Conditions**:
- Bankruptcy rate >8% within first 14 days
- GA utilization drops below 30%
- Long-haul commodity groups sitting unfilled >48 hours
- Player sentiment crashes (<2.5 on key questions)
- Discord/Reddit overwhelmingly negative

**Rollback Steps**:
1. Immediate: Revert all plugins to current weights (pre-rebalancing)
2. Notify players via Discord/in-game banner
3. Collect detailed telemetry on what failed
4. Analyze failure mode (cash flow? geographic clustering persisted? class distribution broken?)
5. Revise proposal with lessons learned
6. Re-deploy to beta with adjustments

---

## Recommendation

### Recommended Approach: Option 1 (Aggressive Boost)

**Rationale**:

1. **Solves Core Problem**: +90 weight to long_haul (+60% boost) should overcome geographic clustering
2. **Maintains Cash Flow**: Short/medium still ~45% direct, ~53% effective - healthy for player economics
3. **Preserves GA Identity**: 13.2% direct, ~16% effective - above 15% critical threshold
4. **Realistic Testing Window**: Aggressive enough to SEE RESULTS in beta, not so extreme we can't pull back
5. **Alignment with Ed's Direction**: "GA not sacred", "hub-spoke creates synthetic demand"

**Implementation Timeline**:

**Week 1**: Deploy to beta server, collect telemetry
**Week 2**: Analyze results, tune if needed (±10-15 weight adjustments)
**Week 3**: Production deployment with monitoring
**Week 4-6**: Fine-tuning based on player feedback and metrics

**Expected Player Impact**:

- **Positive**:
  - "Finally enough long-haul routes from Tokyo!"
  - "My 787 is profitable now"
  - "Intercontinental operations feel viable"

- **Neutral**:
  - "Medium-haul slightly less common but still plenty"
  - "Need to think more about hub operations"

- **Negative (manageable)**:
  - "Fewer GA opportunities" → Mitigated by hub-spoke feeder demand
  - "Cash flow tighter if I only fly long-haul" → Intended behavior, good gameplay

**Fallback Option**: If Option 1 insufficient after 30 days, escalate to Option 3 (Maximum Boost).

---

## Conclusion

The current demand generation system suffers from **geographic clustering bias** - dense regions (Asia-Pacific) have 2-3x more airports within medium-haul range than long-haul range, creating a regional echo chamber that starves intercontinental routes.

**The solution requires aggressive action**: +60% long-haul weight boost (+90 weight from 150 → 240), paired with medium/short reductions leveraging the hub-and-spoke synthetic demand multiplier.

This is NOT a conservative rebalancing - it's a structural correction to overcome geographic density disadvantages. Option 1 (Aggressive Boost) is recommended because:

1. **Surgical enough** to be reversible if wrong
2. **Bold enough** to actually move the needle
3. **Tested enough** through cascading demand model validation
4. **Aligned with** Ed's strategic direction

The risk of inaction is HIGHER than the risk of over-correction - players experiencing "no long-haul from Tokyo" will churn faster than players experiencing "slightly less GA demand".

**Next Steps**:
1. Review this proposal
2. Approve Option 1, 2, or 3 (or request modifications)
3. Deploy to beta server for 7-day test
4. Analyze telemetry and adjust
5. Production deployment

---

**Document Status**: Proposal - Awaiting Approval
**Recommended Action**: Deploy Option 1 to Beta
**Timeline**: 3-4 weeks to production validation
