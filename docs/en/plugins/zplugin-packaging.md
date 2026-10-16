# .zplugin Packaging

Signed distributable plugin format: one file carrying code, native modules, bundled libraries and a manifest — install by dropping it in, verified before it ever executes.

Package anatomy:

- Manifest: name, version, entry description, `dependencies` list (other plugins or libraries with versions), optional `cython_modules` for bundled native builds.
- Code entries: `.py` modules loaded through the standard factory convention.
- Native entries: `.pyd` / `.so` / `.dll` under the `_native/` discipline — the verifier rejects unindexed native modules outright.
- `libs/` bundled dependencies activated per plugin, isolated from other plugins' versions.

Build tools: `tools/build_plugin.py` assembles a plain plugin directory; `tools/build_zplugin.py` compiles native parts (Cython via cythonize when configured), packs the manifest, code, natives and libs into the `.zplugin` archive.

Install and verification flow:

1. Drop the file where the manager scans (or install from the plugin package panel).
2. `verify_zplugin_file` (`core/foundation/plugins/discovery.py:80`) validates the manifest, reports missing dependencies by name, and checks the entry index; payload files re-checked by `_verify_payload_files` (hash mismatch → `modified file`, unindexed `.pyd`/`.so`/`.dll` outside `_native/` rejected).
3. Signatures (`verify_manifest_signature`) verify against `config/trusted_keys` `*.pub` files (64-hex ed25519 keys, plus optional `trusted_keys_dir`); policy `allow_unsigned` (`warn` default, `strict` blocks): with no keys configured verification reports `no trusted keys configured` instead of silently passing.
4. `_zplugin_fingerprint` (`core/foundation/plugins/ui_registry.py:130`, sha256 + size + mtime) consults the extraction cache — reinstalls of unchanged files cost nothing.
5. Activation resolves dependencies, mounts bundled libs, loads the entry.

Updating: bump the manifest version, rebuild, redistribute — the fingerprint changes, the cache re-extracts, dependents re-resolve. Keep `NAME` stable across versions or configs orphan.

The repo ships an example artifact: `plugins/mcp_pack-2.1.1.zplugin` — inspect it (as an archive) to see a real manifest, native layout and versioning scheme.

Troubleshooting: refused unsigned → sign with a trusted key or add the key; missing dependency → install the named package first; stale behavior after rebuild → fingerprint unchanged (mtime granularity), touch or version-bump; native load failure → architecture mismatch (win `.pyd`/`.dll` vs linux `.so`).

Related: Plugin Manager (verification chain), User Plugins guide (what to pack), System Plugins catalog.
