# How to See Plugin Changes in FSC Demand Manager

## Critical: Plugins Are Bundled at Compile Time

The demand manager app **does NOT read plugins at runtime**. Instead:

### How It Works

```rust
// From: fsc-demand-manager/src-tauri/src/demand/plugin_loader.rs:14
static PLUGIN_DIR: Dir<'static> =
  include_dir!("$CARGO_MANIFEST_DIR/../../../../demand-engine-plugins");
```

**What this means**:
1. Plugins are **embedded into the binary** when you compile the Rust app
2. The macro reads from: `/Users/edwardwoodcock/projects/active/fscharter/demand-engine-plugins`
3. Changes to `.jsonplugin` files **won't appear** until you rebuild

### To See Your Changes

**You MUST recompile the Tauri app:**

```bash
cd /Users/edwardwoodcock/projects/active/fscharter/geo-score/worktrees/fsc-demand-manager

# Development build (faster, for testing)
npm run tauri dev

# OR production build (slower, optimized)
npm run tauri build
```

**Development mode** (`tauri dev`):
- Plugins bundled at startup
- Hot-reload for TypeScript/React changes
- **Rust changes require restart** (Ctrl+C, then `npm run tauri dev` again)

**Production build** (`tauri build`):
- Full compilation
- Standalone binary created
- Plugins permanently embedded

### Why This Design?

**Security & Performance**:
- No filesystem access at runtime (safer)
- Instant plugin loading (no I/O)
- All validation done at compile time
- Can't load malicious plugins after deployment

### Quick Test Workflow

```bash
# 1. Make your plugin changes
vim /path/to/demand-engine-plugins/long_haul.jsonplugin

# 2. Navigate to demand manager
cd /Users/edwardwoodcock/projects/active/fscharter/geo-score/worktrees/fsc-demand-manager

# 3. Rebuild Rust (plugins will be re-bundled)
npm run tauri dev

# 4. App opens with new plugin weights
# Test your changes...

# 5. If you need to tweak again:
# Ctrl+C (stop dev server)
# Edit plugins again
# npm run tauri dev (restart with new bundle)
```

### Verification

The app has a test that verifies plugin count:

```rust
// Should match number of active plugins
assert_eq!(count, 31, "Expected 31 plugins, found {}", count);
```

After your changes, this test may need updating if plugin count changed (we kept it at 15 active plugins, so total including disabled should still be 31).

---

**TL;DR**: Edit plugins → Rebuild Tauri app → See changes
