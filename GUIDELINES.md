# Tracen Playgrounds: Device Tree Guidelines

**Lead Devs:** @rthedream · @rexix01 · @naokoshoto
**Updated:** August 6, 2026

These guidelines exist to maintain architectural stability, protect end-user security, and streamline debugging across all releases. The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in RFC 2119, and are used consistently throughout. Unclear cases **MUST** be raised with the leads before compiling.

---

## Global Rules

- You **MUST** own the physical device you build or port for.
- Blind building or porting is strictly prohibited.
- Trusted Developers **MAY** be exempt from ownership, but **MUST NOT** build or port blindly (see Trusted Developer Policy).
- You **MUST** credit upstream sources and lead developers in release notes.
- You **SHOULD** also credit individual helpers where applicable.
- You **MUST** fully understand any AI-assisted code you submit, well enough to read, explain, and debug it (see AI Code Policy).

**Conduct**
- You **MUST NOT** direct personal attacks or bad-faith mockery at leads, builders, porters, or Trusted Developers.
- Substantive, specific criticism of decisions or conduct **IS** permitted.
- Disagreement alone **IS NOT** a violation of this rule.
- You **MUST NOT** publicly compare your work to a lead's or another builder's work in a way intended to demean rather than inform.
- Neutral, factual comparisons remain permitted.
- If provoked publicly over project matters, you **SHOULD** de-escalate and redirect to facts.
- See Conduct Enforcement for how violations of this section are applied in community channels.

**Community channels** *(advisory — non-enforcing)*
- An independent channel **SHOULD NOT** present itself as *the official* Beryl/Citrine community.
- A jointly-formed, lead-acknowledged "alliance" channel **IS** permitted.
- Regional or language-specific groups **REMAIN** permitted, provided they don't claim sole official status.
- Violating this section alone **SHALL NOT** be grounds for removal.

**Monetization**
- You **MUST** disclose any donations, sponsorships, or personal funding in release notes.
- A clearly-labeled personal tip jar **IS** permitted.
- Gating features, updates, or support behind payment **IS NOT** permitted, personal or otherwise.
- Misrepresenting where donations go is grounds for immediate removal and **MAY** count toward blacklist consideration.

---

## Licensing

- Kernel repositories **MUST** remain licensed GPLv2, inherited from upstream Linux.
- Kernel repositories **MUST NOT** be relicensed or have their notices stripped.
- Device tree, build script, and configuration repositories **MUST** ship an Apache License 2.0 `LICENSE` file.
- A repository missing this file is **NOT** in compliance, regardless of public visibility.
- Vendor blob directories **MUST** include a `PROPRIETARY-FILES.txt` (or equivalent notice).
- Vendor blobs **MUST NOT** be presented as open-source content.
- AI-assisted contributions **MUST** have a human-authored, copyright-eligible portion to be validly licensed (see AI Code Policy).

---

## Security Baseline

*(Referenced by Recovery Policy, Kernel Policy, Root Policy, ROM Builders, and OEM Porters below.)*

- "Shipped" **MEANS** any build distributed outside private internal testing or the Trusted Developer testing pool — this includes canary/unofficial-testing channels.
- A build **ONLY** escapes this Baseline while it stays entirely private.
- Every shipped build **MUST** be `user`/`userdebug`, signed with private release keys, SELinux Enforcing, and verified-boot compliant.
- Unsigned or permissive builds **MUST NOT** be shipped.
- No allowance elsewhere in this document (alternate kernels, root support, canary status, porting exceptions, etc.) **MAY** be used to relax this Baseline.

---

## Recovery Policy

*Applies to ROM Builders.*

- You **MUST** ship AOSP/LineageOS recovery by default.
- Custom recoveries (TWRP, OrangeFox, etc.) are **NOT RECOMMENDED**. On modern Virtual A/B devices they break delta OTAs, corrupt hash validation, and raise hard-brick risk — a structural consequence of how seamless updates and dm-verity/AVB interact on current partition layouts, not a project-specific preference.[^1]
- Exceptions **REQUIRE** explicit lead approval and a proven OTA-survival method.
- Any exception **MUST** still meet the Security Baseline.

---

## Kernel Policy

*Applies to ROM Builders and OEM Porters.*

- Builders/porters **SHOULD** ship the tree/OEM-provided kernel by default. This is a **RECOMMENDATION**, not an absolute rule.
- A non-root alternate kernel **MAY** be offered alongside it.
- Any alternate kernel's source **MUST** be disclosed in release notes.
- Issues that only reproduce on an alternate kernel **MAY** receive limited or no support from the leads.
- Alternate kernels **MUST** still meet the Security Baseline.

---

## Root Policy

*Applies to ROM Builders and OEM Porters.*

- "Pre-rooted" **MEANS** root granted automatically, without user action, out of the box.
- Pre-rooted builds **MUST NOT** ship, on either AOSP builds or ports.
- AOSP/LineageOS-based ROM builds **MUST NOT** officially integrate a root framework (KernelSU, Magisk, APatch, or equivalents) into the shipped kernel or boot image, in any variant.
- Users **MAY** self-root an AOSP/LineageOS-based build using their own tooling.
- Self-rooting this way **IS NOT** a policy violation.
- A self-rooted AOSP build is used entirely at the user's own risk.
- Builders **MAY** decline to support issues caused or worsened by self-applied root.
- If an issue also reproduces with root disabled, it **MUST** be treated as a normal ROM issue regardless of root status (see Bug Report / Logcat Policy).
- OEM ports **MAY** ship an integrated root framework.
- Root integration on a port **MUST** be optional: normal operation **MUST NOT** depend on it.
- Enabling root on a port **MUST** require explicit user action.
- Any root-capable image **MUST** still meet the Security Baseline. In practice this means using a re-signed or user-owned root of trust (e.g., a user-generated AVB key) rather than the OEM's stock signing chain — "root-capable" and "verified boot intact" are not in tension as long as some verified chain of trust is preserved and enforced.
- Root support **MUST NOT** be used to justify relaxing the Security Baseline.

**Why AOSP builds and ports are treated differently**
- This is **NOT** a warranty distinction — any custom ROM, AOSP build or port, voids the OEM warranty equally.
- It is also **NOT** a Play Integrity distinction in the way the original rule implied: any device with an unlocked bootloader running a custom ROM — rooted or not — already fails Play Integrity's **Device** and **Strong** Integrity tiers. That happens the moment you flash a non-stock OS, independent of root.
- The actual distinction: AOSP/LineageOS-based builds are this project's primary, widest-reach release. Keeping them free of integrated root maximizes the odds of clearing Play Integrity's **Basic** Integrity tier — the lowest tier, and the one most non-banking apps actually check — for that broader, often less technical audience by default.
- Ports serve a narrower, more technical audience, where that default matters less.
- Builders/porters **SHOULD** note in release notes that Play Integrity's stronger tiers will not pass on any custom build, rooted or not, once the bootloader is unlocked — this is a property of running a custom ROM at all, not a defect specific to root.

---

## ROM Builders

- Recovery choices **MUST** follow the Recovery Policy above.
- Builds **MUST** meet the Security Baseline.
- You **SHOULD NOT** modify kernel/base trees beyond what compiling requires (see Kernel Policy for the alternate-kernel allowance).
- Genuinely necessary fundamental changes **REQUIRE** lead discussion and a PR first.
- You **MUST NOT** maintain more than 3 active ROMs at once.
- You **SHALL NOT** duplicate a ROM another project builder already actively maintains.
- Leads **MAY** grant a documented exception above 3 ROMs where a builder demonstrates the infrastructure (CI/CD, automated testing, sustained monthly update cadence) to support it. This is a discretionary, revocable exception, not a rule change — the default cap **REMAINS** 3.
- You **MUST** notify leads before dropping a project.
- Switching ROMs is **NOT PERMITTED**, except when your current ROM gets an official maintainer elsewhere, or upstream breaks core functionality.

**Release Coordination**
- Builders **MUST** coordinate major version/QPR release timing with leads and other builders on the same ROM.
- This exists to prevent overlapping releases — it **MUST NOT** function as a personal veto.
- Leads **MUST** mediate unresolved scheduling conflicts.
- Unresolved conflicts **SHALL** default to first-ready-first-released, unless a documented technical reason requires delay.

**Abandonment**
- A ROM with no update, security patch, or lead communication for 60 consecutive days **IS** considered abandoned.
- Leads **MUST** attempt to contact the builder before delisting.
- If unreachable or unresponsive, the ROM **MUST** be pulled from official channels and marked discontinued.

---

## OEM Porters

- Kernel choice **MUST** follow the Kernel Policy above.
- Root integration **MUST** follow the Root Policy above.
- Porters **MAY** modify boot-adjacent resources (vendor_boot ramdisk, fstab, dtbo, sepolicy) only as far as needed for a bootable, secure result.
- Changes beyond what's minimally necessary **ARE NOT PERMITTED**.
- Any such change **MUST** meet the Security Baseline.
- Any such change **MUST** be disclosed in release notes (what changed, why).
- Porters **SHOULD NOT** make unnecessary changes to the vendor partition beyond porting compatibility.
- Porters are **REQUIRED** to open-source any public work involving the base vendor. Private internal builds are out of scope.
- Porters **MUST** credit the base kernel/vendor in release notes.
- If the OEM kernel/vendor source is broken, incomplete, or withheld beyond what the above allowances can fix, porters **MUST** raise it with leads first.
- Leads **MAY** grant a narrowly-scoped, disclosed exception.
- Such an exception **MUST NOT** waive the open-source requirement or the Security Baseline.

---

## WIP / Pre-Release Gating

*Applies to ROM Builders and OEM Porters.*

- You **MUST NOT** publicly push or announce a WIP tree/build until it clears the project's Pre-Release Checklist.
- Private staging, internal testing, or the Trusted Developer pool **MAY** be used freely before that point.
- The Checklist is a separate, versioned document the leads **MAY** update independently of this document.
- The checklist version in effect at publish time is the one that applies to that build.
- This checklist **REPLACES** any subjective "stability percentage" as the readiness standard.
- A separate "canary"/unofficial-testing channel **MAY** be offered for earlier builds, if clearly labeled pre-checklist and unsupported — but a canary build is still "shipped" per the Security Baseline, and **REMAINS** bound by it in full.
- GPL compliance **IS NOT** gated by the checklist. If a canary or any pre-checklist build is distributed outside private internal testing and contains GPL-covered components (e.g., the kernel), corresponding source **MUST** be made available alongside or promptly after that distribution, checklist status notwithstanding.

---

## Conduct Enforcement

*Applies to all Tracen Playgrounds community channels. Enforces the Conduct clause in Global Rules.*

- Moderation **SHOULD** follow a graduated response, calibrated to severity and pattern, not just whatever tool is fastest to reach for.
- A first, ambiguous, or borderline instance **SHOULD** receive a logged warning only (e.g., a Rose Bot `/warn`), with no restriction applied.
- A single sharp comment **MUST NOT** be treated the same as a confirmed personal attack — this preserves the Conduct clause's protection for substantive criticism.
- A confirmed personal attack or instance of mockery, or a repeat warning, **MAY** result in a timed mute (24–72 hours).
- A repeat offender who reaches the configured warning limit, or a single clearly serious incident (targeted harassment, slurs), **MAY** result in a group-level ban from that specific channel.
- A group-level ban **REMAINS** reversible on appeal.
- A federation-wide ban **MUST** be reserved for doxxing, threats, hate speech, ban evasion, or a harassment pattern spanning multiple community channels, or repeated group-level offenses across channels.
- A federation ban **MUST NOT** be issued unilaterally by a single moderator.
- A federation ban **REQUIRES** sign-off from at least one lead, before or immediately after the action.
- Every federation ban **MUST** be logged with a stated reason in a moderator log channel.
- A federation ban **MAY** be appealed to the leads once, no earlier than 14 days after the ban.
- Warnings **SHOULD** expire after a defined period (e.g., 30 days), so a person is not held to violations indefinitely.
- Leads **SHOULD** set and disclose the warning-expiry period.
- Moderators **SHOULD** distinguish substantive criticism (permitted under Global Rules) from personal attacks or mockery (not permitted) before applying any tier above a warning.
- When in doubt, moderators **SHOULD** consult a lead rather than escalate unilaterally.

---

## Bug Report / Logcat Policy

*Applies to all support channels.*

- Testers **SHOULD** disable root, Magisk/KernelSU/Xposed-LSPosed before capturing a logcat, unless the issue is specifically tied to the ROM/port's own root or kernel integration.
- A report **MUST NOT** be treated as a ROM/port issue solely because it was captured on a rooted device.
- If the issue does **NOT** reproduce with root/modules disabled, it **IS NOT** a ROM/port issue.
- Issues tied to a specific root module/manager app **SHOULD** be redirected to that module's own developers first.
- Builders/porters **SHOULD** include this triage guidance in their support channel's pinned instructions or issue template.

---

## AI Code Policy

*Adapted from the [PrismLauncher Contributing Guide][^2] and the [Linux kernel coding-assistants policy][^3].*

- You **MUST NOT** post raw AI output in comments or community channels.
- Feeding a prompt to an AI and posting the result, unedited and unverified, **IS NOT ACCEPTABLE**.
- You **MUST** understand every AI-assisted change well enough to explain why it's correct.
- Only the human submitter **MAY** certify authorship (`Signed-off-by`). AI agents **MUST NOT** add this tag.
- You **MUST** disclose AI assistance in commits via `Assisted-by: AGENT_NAME:MODEL_VERSION`.
- You **MUST** be able to assert in good faith that the human-authored portion is your own, original, copyright-eligible work.
- Substantially unedited AI output **MUST NOT** be represented as license-compliant.
- Leads **MAY** reject undisclosed AI-generated contributions, or ones where the submitter can't demonstrate understanding.
- This **INCLUDES** cases discovered after the fact: if AI-assisted code breaks the build and the submitter cannot diagnose or explain the failure, leads **MAY** revert the commit and revoke commit privileges.

---

## Blacklisted ROMs

- You **MUST NOT** build or ship a ROM meeting the Standing Blacklist Criteria below.
- You **MUST NOT** build or ship any ROM named in the project's maintained `BLACKLIST.md`.[^4]
- The default position is always that a ban remains — see Blacklist Reform Process to challenge it.

**Standing Blacklist Criteria** — a ROM **MAY** be evaluated for blacklisting if it:
- Uses copyleft code while refusing corresponding source disclosure for closed internal testing;
- Commits donation/sponsorship misrepresentation;
- Engages in sustained harassment tied to the project.

The specific list of currently banned ROMs, and the evidence behind each entry, is maintained separately in `BLACKLIST.md` so it can be updated without revising these core guidelines. Leads **MAY** add or remove entries there under the Standing Blacklist Criteria and Blacklist Reform Process defined here; the criteria and process themselves still **REQUIRE** a full guidelines revision to change.

---

## Discouraged ROMs

**LunarisAOSP, InfinityX, AfterlifeOS** are **NOT RECOMMENDED** for builds using our trees, due to misalignment with this project's design/UX philosophy — a standards call, not an ethical one. No debugging support **SHALL** be provided for issues arising from them; builders who proceed **MUST** assume full support responsibility themselves.

---

## Trusted Developer Policy

- Trusted Developer status **MAY** exempt a developer from the device-ownership requirement.
- It **IS** granted solely by leads, case by case, based on the quality/impact of merged contributions, not volume.
- A high number of trivial PRs **SHALL NOT** qualify.
- Exemption from ownership **IS NOT** exemption from accountability.
- Without owning the device, you **MUST** coordinate with at least one reliable, physically-verified tester before release.
- That tester **MUST** be named in the release notes.
- The build **MUST** be marked as untested by the developer on physical hardware.
- Releasing without a verified tester is treated as blind building and **IS** grounds for immediate status revocation.
- Leads **MAY** revoke status at any time, without a community vote, if quality, conduct, or testing standards decline.

---

## Blacklist Reform Process

The default position is always that the ban remains; the burden of proof is on the project seeking reinstatement.

1. **Eligibility** — Leads **MUST** confirm the original ban reason has been concretely addressed. If not, the process **SHALL** stop here.
2. **Initiation** — Only a lead, or a petition from 15+ active builders (merged PR in the last 6 months), **MAY** open a reform vote.
3. **Waiting period** — A minimum of 12 months **MUST** have passed since the ban. A failed vote **SHALL** block new votes for 6 months.
4. **Voter eligibility** — Only active builders and leads **MAY** vote.
5. **Threshold** — A two-thirds supermajority **IS REQUIRED**. A simple majority **SHALL NOT** be sufficient.
6. **Probation** — Reinstated projects **MUST** enter 6 months probation. Any repeat misconduct **SHALL** result in immediate permanent re-blacklisting; leads **MAY** act unilaterally during probation.

---

## Platform Trajectory

> **Note (informational, non-binding):** Some rules here — like requiring unrooted-by-default AOSP builds — assume today's Android platform rules. If Google's device-verification requirements change enough to break that assumption, leads **MAY** revisit the affected rules. This note changes nothing by itself.

---

Non-compliance **MAY** result in removal from development channels and permanent loss of repository access. When in doubt, ask the leads before you compile.

---

## References

[^1]: cyberknight777, "Why No TWRP on Modern Devices": https://cyberknight777.dev/posts/2025/12/why-no-twrp-on-modern-devices/
[^2]: PrismLauncher `CONTRIBUTING.md`: http://github.com/PrismLauncher/PrismLauncher/blob/develop/CONTRIBUTING.md
[^3]: Linux kernel documentation, "Coding-assistants": http://kernel.org/doc/html/next/process/coding-assistants.html
[^4]: `BLACKLIST.md`, maintained separately; see project repository root.
