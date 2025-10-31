# Increase Long-Haul Weight from 120 to 150

## Summary

This PR increases the long-haul plugin weight by 25% (120 → 150) to address player demand for more widebody opportunities while maintaining healthy cash flow distribution and platform identity.

**Single Change:**
- `long_haul.jsonplugin`: Weight 120 → 150

**Impact:**
- Effective widebody coverage: 16.55% → 18.80% (+13% improvement)
- Class distribution maintained within ±1% (no other changes needed)
- Total active weight: 858 → 888

---

## Problem Statement

**Player Feedback:** "A lot of people want to fly widebodies"

- 747, 777, 787, A330, A350 are iconic, aspirational aircraft
- Long-haul international flying is the dream for many sim pilots
- Current 16.55% effective coverage feels restrictive

However, simply maximizing long-haul weight creates financial gameplay problems.

---

## The Cash Flow Constraint

### FSCharter's Payment Model

From the help docs:
> Passengers pay **only on passenger's final destination arrival**

This creates a critical constraint for long-haul operations:

**Typical Long-Haul Journey:**
1. **Leg 1 (GA pickup)**: Fly passengers to hub → **no payment yet**
2. **Leg 2 (long-haul)**: Fly hub → destination hub → **no payment yet**
3. **Leg 3 (GA dropoff)**: Fly to final destination → **FINALLY GET PAID**

**Cash Flow by Route Type:**
- **Short-haul (270-800nm)**: Get paid in 1-2 hours ✅💰
- **Medium-haul (800-2,200nm)**: Get paid in 3-4 hours ✅💰
- **Long-haul (2,200+nm)**: Get paid in 6-12+ hours (multi-leg aggregation) ⚠️💰

**Key Insight:** If long-haul dominates demand, players face cash flow problems waiting for those big payouts.

---

## Analysis: Why 150 is the Sweet Spot

### Scenarios Analyzed

We modeled long-haul weights from 120 to 250 using cascading demand calculations:

| Scenario | Effective WB% | GA% | Cash Flow Impact | Assessment |
|----------|---------------|-----|------------------|------------|
| **120 (baseline)** | 16.55% | 21.18% | Best cash flow | "Not enough long-haul!" ❌ |
| **150 (chosen)** | **18.80%** | **20.83%** | **Good balance** ✅ | **"Decent long-haul available"** ✅ |
| 180 | 20.84% | 20.51% | Moderate delays | Cash flow pressure ⚠️ |
| 200 | 22.10% | 20.32% | Significant delays | GA near threshold ⚠️⚠️ |
| 250 | 24.94% | 19.88% ❌ | Major delays | Platform identity shift ❌ |

### Why 150?

**1. Player Satisfaction:**
- 18.80% effective widebody coverage (up 13% from baseline)
- Addresses "not enough long-haul" feedback
- Keeps long-haul aspirational and rewarding (not overwhelming)
- Players see ~1 widebody opportunity per 5 missions (was 1 per 6)

**2. Cash Flow Balance (Critical):**
- 60%+ missions remain short/medium-haul for fast income
- Long-haul becomes a strategic choice, not a cash flow trap
- New players can build capital before tackling long-haul
- Avoids financial pressure to fly long-haul despite slower payouts

**3. Platform Identity Maintained:**
- GA coverage: 20.83% (stays above 20% threshold)
- FSCharter remains GA/charter-focused, not airline sim
- Short-haul fast cash remains prevalent
- Balanced progression: GA → Regional → Long-haul

**4. Real-World Demand Distribution:**
- Production shows 4-8 passenger groups per destination
- Demand already highly distributed across 32k+ airports
- Widebodies require extensive network building regardless of weight
- Natural constraint via passenger aggregation effort

### Why NOT Higher (180, 200, 250)?

**Scenario 180 (+60):**
- ⚠️ Creates moderate cash flow delays
- ⚠️ GA drops to 20.51% (approaching threshold)
- Risk: Players feel pressure to fly long-haul despite slower income

**Scenario 200 (+80):**
- ⚠️ Significant cash flow delays (hard to stay solvent)
- ⚠️ GA drops to 20.32% (dangerously close to 20% threshold)
- Risk: Platform identity shifts toward airline operations

**Scenario 250 (+130):**
- ❌ GA drops to 19.88% (below 20% threshold - UNACCEPTABLE)
- ❌ Widebody becomes 24.94% (nearly 1 in 4 missions)
- ❌ Major cash flow problems (players wait ages for payment)
- ❌ Platform becomes airline-focused, not GA/charter

---

## Hub-and-Spoke Cascading Demand Model

### How the Model Works

FSCharter's collaborative hub-and-spoke network creates **cascading demand** where long-haul flights generate feeder traffic:

**Key Insight:** 1 long-haul passenger needs 3 flight segments:
1. **Pickup**: Home → Hub (GA/regional/short-haul)
2. **Long-haul**: Hub → Destination Hub (widebody)
3. **Dropoff**: Destination Hub → Final destination (GA/regional/short-haul)

This is a **MULTIPLIER effect** - each long-haul mission creates GA/regional work.

### Cascading Analysis for Weight = 150

**Long-haul creates feeder demand:**
- Base weight: 150
- Average passengers: 3.5
- Cascade rate: 60% (60% of pax need feeder flights)
- Feeder demand created: **315 passenger-units**

**This distributes to:**
- Regional Connectors: 30% → +22.7 effective weight
- Short Haul: 25% → +27.0 effective weight
- Very Short Haul: 20% → +30.2 effective weight
- Corporate: 10% → +10.8 effective weight
- Helicharter: 7% → +7.6 effective weight
- Bush/Water Taxi: 8% → +10.3 effective weight

**Result:**
- Total effective weight: 979 (vs 888 direct) = **1.10x multiplier**
- GA/regional aircraft benefit from cascade (feeder positioning flights)
- Widebody coverage slightly diluted by feeder generation (20.72% direct → 18.80% effective)

### Why This Validates 150

The cascading model shows:
1. **Long-haul creates GA work** - higher long-haul weight actually boosts GA opportunities
2. **Effective widebody coverage lower than direct** - suggests we can afford higher weight
3. **Natural balance emerges** - hub-and-spoke dynamics self-regulate
4. **Business jets benefit too** - corporate flights get cascade boost (+10.8 weight)

---

## Impact on Class Distribution

**Good news:** Class distribution remains within ±1% tolerance with NO other plugin changes needed.

| Class | Target | Before (120) | After (150) | Delta |
|-------|--------|--------------|-------------|-------|
| Economy | 60.00% | 60.63% | **60.58%** | -0.05% ✅ |
| Business | 25.00% | 24.61% | **24.70%** | +0.09% ✅ |
| First | 15.00% | 14.76% | **14.72%** | -0.04% ✅ |

**Why no adjustments needed?**

The system uses **weighted cabin selection (60:25:15)** rather than plugin-based balancing. When plugins include [E/B/F], the engine automatically maintains target distribution. Long-haul has [E/B/F], so increasing its weight doesn't skew class balance.

---

## Aircraft Market Coverage Impact

### Direct Coverage (Plugin Weights)

| Aircraft Type | Before (120) | After (150) | Change |
|---------------|--------------|-------------|--------|
| **Widebodies** | 17.95% | **20.72%** | **+2.77%** ↑ |
| Narrowbodies | 17.48% | 16.89% | -0.59% |
| Regional Jets | 36.13% | 34.91% | -1.22% |
| GA (Singles/Twins) | 19.81% | 19.14% | -0.67% |
| Business Jets | 5.71% | 5.52% | -0.19% |

### Effective Coverage (After Cascading)

| Aircraft Type | Before (120) | After (150) | Change |
|---------------|--------------|-------------|--------|
| **Widebodies** | 16.55% | **18.80%** | **+2.25%** ↑ |
| Narrowbodies | 16.12% | 15.33% | -0.79% |
| Regional Jets | 36.88% | 35.91% | -0.97% |
| GA (Singles/Twins) | 21.18% | **20.83%** | -0.35% ✅ |
| Business Jets | 6.58% | **6.57%** | -0.01% ✅ |

**Key Takeaways:**
- ✅ Widebody opportunity increases 13% (18.80% vs 16.55%)
- ✅ GA stays healthy at 20.83% (above 20% threshold)
- ✅ Business jets maintained at 6.57% (good variety)
- ✅ All aircraft types remain viable

---

## Real-World Demand Distribution Context

Production data shows demand is **already highly distributed**:

### Observed Pattern (from screenshots)

**Major Hubs (FAOR, SCEL, VTBS):**
- 4-8 passenger groups per destination
- Dozens of different destinations from each hub
- Requires network building to aggregate passengers

**What This Means:**
- **Singles/Twins**: Find 2-4 passengers → fly immediately ✅
- **Turboprops**: Find 6-10 passengers → moderate coordination
- **Regional Jets**: Find 20-50 passengers → need network building
- **Narrowbodies**: Find 70-150 passengers → serious hub operations required
- **Widebodies**: Find 180-400 passengers → EXTENSIVE hub network + multi-leg coordination

**Conclusion:** The work to fill a widebody is **naturally constrained** by passenger aggregation requirements. Increasing long-haul weight to 150 provides more opportunities without overwhelming the system - widebodies still require significant player effort to operate profitably.

---

## Testing & Validation Plan

### Pre-Deployment Validation

**1. Class Distribution Check:**
```python
# Run validation script
python3 /tmp/long_haul_rebalancing_analysis.py

# Expected results:
# Economy:  60.58% (target 60.00%, deviation: +0.58%) ✓ PASS
# Business: 24.70% (target 25.00%, deviation: -0.30%) ✓ PASS
# First:    14.72% (target 15.00%, deviation: -0.28%) ✓ PASS
```

**2. Weight Calculations:**
- Total active weight: 888 ✓
- All plugins sum correctly ✓
- Widebody coverage: 20.72% direct, 18.80% effective ✓

### Recommended Production Testing

**Phase 1: Generate Test Demand (1-2 hours)**
1. Generate 10,000 test missions
2. Verify class distribution: 60/25/15 ±1%
3. Verify widebody frequency: ~18.8% (effective after cascade)
4. Check for unexpected patterns

**Phase 2: Soft Launch (1 week)**
1. Deploy to beta/test environment
2. Monitor player behavior and feedback
3. **Track cash flow patterns** - are players staying solvent?
4. Measure widebody mission acceptance rate

**Phase 3: Full Deployment (2 weeks)**
1. Production deployment
2. Monitor key metrics:
   - Class distribution (telemetry)
   - Widebody utilization
   - **Player cash flow health** (critical)
   - Mission completion rates by distance
3. Gather player feedback via Discord/surveys

**Phase 4: Adjustment (if needed)**
1. If widebody demand still too low: Consider 150 → 180
2. **If cash flow issues emerge**: Revert to 120-130
3. If class distribution drifts: Adjust economy-heavy plugins
4. Likely range: 120-180 based on real-world data

### Success Metrics

**Must Have (Critical):**
- ✅ Class distribution within ±1%
- ✅ No game-breaking bugs
- ✅ **Player cash flow remains healthy** (no mass bankruptcies)

**Should Have (Important):**
- ✅ Positive feedback on widebody availability
- ✅ Widebody missions accepted at reasonable rate
- ✅ GA missions still popular
- ✅ Business jet progression viable

**Nice to Have (Desirable):**
- ✅ Natural hub-and-spoke patterns emerge
- ✅ Player collaboration increases
- ✅ Regional diversity in demand

---

## Rollback Plan

If critical issues arise (especially **cash flow problems**):

**Quick Rollback (< 5 minutes):**
```bash
# Revert this commit
git revert d724a99
git push

# Or manual fix:
# long_haul.jsonplugin: Weight 150 → 120
```

**Why We Might Rollback:**
1. **Cash flow crisis**: Players going bankrupt waiting for long-haul payouts
2. **Class distribution drift**: Telemetry shows >1% deviation
3. **Widebody oversaturation**: Hub congestion issues
4. **Player feedback**: "Too much long-haul, not enough variety"

**Monitoring for 2 weeks post-launch is critical.**

---

## Conclusion

**Recommended: long_haul weight 120 → 150**

This strikes the optimal balance between:
- ✅ Player demand for aspirational widebody content
- ✅ Financial gameplay requiring healthy cash flow
- ✅ Platform identity as a GA/charter simulator
- ✅ Natural gameplay progression

**Key Decision Factors:**

1. **Financial Reality First**: Cash flow constraints are real and impact player experience
2. **Data-Driven**: Cascading demand model shows 150 is sustainable
3. **Conservative Approach**: 13% improvement addresses feedback without overcorrecting
4. **Reversible**: Easy to adjust up/down based on production telemetry

The 25% increase provides meaningful improvement in widebody availability while maintaining the financial and gameplay balance that makes FSCharter unique.

---

**Analysis Scripts:**
- `/tmp/long_haul_rebalancing_analysis.py` - Full scenario modeling
- `/tmp/cascading_demand_model.py` - Hub-and-spoke calculations

**Documentation:**
- `REBALANCING_2025.md` - Complete rationale and decision history

**Date:** 2025-10-28
**Branch:** feature/long-haul-boost-150
