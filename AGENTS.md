# Shared data maintenance

Read README.md and NOTICE.md.

- Edit source/*.json for corrections; never replay historical patches.
- Preserve unknown fields, sparse IDs, precision, attribution and source scope.
- JSON arrays represent one-based Lua sequences; numeric ID maps remain objects.
- Keep this repository data-only: adapters, build tools and tests belong in consumers.
- Regenerate and test both addons using the README commands after corrections.
