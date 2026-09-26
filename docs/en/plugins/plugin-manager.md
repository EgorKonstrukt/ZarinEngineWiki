# Plugin Manager

Discovery, ordering, security and teardown for everything pluggable. One class, `PluginManager`, three plugin tiers: built-in `.py`, native binaries, signed `.zplugin` packages.

Discovery and order:

- System plugins load from `plugins/` first, user plugins from `plugins/user/` after. Registration order appends to `_load_order`; teardown walks it in reverse so dependents release before their dependencies.
- `get_system_plugins()` filters the tier; iteration helpers yield instances in load order for frames (`step`/`pre_step`) and event fan-out (scene, play, window hooks).
- Replacing a plugin (same `NAME`, new path) swaps the entry in place instead of duplicating — hot iteration without restarts for pure-Python plugins in some flows.

Native binaries:

- `.dll` / `.so` entries load through the native path with `_native_plugin_path` recorded on the instance; a sibling `<name>_resources` folder auto-resolves as `resource_dir`.
- Bundled dependency directories activate per plugin (`_activate_bundled_libs`) so two plugins can ship conflicting library versions without poisoning each other.

`.zplugin` security chain, verified step by step:

1. `verify_zplugin_file` checks the manifest (name, version, `dependencies` list with missing-dependency reports), indexes every entry, and rejects unindexed native modules (`_native/` discipline for `.pyd`/`.so`/`.dll`).
2. Signatures verify against trusted keys from `config/trusted_keys` (plus an optional configured dir); without keys configured, verification reports "no trusted keys configured" instead of silently passing.
3. `_zplugin_fingerprint` (sha256 + size + mtime) gates the extraction cache: unchanged packages reuse the extracted tree, changed ones re-extract.
4. Manifest `dependencies` resolve (including bundled Cython builds via `cython_modules`); missing ones log explicitly and block activation.

Configuration: per-plugin security knobs (`check_updates_on_startup`, `trusted_keys_dir`) feed from the plugin security config; per-plugin data persists through the base-class config load/save.

Failure posture: a broken plugin logs and skips — one bad apple never prevents editor startup. Check the console first; the plugin manager panel mirrors load state, order and errors.

Related: PluginBase Lifecycle, .zplugin Packaging, User Plugins guide, System Plugins catalog.
