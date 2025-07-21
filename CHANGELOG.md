# Changelog

All notable changes to the Celestial Unification Framework are documented in this file.

## [10.3.0] - 2025-07-21 "Integrity Edition"

### Added
- **FFI Library Integrity Verification**: The KernelBridge now verifies the SHA-256 checksum and a GPG-style signature of the native `libcelestial_kernel.so`, preventing supply-chain attacks.
- **Deep Health Checks**: The `sim:health` command now performs comprehensive checks on FFI integrity, database writability, configuration validity, and PCNTL availability, with `--json` output for operational monitoring.
- **Explicit Scientific Degradation Flag**: The `NullQuantumKernel` fallback is only activated with the `--allow-degraded` CLI flag, defaulting to abort if the native kernel is unavailable.
- **Comprehensive Test Suite**: A testing harness using Pest includes unit tests for domain models and integration tests for the persistence layer.
- **Static Analysis Configuration**: A `phpstan.neon` file is included, configured for level 9 analysis across the codebase.

### Changed
- **Singleton Configuration**: `SimulationConfig` is now a singleton service within the DI container, ensuring consistent configuration state.
- **Full PRNG State Persistence**: Checkpoints save and restore the complete internal state of the `\Random\Randomizer` engine for deterministic resumption.
- **Atomic & Validated Checkpointing**: The `SqliteCheckpointRepository` uses WAL mode, busy timeouts, and atomic `BEGIN IMMEDIATE TRANSACTION` wrappers to prevent data corruption.
- **Hardened Configuration Loading**: The `env()` helper enforces critical environment variables, aborting on startup if missing.
- **Robust Log Rotation**: The `Monolog RotatingFileHandler` is configured with filename patterns and file limits to prevent disk usage issues.
- **Sanitized Logging**: Exception logging redacts sensitive file paths by default.
- **Validated CLI Inputs**: Numeric CLI options (`--nodes`, `--cycles`, `--seed`) are rigorously validated.
- **PRNG Upgrade**: Uses PHP 8.2+'s native `\Random\Randomizer`, removing dependency on `random-lib/random`.

### Fixed
- **Graceful Shutdown Determinism**: The SIGINT signal handler immediately persists PRNG state to a temporary file, restored first upon resumption.
- **FFI `__call` Allow-List**: Reinstated security allow-list for FFI function calls in the KernelBridge.
- **SQLite Connection Handling**: The `SqliteCheckpointRepository` correctly implements `connect()` and `load()` methods and closes handles on `__destruct`.
- **PCNTL Availability Check**: Signal handler registration checks for the `pcntl` extension, warning users if unavailable.
- **Seed Collision Vulnerability**: Default seed for new simulations uses `random_bytes`, eliminating seed collision risks.

## [10.2.0]

### Added
- **FFI Integrity Verification**: Verifies SHA-256 checksum of `libcelestial_kernel.so` to prevent supply-chain attacks.
- **`--no-fallback` CLI flag**: Allows simulations to abort if the native kernel is unavailable.
- **Comprehensive Health Checks**: The `sim:health` command checks FFI library availability, database writability, and configuration validity.
- **Log Rotation**: Uses `RotatingFileHandler` to prevent log growth and disk exhaustion.
- **Externalized Checkpoint Validation Schemas**: Allows schemas to be versioned independently.

### Changed
- **Hardened Configuration Loading**: Improved environment variable handling to prevent null values and abort if required variables are missing.
- **Sanitized Logging**: Exception traces are sanitized before logging to avoid leaking sensitive file paths.
- **PRNG Upgrade**: Upgraded to PHP 8.2's native `\Random\Randomizer` for security.
- **Default PRNG Seed Generation**: Now uses cryptographically secure random bytes to reduce seed collisions.

### Fixed
- **Atomic Checkpointing**: All SQLite writes wrapped in `BEGIN IMMEDIATE TRANSACTION` to prevent race conditions and partial writes.
- **Immediate PRNG State Save on SIGINT**: Persists PRNG state to a temporary file immediately upon SIGINT for deterministic resumption.

## [10.1.0] "Perfection Edition"

### Added
- **`SimulationConfig` value object**: Decouples configuration from the domain layer.
- **Versioned & Validated Checkpoints**: Checkpoints include metadata and are validated against a JSON schema.
- **Deterministic Resumption**: PRNG state saved and restored with checkpoints for reproducible simulations.
- **Graceful Shutdown**: Handles SIGINT (Ctrl+C) to gracefully halt simulations and save final checkpoints.
- **`sim:health` CLI command**: Verifies operational readiness.
- **Observability**: Simple metrics collection system for monitoring.
- **Bootstrapped Test Suite**: Example unit and integration tests using Pest.
- **Static Analysis**: Configured `phpstan.neon` for level 9 static analysis.

### Changed
- **Decoupled Domain from CLI options**: Domain layer no longer directly accesses CLI options.
- **Improved DI container**: Fully configured with explicit service definitions and robust fallbacks (e.g., `NullQuantumKernel`).

### Fixed
- **Logging configuration**: Now properly configured to write to files specified in the environment.

## [10.0.0]

### Added
- **Hexagonal Architecture implementation**: Clean separation of concerns with Ports & Adapters.
- **Safe Serialization**: Replaced `serialize()`/`unserialize()` with JSON encoding and schema validation to prevent RCE vulnerabilities.
- **Secrets Management**: Utilizes `.env` files with `vlucas/phpdotenv` for secure storage of secrets.
- **Production JWTs**: Robust JWT middleware for authentication.
- **FFI Hardening**: Anti-Corruption Layer with strict validation and memory ownership rules.
- **Deterministic PRNG**: Seedable Pseudo-Random Number Generator for reproducible simulations.
- **Powerful CLI**: New `symfony/console`-based CLI (`bin/celestial`) as the single entry point.
- **Dockerized Environment**: Provided `Dockerfile` and `docker-compose.yml` for easy setup.
- **Static Analysis**: Configured for PHPStan (level 9) and PSR-12 standards.

### Changed
- **Replaced `serialize()`/`unserialize()` with JSON** for safer serialization.
- **Upgraded to PHP 8.2+** for modern language features and security improvements.

### Fixed
- **FFI Memory Management**: Added `__destruct()` methods to prevent memory leaks from FFI allocations.
- **Transactional Checkpointing**: SQLite operations are now atomic with metadata validation for improved reliability.
