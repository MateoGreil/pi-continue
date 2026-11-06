# Changelog

## [0.1.0]

- Extract the existing production `/continue` extension unchanged into a standalone Pi package.
- Preserve interrupted-session continuation, tools, native retry, cancellation, reload, and saved-session behavior.
- Retain all existing test scenarios, replace the machine-specific installation resolver with normal SDK imports, and point fixtures at `src/index.js`.
- Add package metadata and document private API dependencies verified against Pi 1.5.0.
