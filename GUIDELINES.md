# Tracen Playgrounds: Device Tree Guidelines

**Lead Devs:** @rthedream · @rexix01 · @naokoshoto

**Updated:** August 6, 2026

These guidelines exist to maintain architectural stability, protect end-user security, and streamline debugging across all releases. The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** follow RFC 2119. Unclear cases **MUST** be raised with the leads before compiling.

---

## Global Rules

- You **MUST** own the physical device you build or port for. Blind building/porting is prohibited. Trusted Developers **MAY** be exempt from ownership but **MUST NOT** build blind (see Trusted Developer Policy).
- You **MUST** credit upstream sources and lead developers in release notes, and **SHOULD** credit individual helpers.
- You **MUST** fully understand any AI-assisted code you submit, well enough to read, explain, and debug it (see AI Code Policy).
- You **MUST NOT** direct personal attacks or bad-faith mockery at leads, builders, or Trusted Developers. Substantive, specific criticism of decisions or conduct is permitted; disagreement alone is not a violation. Demeaning public comparisons of your work to others' are prohibited. Neutral factual comparisons are fine. If provoked publicly over project matters, de-escalate and redirect to facts.
- **Community channels** *(advisory only)*: an independent channel **SHOULD NOT** present itself as *the official* Beryl/Citrine community. A jointly-formed, lead-acknowledged "alliance" channel is fine, as are regional/language groups that don't claim sole official status. Violating this alone is not grounds for removal.
- **Monetization**: disclose any donations/sponsorships/personal funding in release notes. A clearly-labeled personal tip jar is fine; gating features, updates, or support behind payment is not. Misrepresenting where donations go is grounds for immediate removal and may count toward blacklisting.

---

## Licensing

- Kernel repos **MUST** stay GPLv2 (inherited from upstream Linux), with no relicensing or stripping of notices.
- Device tree / build script / config repos **MUST** ship an Apache 2.0 `LICENSE` file; missing it means non-compliance regardless of public visibility.
- Vendor blob directories **MUST** include a `PROPRIETARY-FILES.txt` (or equivalent) and **MUST NOT** be presented as open-source.
- AI-assisted contributions are only validly licensed if the human-authored portion is original and copyright-eligible (see AI Code Policy).

---

## Security Baseline

*(Referenced by Recovery Policy, Kernel Policy, Root Policy, ROM Builders, and OEM Porters below.)*

**"Shipped" means any build distributed outside private internal testing or the Trusted Developer testing pool.** This includes canary/unofficial-testing channels. A build only escapes this Baseline while it stays entirely private.

Every shipped build, whether ROM or port, canary or stable, rooted or not, **MUST** be `user`/`userdebug`, signed with private release keys, SELinux Enforcing, and verified-boot compliant. Unsigned or permissive builds **MUST NOT** be shipped. No allowance elsewhere in this document (alternate kernels, root support, canary status, porting exceptions, etc.) may be used to relax this baseline.

---

## Recovery Policy

*Applies to ROM Builders.*

- Ship AOSP/LineageOS recovery by default.
- Custom recoveries (TWRP, OrangeFox, etc.) are **NOT RECOMMENDED**. On modern Virtual A/B devices they break delta OTAs, corrupt hash validation, and raise hard-brick risk. This is a structural consequence of how seamless updates and dm-verity/AVB interact on current partition layouts, not a project-specific preference.[^1]
- Exceptions require explicit lead approval and a proven OTA-survival method.
- Any exception is still bound by the Security Baseline.

---

## Kernel Policy

*Applies to ROM Builders and OEM Porters.*

- Ship the tree/OEM-provided kernel by default (**SHOULD**, not absolute). A non-root alternate kernel **MAY** be offered alongside it, with its source disclosed in release notes.
- Issues that only reproduce on an alternate kernel **MAY** get limited or no support from leads.
- Alternate kernels are still bound by the Security Baseline.

---

## Root Policy

*Applies to ROM Builders and OEM Porters.*

- "Pre-rooted" means root granted automatically, out of the box. Pre-rooted builds **MUST NOT** ship, on either AOSP builds or ports.
- AOSP/LineageOS builds **MUST NOT** officially integrate a root framework (KernelSU, Magisk, APatch, or equivalents) into the shipped kernel/boot image. Users **MAY** self-root via their own tooling. That's not a violation, and it's entirely at the user's own risk; builders **MAY** decline to support issues caused or worsened by it. If an issue also reproduces with root disabled, it's a normal ROM issue (see Bug Report Policy).
- OEM ports **MAY** ship an integrated root framework, but it **MUST** be optional (normal operation doesn't depend on it) and **MUST** require explicit user action to enable. This exception exists because OEM ports operate outside the OEM's own warranty and support chain by definition. The trust and update-cadence assumptions an AOSP build makes to its users don't carry over, so gating root behind explicit user action is judged sufficient where it wouldn't be for an official AOSP release.
- Any root-capable image is still bound by the Security Baseline. Root support never justifies relaxing it. In practice this means root-capable images use a re-signed or user-owned root of trust (e.g. a user-generated AVB key) rather than the OEM's stock signing chain; "root-capable" and "verified boot intact" are not in tension as long as *some* verified chain of trust is preserved and enforced.
- Note: Play Integrity's strongest attestation will fail once root is actually activated. Expected, not a defect.

---

## ROM Builders

- Recovery choices follow the Recovery Policy above.
- Builds **MUST** meet the Security Baseline.
- Don't modify kernel/base trees beyond what compiling requires (alternate-kernel allowance excepted). Genuinely necessary fundamental changes need lead discussion and a PR first.
- Maintain no more than 3 active ROMs at once, and don't duplicate a ROM another project builder already actively maintains. Leads **MAY** grant a documented exception above 3 ROMs where a builder demonstrates the infrastructure (CI/CD, automated testing, sustained monthly update cadence) to support it. This is a discretionary, revocable exception, not a rule change; the default cap remains 3.
- Notify leads before dropping a project. Switching ROMs isn't permitted except when your current ROM gets an official maintainer elsewhere, or upstream breaks core functionality.
- **Release coordination**: coordinate major version/QPR timing with leads and other builders on the same ROM. This prevents overlapping releases, not personal vetoes. Leads mediate unresolved conflicts; unresolved cases default to first-ready-first-released unless there's a documented technical reason to delay.
- **Abandonment**: a ROM with no update, security patch, or lead communication for 60 consecutive days is considered abandoned. Leads **MUST** attempt to contact the builder before delisting; if unreachable or unresponsive after that attempt, the ROM **MUST** be pulled from official channels and marked discontinued.

---

## OEM Porters

- Kernel and root choices follow the Kernel Policy and Root Policy above.
- You **MAY** modify boot-adjacent resources (vendor_boot ramdisk, fstab, dtbo, sepolicy) only as far as needed for a bootable, secure result. Nothing beyond that's minimally necessary. Any such change must stay within the Security Baseline and be disclosed in release notes (what changed, why).
- Don't make unnecessary changes to the vendor partition beyond porting compatibility.
- Open-source any public work involving the base vendor (private internal builds are out of scope), and credit the base kernel/vendor in release notes.
- If the OEM kernel/vendor source is broken, incomplete, or withheld beyond what the above allowances can fix, raise it with leads first. Leads **MAY** grant a narrowly-scoped, disclosed exception, but it can never waive the open-source requirement or the Security Baseline.

---

## WIP / Pre-Release Gating

*Applies to ROM Builders and OEM Porters.*

- Don't publicly push or announce a WIP tree/build until it clears the project's Pre-Release Checklist (a separate, versioned document the leads can update independently; the version in effect at publish time is the one that applies). Private staging, internal testing, or the Trusted Developer pool are fine before that point.
- This checklist replaces any subjective "stability percentage" as the readiness standard.
- A separate "canary"/unofficial-testing channel is allowed for earlier builds, if clearly labeled pre-checklist and unsupported, but a canary build is still "shipped" per the Security Baseline (see above), and it is still bound by the Baseline in full.
- **GPL compliance is not gated by the checklist.** If a canary or any other pre-checklist build is distributed to anyone outside private internal testing, and it contains GPL-covered components (e.g. the kernel), corresponding source **MUST** be made available alongside or promptly after that distribution, checklist status notwithstanding. "Not checklist-ready" is not an excuse to withhold GPL source from a build that's already out the door.

---

## Bug Report / Logcat Policy

*Applies to all support channels.*

- Testers **SHOULD** disable root, Magisk/KernelSU/Xposed-LSPosed before capturing a logcat, unless the issue is specifically tied to the ROM/port's own root or kernel integration.
- A report isn't automatically a ROM/port issue just because it was captured on a rooted device; conversely, if it doesn't reproduce with root/modules disabled, it isn't a ROM/port issue. Issues tied to a specific root module/manager app should be redirected to that module's own developers.
- Include this triage guidance in your support channel's pinned instructions or issue template.

---

## AI Code Policy

*Adapted from the [PrismLauncher Contributing Guide][^2] and the [Linux kernel coding-assistants policy][^3].*

- Don't post raw AI output in comments or community channels. Feeding a prompt to an AI and posting the result, unedited and unverified, isn't acceptable.
- You **MUST** understand every AI-assisted change well enough to explain why it's correct.
- Only the human submitter may certify authorship (`Signed-off-by`). AI agents never add this tag. Disclose AI assistance in commits via `Assisted-by: AGENT_NAME:MODEL_VERSION`.
- You must be able to assert in good faith that the human-authored portion is your own, original, copyright-eligible work. Substantially unedited AI output can't be represented as license-compliant.
- Leads may reject undisclosed AI-generated contributions, or ones where the submitter can't demonstrate understanding. This includes cases discovered after the fact: if AI-assisted code breaks the build and the submitter cannot diagnose or explain the failure, leads **MAY** revert the commit and revoke commit privileges.

---

## Blacklisted ROMs

Never build or ship a ROM meeting the Standing Blacklist Criteria below, or any ROM named in the project's maintained [`BLACKLIST.md`][^4]. The default position is always that a ban remains. See Blacklist Reform Process to challenge it.

**Standing Blacklist Criteria**: a ROM may be evaluated for blacklisting if it uses copyleft code while refusing corresponding source disclosure for closed internal testing; commits donation/sponsorship misrepresentation; or engages in sustained harassment tied to the project.

The specific list of currently banned ROMs, and the evidence behind each entry, is maintained separately in [`BLACKLIST.md`][^4] so it can be updated without revising these core guidelines. Leads **MAY** add or remove entries there under the Standing Blacklist Criteria and Blacklist Reform Process defined here; the criteria and process themselves still require a full guidelines revision to change.

---

## Discouraged ROMs

**LunarisAOSP, InfinityX, AfterlifeOS**: not recommended for builds using our trees, due to misalignment with this project's design/UX philosophy (a standards call, not an ethical one). No debugging support is provided for issues arising from them; builders who proceed take on full support responsibility themselves.

---

## Trusted Developer Policy

- Granted solely by leads, case-by-case, based on the quality/impact of merged contributions (not volume; many trivial PRs don't qualify). Exempts a developer from device ownership, but not from accountability.
- Without owning the device, you **MUST** coordinate with at least one reliable, physically-verified, named (in release notes) tester before release, and mark the build as untested by you on physical hardware.
- Releasing without a verified tester is treated as blind building, and is grounds for immediate status revocation.
- Leads may revoke status any time, without a community vote, if quality, conduct, or testing standards decline.

---

## Blacklist Reform Process

Default position: the ban remains; burden of proof is on the project seeking reinstatement.

1. **Eligibility**: leads confirm the original ban reason has been concretely addressed. If not, the process stops here.
2. **Initiation**: only a lead, or a petition from 15+ active builders (merged PR in the last 6 months), can open a reform vote.
3. **Waiting period**: 12 months minimum since the ban; a failed vote blocks new votes for 6 months.
4. **Voter eligibility**: active builders and leads only.
5. **Threshold**: two-thirds supermajority required; simple majority isn't enough.
6. **Probation**: reinstated projects get 6 months probation. Any repeat misconduct means immediate permanent re-blacklisting; leads may act unilaterally during probation.

---

## Platform Trajectory

*(Informational, non-binding.)* Some rules here (like requiring unrooted, Play Integrity-compliant builds) assume today's Android platform rules. If Google's device-verification requirements change enough to break that assumption, leads may revisit the affected rules. This note changes nothing by itself.

---

Non-compliance may result in removal from development channels and permanent loss of repository access. When in doubt, ask the leads before you compile.

---

## References

[^1]: cyberknight777, "Why No TWRP on Modern Devices": http://cyberknight777.dev/posts/2025/12/why-no-twrp-on-modern-devices
[^2]: PrismLauncher `CONTRIBUTING.md`: http://github.com/PrismLauncher/PrismLauncher/blob/develop/CONTRIBUTING.md
[^3]: Linux kernel documentation, "Coding-assistants": http://kernel.org/doc/html/next/process/coding-assistants.html
[^4]: `BLACKLIST.md`, maintained separately; see project repository root.
