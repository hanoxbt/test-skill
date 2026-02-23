# 🧪 Skill Card — Test Case Generator

---

## 🏷️ Skill Name

**Test Case Generator** — *From Spec to Full Test Suite in Minutes*

> Takes a spec `.md` file as input → Returns a complete test case suite in Gherkin/BDD format,
> grouped by feature, tagged by test type, with a Coverage Matrix & Risk Notes.

---

## ⚙️ What Gets Automated

| | Details |
|---|---|
| **Input** | A spec `.md` file (or PRD, requirements doc, pasted text) |
| **Output** | Complete test suite — **16 categories:** Core (Happy Path · Basic · Edge Cases · Negative) · Quality (Security · UI/UX · Accessibility) · Platform (Mobile · API · Performance) · Web3/DeFi (DeFi Security · Wallet · Token · Smart Contract · Blockchain) |
| **Format** | Gherkin/BDD — with tags, `Scenario Outline`, `Background`, `Examples` tables |
| **Bonus output** | Coverage Matrix · Risk Areas & Notes · Ambiguity flags |

**Problems this skill solves:**
- ✋ Writing test cases manually takes days and easily misses edge cases and security scenarios
- 📋 No coverage matrix → hard to know if coverage is complete
- 🔀 Every QC writes in a different format → inconsistent across the team
- 🧠 Depends on individual experience → new team members miss many cases

---

## ⏮️ BEFORE — Manual Process

```
Spec → QC reads spec manually → brainstorms test cases → writes them out
     → reviews again → fills in missed cases → reformats for consistency

     ⏱️ Time: 1–2 days per module
```

| Item | Reality |
|---|---|
| ⏱️ **Time spent** | **1–2 days** for an average module (3–5 features) |
| ❌ **Easily missed** | Hidden edge cases, security scenarios, accessibility, mobile behaviors |
| 📉 **Format** | Each QC writes differently — hard to standardize across team |
| 🔁 **Repetitive** | Same effort repeated from scratch every sprint |

---

## ✅ AFTER — With AI Skill

```
Spec → Feed into Claude Code + Skill → Review output → Done

     ⚡ Time: ~15–30 minutes (including review)
```

| Item | Reality |
|---|---|
| ⚡ **Time spent** | **~15–30 minutes** for the same module (including review) |
| ✅ **Coverage** | 16 test types generated automatically — including 6 Web3/DeFi categories when applicable |
| 📐 **Format** | Consistent Gherkin/BDD — importable into Cucumber / Playwright / Behave |
| 🗺️ **Visibility** | Coverage Matrix at the end of every output — instant coverage overview |
| 🚨 **Risk awareness** | Skill auto-flags spec ambiguities and recommends test data to prepare |

| Metric | Value |
|---|---|
| **Time saved** | ~90% reduction in manual test case writing time |
| **Quality gain** | Security, DeFi exploit vectors, accessibility, and mobile scenarios — the ones usually skipped — are now always covered |
| **DeFi-ready** | Web3/DeFi specs auto-trigger 6 specialized checklists: DeFi Security, Wallet, Token, Smart Contract, Financial Precision, Blockchain Errors |

---

## 🛠️ Tools & AI Used

| Tool | Role |
|---|---|
| **Claude Code** | Platform running the skill — feed spec, receive output |
| **Claude Sonnet** | AI model processing the spec and generating test cases |
| **Markdown** | Output format — portable, version-controllable, shareable |

> The skill runs entirely on **Claude Code** — no deployment, no backend, no extra accounts needed.
> Feed the spec → get output directly in the terminal.

---

## ⚠️ Limitations

| Limitation | Description |
|---|---|
| **Spec quality in = quality out** | A vague or very short spec leads to incomplete output; the skill flags gaps but cannot invent business logic |
| **No automation code** | Output is Gherkin spec only, not Cypress/Playwright scripts — a separate step is needed for test automation |
| **Domain-specific rules** | The skill doesn't know project-specific business rules unless they are explicitly stated in the spec |
| **Human review still needed** | Output should be reviewed and extended by a QC for project-specific edge cases |
| **Large specs** | Very long specs (200+ pages) may need to be split by feature before feeding in |
| **DeFi specificity** | DeFi checklists require the spec to explicitly mention protocols, attack vectors, and chain details — the skill flags gaps but cannot invent protocol-specific business logic |

---

## 🗺️ Roadmap

If given more time, the skill would be extended in this order:

### ✅ Phase 2 — Priority Matrix (Done in v1.2)
- Automatically assign `Critical / Major / Minor` priority to each scenario based on business impact
- Priority Distribution table in Coverage Matrix output

### ✅ Phase 2.5 — Web3/DeFi Support (Done in v2.0)
- 6 new test categories: `@web3`, `@wallet`, `@defi-security`, `@smart-contract`, `@token`, `@blockchain`
- DeFi Security checklist: 15 exploit vectors (reentrancy, flash loan, oracle, MEV, sandwich, etc.)
- Wallet, Token, Smart Contract, Financial Precision, DeFi Edge Cases checklists
- Blockchain Error Handling Map (12 error types)
- Conditional activation — DeFi checklists only trigger when spec contains DeFi keywords

### 🔜 Phase 3 — Test Report Template
- After generating test cases → also generate a ready-to-use report template (QC only fills in Pass/Fail)
- Skill auto-aggregates results into a **Test Summary Report**
- Full pipeline: `Spec → Test Cases → Report` — zero manual writing at any stage

### 🔜 Phase 4 — Multi-format Export
- Export to **Excel/CSV** for direct import into TestRail / Jira Xray / Notion
- Export to **Playwright test stubs** — skeleton code with describe/it blocks ready for QC to fill in

### 🔜 Phase 5 — Spec Diff Mode
- Feed two spec versions (v1 vs v2) → Skill detects changes and **generates only new/updated test cases**
- Designed for regression testing when features are updated mid-sprint

---

*Built by:* **QC Team** · *Platform:* Claude Code · *Version:* 2.0 · *Date:* 2026-02-23
