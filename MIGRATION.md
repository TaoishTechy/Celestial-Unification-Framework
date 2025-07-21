# Migration Guide v10.2 → v10.3

This guide walks you through the breaking changes and improvements introduced in version **10.3.0** of the Celestial Unification Framework, ensuring a smooth transition from **10.2.0**.

---

## Environment Variable Changes

### New mandatory variables

- `FFI_LIBRARY_PATH`
- `FFI_LIBRARY_CHECKSUM`
- `DEFAULT_CHECKPOINT_PATH`
- `LOG_PATH`

### New optional variables

- `FFI_SIGNATURE_PUBLIC_KEY_PATH`
- `ALLOW_DEGRADED_PHYSICS`

### Action Required

1. Update your `.env` (or environment) to include **all mandatory** variables.
2. If you’re using the optional features, add the optional variables as needed.
3. To generate the checksum for your FFI library, run:

   ```bash
   sha256sum /path/to/your/ffi/library.so
   ```

---

## CLI Changes

- **New flag**: `--allow-degraded`
  - Allows running with degraded physics when the FFI kernel is missing.

- **Deprecated flag**: `--no-fallback`
  - Removed in **v10.3.0**.
  - By default (without `--allow-degraded`), the CLI now **aborts** if the FFI kernel cannot be loaded.

- **Enhanced validation**
  - All numeric inputs (`--nodes`, `--cycles`, `--seed`, etc.) are now strictly validated and will error out if they fall outside supported ranges.

---

## Checkpoint Database

- **No schema migration needed**.
  - The on-disk checkpoint format remains compatible at the schema level.

- **Backward incompatibility warning**
  - Checkpoints created by **v10.3.0** **cannot** be read by **v10.2.0** or earlier.

---

## Recommendation

After upgrading, initialize a fresh checkpoint to avoid read/write issues:

```bash
# Replace with your application entrypoint
your_app --init-checkpoint /path/to/new/checkpoint.db
```

---

## Summary

- **Update** your environment variables.
- **Adjust** CLI usage (remove `--no-fallback`, use `--allow-degraded` when needed).
- **Reinitialize** your checkpoint storage to ensure compatibility.

By following these steps, you’ll transition seamlessly to **v10.3.0** of the Celestial Unification Framework. If you encounter any issues, please consult the documentation or reach out to the maintainers.
