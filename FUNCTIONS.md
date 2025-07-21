# Functions

This document provides an overview of the primary commands, API functions, and services offered by the Celestial Unification Framework (v10.3.0 "Integrity Edition").

## CLI Commands

- **`celestial sim:health`**
  - Performs comprehensive health checks including FFI integrity, database writability, configuration validity, and PCNTL availability.
  - Supports `--json` output for integration with monitoring systems.

- **`celestial run [options]`**
  - Launches a simulation session.
  - **Options:**
    - `--allow-degraded` (boolean) — Permit fallback to `NullQuantumKernel` when FFI kernel is missing.
    - `--nodes <int>` — Number of nodes to simulate (validated range enforced).
    - `--cycles <int>` — Number of simulation cycles (validated range enforced).
    - `--seed <int>` — PRNG seed value (validated range enforced).
    - `--init-checkpoint <path>` — Initialize or reset checkpoint storage.

- **Deprecated**: `celestial --no-fallback` (removed in v10.3.0)

## Core API Functions

### `KernelBridge::verifyIntegrity(string $libraryPath, string $checksum, ?string $signaturePath = null): bool`
Verifies the integrity of the native FFI library by checking its SHA-256 checksum and optional GPG signature. Throws an exception on mismatch.

### `SimulationConfig::getInstance(): SimulationConfig`
Retrieves the singleton configuration instance, ensuring a consistent environment across services.

### `CheckpointRepositoryInterface`
Defines methods for managing simulation state:

- `init(string $path): void` — Initialize new checkpoint storage.
- `saveState(array $state): void` — Atomically persist full PRNG and simulation state.
- `loadState(): array` — Retrieve and validate last checkpoint state.

## Services & Helpers

- **`env(string $key): string`**
  - Fetches environment variables, aborting startup if a mandatory key is missing.

- **`MonologRotatingFileHandler`**
  - Configured for automatic log rotation, file count limits, and sanitized exception output.

- **`RandomizerEngine`**
  - Wraps PHP 8.2+ native `\Random\Randomizer`, with full state persistence support.

## Testing & Analysis

- **Pest Test Suite**
  - Executes unit tests for domain models and integration tests for persistence layers via `./vendor/bin/pest`.

- **Static Analysis**
  - Uses `phpstan.neon` at level 9 to enforce coding standards and catch type errors.

## Utility Scripts

- **`scripts/generate-checksum.sh`**
  ```bash
  #!/usr/bin/env bash
  sha256sum "$1"
  ```
  Generates the SHA-256 checksum for a given FFI library file.

- **`scripts/monitor-health.sh`**
  ```bash
  #!/usr/bin/env bash
  celestial sim:health --json | jq
  ```
  Monitors system health in CI/CD or cron jobs.

---
*Last updated: 2025-07-21*
