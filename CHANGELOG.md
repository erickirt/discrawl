# Changelog

All notable changes to `discrawl` will be documented in this file.

## 0.1.0 - 2026-03-07

- initial public release of the `discrawl` CLI
- added local SQLite archive, FTS search, raw SQL, sync, tail, status, doctor, members, and channels commands
- added OpenClaw/env token resolution and local runtime config
- added linting, CI, secret scanning, and coverage enforcement
- fixed targeted channel sync to avoid unnecessary guild-wide refresh work
- fixed inaccessible channel handling so sync marks and skips `403 Missing Access` channels
- fixed empty-channel sync state so full crawls can prove completion for quiet channels
