# Tracen Playgrounds: Blacklisted ROMs

**Maintained separately from the core [Device Tree Guidelines][guidelines]** so entries can be added or removed without a full guidelines revision. Additions/removals still require the Standing Blacklist Criteria and Blacklist Reform Process defined there; this file only tracks *which* ROMs currently meet those criteria and why.

**Last updated:** August 6, 2026

---

## Currently Banned

### Project Elixir
Hidden ADB-triggered wipe (internal storage, external storage, eSIM) targeting users who bypass paid features; source deliberately hidden from its own maintainers. Banned by XDA Forums.[^1]

### EvolutionX
Multi-year pattern of GPL violations, donation fraud, open-source paywalling, and developer harassment, confirmed by archived commits[^2] and a former co-developer's own account.[^3]

### DerpFest
UI plagiarism from Halcyon, close-source threats in response to criticism, nationality-based community bans.[^4] Blacklisted independent of its conflict with EvolutionX.

---

## Process Notes

- Entries here are added or removed only per the Blacklist Reform Process in the core guidelines; this file is a record of current status, not a separate approval track.
- Each entry **MUST** cite verifiable evidence (archived commits, forum rulings, first-party statements, etc.) rather than unsubstantiated claims.
- Removing an entry follows the same reform process regardless of how it's recorded. Moving the list here does not create a lighter-weight removal path.

---

## References

[^1]: XDA Forums, "ElixirOS to break and wipe your device": http://xdaforums.com/t/elixiros-to-break-and-wipe-your-device.4672456
[^2]: Archived commit, `Evolution-X/packages_apps_Evolver`: http://web.archive.org/web/20240524153441/github.com/Evolution-X/packages_apps_Evolver/commit/358ac376432f1df6efe5ff264e328aca7f3c14f4
[^3]: Joey Huab: http://joeyhuab.com/
[^4]: Telegram, `@keepandroidlegacy`, post 52: http://t.me/keepandroidlegacy/52

[guidelines]: ./Tracen-Playgrounds-Device-Tree-Guidelines-v3.md
