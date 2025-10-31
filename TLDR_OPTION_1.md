# TLDR: Long-Haul Rebalancing (Option 1)

**Date**: 2025-11-01

## The Problem

Current demand from major hubs (e.g., Tokyo) shows **heavy regional clustering** (Philippines, Indonesia, Thailand) but **sparse intercontinental** (US, Europe). Players report insufficient long-haul demand for widebody operations.

**Root Cause**: Geographic density bias - Asia-Pacific has 2-3x more airports within medium-haul range (800-2,200nm) than true long-haul range (2,200+nm). Even with equal plugin weights, medium-haul wins due to more eligible destinations.

## The Solution

**Boost long-haul by 60%**, reduce medium/short-haul to compensate, leveraging the fact that hub-and-spoke networks CREATE synthetic short/medium demand through feeder operations.

## What Changed

| Plugin | Before | After | Change |
|--------|--------|-------|--------|
| **long_haul** | 150 | **240** | **+60%** |
| budget_international | 34 | 52 | +53% |
| medium_haul | 150 | 110 | -27% |
| short_haul | 180 | 145 | -19% |
| Others | - | - | Minor trims |

**Total weight**: 887 (was 888)

## Expected Results

- **Long-haul demand**: 18.8% → **~29.5%** effective
- **Widebody utilization**: 42% → **~64%** (viable for first time)
- **Tokyo intercontinental**: ~15% → **~34%** of demand
- **GA preserved**: 14.6% (above 13% identity threshold)
- **Cash flow**: Healthy (52.5% short/medium maintains quick payment cycles)

## Player Impact

**Before**: "My 777 sits idle, not enough long-haul routes"
**After**: "Can fly long-haul 2x/week profitably, balanced with regional operations"

## Next Steps

1. Deploy to beta server for 1-2 weeks
2. Monitor widebody utilization (target: >60%)
3. Check geographic heatmaps (RJTT intercontinental ratio >30%)
4. If insufficient, escalate to Option 3 (+40 more weight)
5. If successful, declare victory and monitor long-term

## Risk Mitigation

- Conservative approach (can escalate to Option 3 if needed)
- GA identity safe (14.6% vs 13% threshold)
- Cash flow protected (52.5% short/medium for quick payments)
- Rollback plan ready if issues arise

**Philosophy**: Start conservative, measure real results, escalate only if warranted.
