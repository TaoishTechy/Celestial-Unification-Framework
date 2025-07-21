# Security Policy

This document outlines the security advisory for version 10.3 of the Celestial Unification Framework and best practices for maintaining a secure environment.

## Security Advisory (v10.3.0 "Integrity Edition")

### Addressed Vulnerabilities

- **FFI Supply-Chain Attack**: Prevented by verifying the SHA-256 checksum and GPG-style signature of native libraries (`libcelestial_kernel.so`).
- **Insecure Configuration**: The `env()` helper now aborts startup when critical environment variables are missing, preventing undefined behaviors.
- **Information Disclosure**: Exception messages are sanitized by default, removing sensitive file paths unless `APP_DEBUG` is explicitly enabled.
- **Scientific Integrity**: The framework aborts if the native FFI kernel is unavailable; fallback (`NullQuantumKernel`) only activates with the `--allow-degraded` flag.
- **Data Integrity & Reliability**: Checkpointing is atomic and validated, ensuring deterministic resumption and preventing data corruption.
- **Operational Gaps**: Enhanced logging and health checks via `sim:health` cover FFI integrity, database writability, configuration validity, and signal handling availability.

## Best Practices

1. **Immediate Upgrading**: Upgrade to v10.3.0 or later to apply all security patches and integrity checks.
2. **Library Integrity Verification**:
   - Regularly run `KernelBridge` integrity checks as part of your CI/CD pipeline:
     ```bash
     php artisan sim:health --json | jq '.ffiStatus'
     ```
3. **Environment Configuration**:
   - Ensure all critical environment variables (e.g., `DATABASE_URL`, `FFI_LIB_PATH`, `APP_DEBUG`) are defined in your deployment environment.
   - Use secret management solutions compatible with `.env` files (e.g., HashiCorp Vault, AWS Secrets Manager).
4. **Logging & Monitoring**:
   - Enable log rotation for Monolog RotatingFileHandler to prevent disk exhaustion.
   - Integrate `sim:health --json` output into your monitoring dashboard (e.g., Prometheus, Datadog).
5. **Deterministic Resumption**:
   - Validate checkpoint files before resuming simulations:
     ```bash
     php artisan sim:health
     ```
   - Store checkpoint metadata securely and version-control schemas used for validation.
6. **Access Control & Permissions**:
   - Run Celestial Framework services under least-privilege accounts.
   - Restrict access to FFI libraries and checkpoint directories.

## Reporting Security Issues

If you discover a security vulnerability, please report it to the maintainers via email at security@celestial-framework.example.com or open an issue on the GitHub repository: https://github.com/celestial-unification-framework/issues.

---
*Last updated: 2025-07-21*
