# Tracen Playgrounds: Blacklisted ROMs

**Maintained separately from the core [Device Tree Guidelines][guidelines]** so entries can be added or removed without a full guidelines revision. Additions/removals still require the Standing Blacklist Criteria and Blacklist Reform Process defined there; this file only tracks *which* ROMs currently meet those criteria and why.

**Last updated:** August 6, 2026

---

## Severity Framework

Each entry is rated on two independent axes, since a ROM's danger to the people running it and its danger to the collaborative developer ecosystem don't always move together. A rating reflects the strength of evidence behind it; entries still marked "provisional" or "unverified" above carry a provisional severity rating until confirmed.

**User Severity**, risk to the people actually running the ROM (device safety, data, privacy, finances):
- **Critical**: direct device or data harm (e.g. malicious wipe triggers), or confirmed financial fraud with real losses.
- **High**: significant financial or privacy risk (e.g. confirmed donation fraud), without active device-damaging behavior.
- **Moderate**: indirect risk to users (e.g. instability or unpatched issues stemming from the underlying misconduct).
- **Low**: no meaningful direct risk to end users; the issue is primarily inter-project.

**Community Severity**, harm to the collaborative open-source ecosystem (license compliance, credit, conduct toward other developers):
- **Critical**: a sustained, proven pattern of license violations combined with harassment or closed-source retaliation.
- **High**: confirmed license violations or uncredited code use, or a single severe, well-corroborated conduct incident.
- **Moderate**: a single verified incident without an established pattern, or credible but not yet fully corroborated allegations.
- **Low**: an advisory or standards disagreement, without misconduct.

---

## Currently Banned

### Project Elixir
**Severity:** User: Critical · Community: High. *(Direct data/device harm to users via the wipe trigger; concealment of source from the project's own maintainers is a serious but single-incident breach of collaborative trust, not an established multi-incident pattern.)*

Hidden ADB-triggered wipe (internal storage, external storage, eSIM) targeting users who bypass paid features; source deliberately hidden from its own maintainers. Banned by XDA Forums.[^1]

### EvolutionX
**Severity:** User: Low · Community: High. *(No confirmed direct harm to end users; the donation-fraud claim remains unconfirmed. Community severity now reflects two independently-standing confirmed incidents, the `bbd156f` kanging case and the archived harassment incident below, though not yet an established multi-incident pattern, which would push it to Critical.)*

- A documented pattern of uncredited code cherry-picking ("kanging") and disputed donation practices.[^2]
- **Verified instance:** Evolution-X's `packages_apps_LMOFreeform` commit `bbd156f`[^4] reproduces the combined code changes of two separate Pixelify-AOSP commits by `kenway214`[^5][^6], by diff comparison, credited only to the Evolution-X committer, with no reference to the original author or repository.
- **Evidentiary caveat on the "pattern" source:** the general documentation source (`joeyhuab.com`)[^2] is also cited by DerpFest's own GitHub organization as supporting material in DerpFest's separate, ongoing dispute with EvolutionX (see DerpFest entry below). Separately, the same account (`joeyhuab`) is itself the committer on the Evolution-X repository involved in the verified `bbd156f` incident above.[^4] For both reasons, `joeyhuab.com` should not be treated as an independent, disinterested source for the broader "pattern" claim, even though the `bbd156f` diff comparison stands on its own regardless of who reported it.
- A previously-cited "GPL violation" commit has been removed from this entry after inspection showed it does not demonstrate a violation (it adds correct Apache-2.0/SPDX headers).
- **Confirmed:** an archived capture of `packages_apps_Evolver` commit `7104505`[^7] contains a squash of numerous "Evolver" feature commits, including a personal attack on DerpFest's owner (Alexander Brunswig) by name embedded directly in the commit message. This commit no longer resolves on live GitHub, consistent with a later history rewrite. The capture was manually verified against archive.org directly (this tooling cannot reach archive.org itself, see [^7]). This speaks to the "sustained harassment" ground in the Standing Blacklist Criteria rather than to code provenance, and is a separate matter from the `bbd156f` kanging evidence above.
- **Status:** two incidents are now independently confirmed (`bbd156f` kanging, and the `7104505` harassment incident). The general "pattern" and donation-fraud claims sourced only to `joeyhuab.com` still need corroboration from a source without the conflicts noted above before this entry's broader claims can be considered as solid as its two confirmed incidents.

### DerpFest
**Severity:** User: Low · Community: High. *(No direct harm to end users; community severity reflects multiple independently-corroborated elements, confirmed plagiarism, a public closed-source retaliation threat, and self-published combative conduct, though not an established pattern across multiple separate incidents, which would push it to Critical.)*

**Disclosure:** Halcyon, the project referenced throughout this entry as the party DerpFest allegedly copied, is the lead developers' maintained ROM. The file- and screenshot-level evidence below is independently checkable by anyone regardless of that fact, but readers should weigh the framing accordingly, consistent with this document's standard of flagging non-neutral sourcing (see the `joeyhuab.com` caveat under EvolutionX).

- Copied Halcyon's "About Phone" settings redesign, by Halcyon's own account specifically the visual/UI identity (in place since Android 13), not a code-kanging claim.[^3]
- **Primary source, directly reviewed:** the original Telegram post documenting the dispute[^3] includes a direct quote from DerpFest's owner, Alexander Brunswig, announcing the project would revert to a private, closed repository after the plagiarism accusation became public, alongside Halcyon's public response and a screenshot of DerpFest's own GitHub README "Warning" section.
- **File-level comparison (on file, screenshots not independently re-fetched):** DerpFest's `packages_apps_Settings/stub/ui/res/layout/about_phone_info_header.xml` (166 lines, header dated "Copyright (C) 2021-2023 DerpFest Project," committed 4 days before the screenshot was taken) and Halcyon's file at the identical path (162 lines, header dated "Copyright (C) 2021-2023 Halcyon Project," committed roughly 8 months earlier) are near-identical in structure down to shared layout/dimension naming. Given the disclosure above, the project should archive stable, independently-fetchable links to both commits before treating this as settled rather than relying on screenshots alone.
- **Supporting visual comparison:** side-by-side "About phone" screens from a Halcyon-based build and a DerpFest build show matching banner-plus-quick-info-grid layouts, consistent with the file-level comparison above. A second pair of screenshots (Launcher-side "Home settings" screens) shows a similar six-item menu structure on both, but this is weaker evidence on its own: DerpFest's launcher is itself explicitly Lawnchair-derived (credited as such in its own `launcher_about.xml`), and a shared Lawnchair ancestry would produce similar settings-list structure independent of any Halcyon-specific copying.

| DerpFest | Halcyon |
| :---: | :---: |
| <img alt="file-53f9abc3f1d9fb4139d65469b2028ea5" src="https://github.com/user-attachments/assets/f9aac65c-30af-497e-8b68-4b2647c0359b" /> | <img alt="file-5896933f5174c01df177c6ba5f3d9f9d" src="https://github.com/user-attachments/assets/e239b86e-68dc-49e9-a479-2ea4a8ba067e" /> |

| DerpFest | Halcyon |
| :---: | :---: |
| <img alt="file-067e51a0f1bb355af2b8e70770f50d53" src="https://github.com/user-attachments/assets/a07c4368-6e80-47b7-99d9-ffcbf1458d51" /> | <img alt="file-0c50be28db7e47d65908d67dc58d6ebd" src="https://github.com/user-attachments/assets/6d8710bc-efe6-435c-8e6e-22951b52a642" /> |

- DerpFest's own GitHub organization page independently corroborates a combative posture toward other projects: an unprompted dig at another project in its own org bio, and a README "Warning" section accusing CrDroid and EvolutionX of using DerpFest's contributions without credit.[^4] Blacklisted independent of its conflict with EvolutionX.

---

## Process Notes

- Entries here are added or removed only per the Blacklist Reform Process in the core guidelines; this file is a record of current status, not a separate approval track.
- Each entry **MUST** cite verifiable evidence (archived commits, forum rulings, first-party statements, etc.) rather than unsubstantiated claims.
- Removing an entry follows the same reform process regardless of how it's recorded. Moving the list here does not create a lighter-weight removal path.
- Before adding a new citation, verify it actually supports the specific claim attached to it (fetch the source, don't just trust a pasted excerpt). A citation that doesn't match its claim is worse than no citation at all in a document that names real people and projects.
- Severity ratings are informational, they help readers calibrate how seriously to weigh an entry, and don't themselves change the Blacklist Reform Process threshold or timeline. A "Low" severity entry still requires the full reform process to remove.
- Update an entry's severity rating whenever its underlying evidence changes (e.g. a "provisional" or "unverified" item is confirmed or disproven).

---

## References

[^1]: XDA Forums, "ElixirOS to break and wipe your device": http://xdaforums.com/t/elixiros-to-break-and-wipe-your-device.4672456
[^2]: Third-party documentation of alleged cherry-picking and donation records: http://joeyhuab.com/, see evidentiary caveat above; corroborate independently (e.g., specific commit diffs showing uncredited authorship) before relying on this alone.
[^3]: Primary source, Telegram, `@keepandroidlegacy/52`, "#Drama: #DerpFest switches to close-source": https://t.me/keepandroidlegacy/52. Contains direct quotes from DerpFest owner Alexander Brunswig and Halcyon's public response. Reviewed directly by @naokoshoto; see disclosure in the DerpFest entry above regarding that relationship. A separate Telegram post from the same channel, alleging DerpFest bans users over ethnic/historical disputes, was not used as a source for this entry: it consists substantially of Armenian genocide denial and is not cited or reproduced here regardless of its relevance to the drama in question.
[^4]: Evolution-X/packages_apps_LMOFreeform@bbd156f, "LMOFreeform: Fix build on Android 17" (committed by joeyhuab): https://github.com/Evolution-X/packages_apps_LMOFreeform/commit/bbd156fd62965346c26ba71cb9139489745d8f2c
[^5]: Pixelify-AOSP/packages_apps_LMOFreeform@3752f6c, "LMPFreeform: Cleanup unused task listener methods" (kenway214): https://github.com/Pixelify-AOSP/packages_apps_LMOFreeform/commit/3752f6c8bf2e9f7f0d32cb9f821e998b5950805e
[^6]: Pixelify-AOSP/packages_apps_LMOFreeform@c15d98c, "LMOFreeform: Update virtual display creation and display configuration signature" (kenway214): https://github.com/Pixelify-AOSP/packages_apps_LMOFreeform/commit/c15d98c05171544b3077deb648728b344069212f
[^7]: Archived capture, Evolution-X/packages_apps_Evolver@7104505 (squash commit, "[SQUASH] Evolver: Refactor for A14 QPR2"): https://web.archive.org/web/20240524151758/https://github.com/Evolution-X/packages_apps_Evolver/commit/7104505017b02b7020bab642a4d396d72dad43b7. **Manually verified** by @naokoshoto directly against the archive.org capture (this tooling cannot reach archive.org to independently re-check it); the corresponding commit hash no longer resolves on live GitHub, consistent with a later history rewrite.

[guidelines]: ./GUIDELINES.md
