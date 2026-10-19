# APK Threat Analysis Platform — with C2 Detection

**In one sentence:** an investigator uploads a suspicious Android app, and within a minute the tool explains — in plain language — what the app secretly does, where it sends stolen data, and produces a report a police officer can act on.


---

## Start here: the problem (no tech background needed)

Imagine this real, everyday situation in India:

> Someone gets a WhatsApp message: *"Instant loan, no paperwork — just install this app."* They install it. The app looks like a normal loan app. But quietly, in the background, it copies **all their contacts and text messages** and sends them to a criminal. Weeks later the victim is being **blackmailed and harassed** — the gang threatens to message their whole contact list. This is a real pattern behind loan-app extortion, "digital arrest" scams, and OTP/bank-fraud apps.

Now put yourself in the **police investigator's** chair. They've seized a phone. On it is a suspicious app. They need to answer simple questions, fast:

- **What does this app actually do?** (Does it steal contacts? Read your SMS? Record audio?)
- **Where is the stolen data going?** (Which server, which Telegram account, which person can we go after?)
- **Can we prove it, in a way that stands up in court?**

Today, getting those answers is **slow and fragmented**. The app has to go to a specialist forensic lab, the analyst juggles several different tools by hand, and it can take days. Most investigators are not programmers and can't read app code. That delay helps criminals and hurts victims.

**A few terms, in plain words:**
- **APK** = the installable file for an Android app (like a `.exe` on Windows). It's just the app, packaged up.
- **C2 ("command-and-control") server** = the criminal's "home base" computer that the malicious app secretly talks to — to receive instructions and to receive the stolen data. **Finding the C2 is the goal**, because it points at the people behind the crime.
- **Malware** = software built to do harm (spy, steal, extort).

---

## What this tool does

It's a single web app that does the analyst's job automatically and explains it for a non-expert. You **upload the suspicious APK**, and it:

1. **Looks inside the app without running it** ("static analysis") — reads what permissions it asks for (contacts? SMS? camera?), and pulls out hidden clues like web addresses, phone numbers, and secret tokens.
2. **Safely runs the app in a sealed sandbox** ("dynamic analysis") — a throwaway, isolated Android phone-on-a-computer with no real internet access, so the malware can misbehave where it can't hurt anyone. The tool **watches everything it does** and, crucially, **catches the data the app tries to send right before it gets scrambled (encrypted)** — so it can read the real destination even when the app tries to hide it.
3. **Connects the two views** — and flags the tell-tale lie: the app's paperwork *says* it talks to its own friendly server, but in reality it's shipping your text messages to a Telegram account. **That contradiction is the crime.**
4. **Writes a clear report** — a plain-English summary ("HIGH risk: disguised loan app, steals contacts and SMS, sends them to a Telegram channel"), the technical evidence, and a **ready-to-sign legal worksheet** for the officer.

### A simple analogy
Think of it like a **customs X-ray plus a sealed test chamber** for a suspicious package. The X-ray (static) shows what's inside without opening it. The sealed chamber (dynamic) lets you safely watch what the package *does* when activated — without letting anything escape. Then you get a printed report explaining what you found.

---

## What makes it better than just "scanning" an app

Lots of tools list an app's permissions or give it a "score." Investigators need more. This tool focuses on the parts that actually crack a case:

- **It finds the real destination of stolen data**, even when the app encrypts its traffic to hide it — by reading the data *before* it gets scrambled.
- **It catches the lie** between what the app claims and what it really does.
- **It speaks the investigator's language** — a plain-English finding at the top, and a forensic report at the bottom, no coding required.
- **It's tuned to Indian cyber-crime** — fake loan apps, sextortion, OTP/SMS theft, and the Telegram/Discord channels these gangs actually use.

---

## What this tool is **not** (we're deliberately honest about this)

This honesty is a feature — for a police/forensic audience, overclaiming is the fastest way to lose trust.

- It gives **investigative leads, not a guilty verdict.** A "CRITICAL" result means *"examine this first,"* not *"this person is guilty."* A human always decides.
- It **does not make evidence court-admissible by itself.** That still needs lawful seizure, proper handling, a **human-signed legal certificate**, and the examiner's testimony. The tool *prepares* a pre-filled certificate worksheet — but it leaves the signature blank, because **software cannot legally certify itself.**
- **"We saw nothing" never means "it's clean."** Clever malware can detect it's being watched and stay quiet. When that happens, the tool says **"inconclusive — possible evasion,"** not "safe."

---

## Who it's for

- **Police investigators** — get fast, readable answers on a seized app and a report to take forward.
- **Cyber-forensics examiners (e.g. CCITR)** — a defensible, repeatable analysis with proper evidence handling.
- **Anyone triaging suspicious Android apps** for a lawful, authorized investigation.

---

## How it works (the 4-step flow, simply)

```
  1. UPLOAD          2. LOOK INSIDE        3. SAFELY RUN IT      4. REPORT
  the suspicious  →  (read permissions  →  in a sealed,       →  plain-English
  app (APK)          + hidden clues)       isolated sandbox       finding + a
                                           and watch it           court-ready
                                           steal & "phone          evidence pack
                                           home"
```

The whole thing runs **offline** (no internet needed during a demo), and **real malware is only ever run inside a throwaway, sealed virtual machine** that is wiped after every use — never on the investigator's own computer.

---

## For technical readers

In short:

- **Backend:** Python 3.11 + FastAPI; static analysis via androguard; controlled dynamic analysis via an Android emulator + Frida instrumentation (including a native `SSL_write` hook to recover plaintext before encryption) + mitmproxy + packet capture; conjunctive, calibrated C2 scoring; MITRE ATT&CK Mobile mapping; encrypted evidence store (SQLCipher) with a hash-chained audit trail.
- **Frontend:** Next.js + Tailwind + shadcn/ui investigator dashboard (summary, C2 candidates, network graph, timeline, report).
- **Output:** an examiner's evidence package (PDF) including a pre-filled, **unsigned** Bharatiya Sakshya Adhiniyam (BSA) 2023 §63 certificate worksheet.
- **Honest by design:** results are investigative-priority leads with a stated benchmark and confidence interval — not a self-certified verdict.

