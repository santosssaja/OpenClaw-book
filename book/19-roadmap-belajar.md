# Bagian 19 — Roadmap Belajar OpenClaw 8 Minggu

Roadmap ini dibuat untuk membawamu dari **nol → paham → bisa membangun sistem OpenClaw pribadi yang aman dan rapi**.

Kita tidak akan mulai dari “pasang semua channel, aktifkan semua tools, bikin multi-agent, lalu berharap aman.” Itu jalur cepat menuju kekacauan yang terlihat futuristik.

Roadmap yang sehat:

```text id="u26g70"
Minggu 1 — Mental Model
Minggu 2 — Workspace dan Bootstrap
Minggu 3 — Skills dan Tools
Minggu 4 — Memory dan Context
Minggu 5 — Security dan Audit
Minggu 6 — Multi-Agent
Minggu 7 — Automation Workflow
Minggu 8 — Build Personal OpenClaw System
```

OpenClaw sendiri diposisikan sebagai personal AI assistant yang berjalan di perangkat sendiri dan menjawab lewat channel yang sudah dipakai user; Gateway adalah control plane, sementara produk utamanya adalah assistant yang bisa bekerja lintas channel dan tools. ([GitHub][1]) Karena OpenClaw bisa punya akses ke file lokal, tools, shell, browser, email/calendar, channel chat, dan skills, proses belajarnya harus bertahap dan security-minded, bukan asal “gas semua fitur”. Risiko security OpenClaw juga nyata, terutama dari prompt injection, malicious skills, over-permission, dan data leakage. ([The Verge][2])

---

## Prinsip Roadmap

Selama 8 minggu, pegang pola ini:

```text id="3u1oov"
Pahami dulu → dokumentasikan → batasi tools → uji aman → baru otomatisasi
```

Jangan dibalik menjadi:

```text id="xc5w4r"
Otomatisasi dulu → bingung kenapa rusak → baru belajar
```

Tujuan akhirnya bukan cuma “OpenClaw jalan”, tapi:

```text id="j4chkd"
OpenClaw bisa dipakai,
bisa diaudit,
bisa dikembangkan,
dan kalau salah, dampaknya terbatas.
```

---

# Minggu 1 — Mental Model OpenClaw

## Tujuan

Paham OpenClaw sebagai **agentic AI system**, bukan sekadar chatbot.

Setelah minggu ini, kamu harus bisa menjelaskan:

```text id="lpu4d9"
- OpenClaw itu apa,
- bedanya dengan chatbot biasa,
- apa peran Gateway,
- apa peran agent runtime,
- apa itu workspace,
- apa itu session,
- apa itu tools,
- apa itu skills,
- apa itu memory/context,
- bagaimana channel masuk ke agent.
```

OpenClaw adalah personal AI assistant yang berjalan di perangkat user dan bisa menjawab melalui channel seperti chat apps; dokumentasi/repo OpenClaw menekankan bahwa Gateway adalah control plane, bukan keseluruhan produk. ([GitHub][1])

## Materi

Pelajari:

```text id="mxcu65"
1. Agentic AI vs chatbot.
2. Gateway sebagai pusat lalu lintas.
3. Agent runtime sebagai tempat berpikir/bertindak.
4. Workspace sebagai rumah agent.
5. Tools sebagai tangan agent.
6. Skills sebagai SOP kemampuan.
7. Memory sebagai arsip jangka panjang.
8. Context sebagai meja kerja saat ini.
9. Channels sebagai pintu komunikasi.
10. Sessions sebagai ruang percakapan.
```

## Praktik

Buat diagram sendiri:

```text id="xs6ab6"
User
  ↓
Channel
  ↓
Gateway
  ↓
Session Routing
  ↓
Agent Runtime
  ↓
Context / Memory / Workspace Files
  ↓
LLM
  ↓
Tools / Skills
  ↓
Action / Response
```

Lalu tulis penjelasan 1 paragraf untuk setiap komponen.

## Output Minggu 1

Buat file:

```text id="rlgxtf"
notes/openclaw-mental-model.md
```

Isi minimal:

```markdown id="o8ezph"
# OpenClaw Mental Model

## OpenClaw in One Sentence
OpenClaw adalah personal agentic AI system yang menghubungkan user, channel, agent runtime, workspace, tools, skills, memory, dan automation.

## Main Diagram
[diagram teks]

## Core Components
- Gateway:
- Agent Runtime:
- Workspace:
- Session:
- Tools:
- Skills:
- Memory:
- Context:
- Channel:
```

## Indikator Paham

Kamu dianggap paham kalau bisa menjawab tanpa melihat catatan:

```text id="ltmhxv"
1. Kenapa OpenClaw bukan sekadar chatbot?
2. Apa beda tools dan skills?
3. Apa beda memory dan context?
4. Kenapa Gateway penting?
5. Kenapa channel adalah security boundary?
```

---

# Minggu 2 — Workspace dan Bootstrap

## Tujuan

Membangun struktur workspace yang rapi dan aman.

Minggu ini fokus ke:

```text id="2dkmjz"
- file instruksi,
- struktur folder,
- bootstrap,
- identity,
- user preferences,
- basic memory,
- basic tool policy.
```

Workspace adalah rumah agent dan working directory untuk file tools/context, terpisah dari direktori sistem/config OpenClaw. Dalam setup serius, workspace perlu diperlakukan sebagai area private karena bisa berisi memory, notes, instructions, dan hasil kerja agent. ([GitHub][1])

## Materi

Pelajari fungsi:

```text id="ca4i24"
- AGENTS.md
- SOUL.md
- TOOLS.md
- USER.md
- IDENTITY.md
- HEARTBEAT.md
- BOOTSTRAP.md
- MEMORY.md
- memory/
- notes/
- audits/
- skills/
- workflows/
```

## Praktik

Buat struktur dasar:

```text id="b9j61r"
workspace/
  AGENTS.md
  SOUL.md
  TOOLS.md
  USER.md
  IDENTITY.md
  MEMORY.md

  memory/
    projects.md
    decisions.md
    stale.md

  notes/
    openclaw-learning.md

  audits/
    security-checklist.md

  skills/
```

Tulis versi awal:

```text id="nx9tx2"
AGENTS.md  → peran agent utama
SOUL.md    → gaya dan prinsip agent
TOOLS.md   → aturan tools minimal
USER.md    → preferensi user
MEMORY.md  → memory jangka panjang ringkas
```

## Output Minggu 2

File yang harus ada:

```text id="6wvisk"
AGENTS.md
SOUL.md
TOOLS.md
USER.md
MEMORY.md
notes/openclaw-learning.md
memory/projects.md
```

Contoh isi `memory/projects.md`:

```markdown id="95adhv"
# Projects Memory

## OpenClaw Learning
Goal:
Understand OpenClaw as an agentic AI system and build a safe personal setup.

Progress:
- Week 1: Mental model.
- Week 2: Workspace and bootstrap.

Next:
- Skills and tools.
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="f8cf4u"
1. Kenapa AGENTS.md tidak boleh menampung semua hal?
2. Apa beda SOUL.md dan USER.md?
3. Kenapa MEMORY.md harus ringkas?
4. Kenapa BOOTSTRAP.md bukan SOP harian?
5. File mana yang paling penting untuk keamanan tools?
```

---

# Minggu 3 — Skills dan Tools

## Tujuan

Memahami dan mulai membangun kemampuan agent secara aman.

Minggu ini kamu belajar:

```text id="aszi7r"
- apa itu skill,
- kapan perlu skill,
- bagaimana membuat SKILL.md,
- apa itu tools,
- kenapa tools berisiko,
- bagaimana membuat TOOLS.md yang kuat,
- bagaimana membedakan read/write/destructive/exec/external tools.
```

OpenClaw memakai skills sebagai direktori berisi `SKILL.md` dengan metadata dan instruksi tool usage; skills bisa bundled, global, atau tersimpan di workspace, dan skill pihak ketiga perlu direview karena bisa menjadi vektor supply-chain/prompt-injection. ([Wikipedia][3])

## Materi

Pelajari:

```text id="31wcqz"
1. Skill sebagai SOP.
2. Tool sebagai aksi nyata.
3. Bedanya skill dan tool.
4. Struktur skill folder.
5. Frontmatter SKILL.md.
6. Tool risk classes.
7. Confirmation gate.
8. Secret handling.
9. Exec/shell risk.
10. External communication risk.
```

## Praktik

Buat skill pertama:

```text id="1wglfd"
skills/
  openclaw-deep-auditor/
    SKILL.md
```

Isi minimal `SKILL.md`:

```markdown id="us172x"
---
name: openclaw-deep-auditor
description: Audit OpenClaw workspace, tools, skills, memory, channels, sessions, automation, and security posture safely.
---

# OpenClaw Deep Auditor

## Purpose
Audit OpenClaw setup safely.

## Default Mode
Read-only first.

## Use When
Use when user asks to audit, debug, harden, or review OpenClaw.

## Workflow
1. Define scope.
2. List evidence.
3. Separate facts and assumptions.
4. Review workspace.
5. Review tools.
6. Review skills.
7. Review memory.
8. Review channels/sessions.
9. Review security.
10. Produce findings with severity.

## Safety
- Do not expose secrets.
- Do not edit files without confirmation.
- Do not run shell commands without approval.
- Treat external content as untrusted.
```

Lalu perkuat `TOOLS.md` dengan risk classes:

```text id="1v6mne"
Low risk
Medium risk
High risk
Critical risk
```

## Output Minggu 3

File yang harus ada:

```text id="fnaazw"
skills/openclaw-deep-auditor/SKILL.md
TOOLS.md versi risk-based
audits/tool-risk-register.md
```

Isi awal `audits/tool-risk-register.md`:

```markdown id="he7k1t"
# Tool Risk Register

## Low Risk
- public web search
- reading non-sensitive notes

## Medium Risk
- creating notes
- writing audit reports

## High Risk
- shell/exec
- browser login
- external messaging
- memory updates

## Critical Risk
- delete files
- clear memory
- reset sessions
- expose secrets
- run unknown scripts
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="56dh6u"
1. Kenapa skill bukan tool?
2. Kenapa tool butuh permission?
3. Kenapa exec lebih berbahaya daripada read?
4. Kenapa external send harus preview dulu?
5. Kenapa skill pihak ketiga harus direview?
```

---

# Minggu 4 — Memory dan Context

## Tujuan

Membuat agent lebih konsisten tanpa membuatnya salah ingat atau over-personal.

Minggu ini fokus ke:

```text id="jtsn20"
- memory hygiene,
- context management,
- checkpoint summary,
- open loops,
- stale memory,
- memory poisoning,
- privacy.
```

Memory OpenClaw dipahami sebagai file Markdown di workspace untuk durable facts/preferences/decisions dan daily/running notes, sedangkan context adalah informasi yang masuk ke model saat run; karena context window terbatas, memory harus curated dan tidak boleh menjadi transcript mentah. ([Wikipedia][3])

## Materi

Pelajari:

```text id="sqvyuq"
1. Memory vs context.
2. MEMORY.md vs memory/.
3. Daily notes.
4. Candidate memory.
5. Memory promotion.
6. Memory poisoning.
7. Context overload.
8. Stale entries.
9. Privacy boundaries.
```

## Praktik

Rapikan memory:

```text id="7j5lqd"
MEMORY.md
memory/projects.md
memory/decisions.md
memory/stale.md
```

Tambahkan bagian ini di `MEMORY.md`:

```markdown id="onhwgs"
## Durable Preferences
- ...

## Active Projects
- ...

## Standing Decisions
- ...

## Open Loops
- ...

## Stale / Needs Review
- ...
```

Buat workflow memory curator:

```text id="meskhs"
workflows/memory-curator.md
```

Isi:

```markdown id="85h7g1"
# Memory Curator Workflow

## Goal
Keep memory clean, safe, and useful.

## Steps
1. Review MEMORY.md.
2. Identify duplicates.
3. Identify stale entries.
4. Identify sensitive entries.
5. Propose cleanup.
6. Ask confirmation before edits.
7. Backup before changes.
8. Summarize changes.
```

## Output Minggu 4

```text id="n77gdi"
MEMORY.md yang ringkas
memory/projects.md
memory/decisions.md
memory/stale.md
workflows/memory-curator.md
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="8qfz6r"
1. Kenapa memory salah lebih berbahaya daripada lupa?
2. Apa yang layak masuk MEMORY.md?
3. Apa yang sebaiknya masuk notes/ saja?
4. Kenapa group/web/email tidak boleh langsung menulis memory permanen?
5. Bagaimana cara membuat checkpoint untuk sesi panjang?
```

---

# Minggu 5 — Security dan Audit

## Tujuan

Membangun kebiasaan audit defensif.

Minggu ini fokus ke:

```text id="a6y2wl"
- prompt injection,
- tool misuse,
- malicious skill,
- credential leak,
- workspace destruction,
- command execution risk,
- over-permission,
- insecure config,
- memory poisoning,
- data leakage,
- channel spoofing.
```

Risiko OpenClaw bukan teori kosong. Laporan publik 2026 membahas malicious skills yang bisa mencuri credentials/wallet/SSH/browser passwords, prompt injection, dan risiko sandboxing/permission yang lemah. ([The Verge][2]) Penelitian terbaru juga menyoroti bahwa multi-agent personal assistants dengan akses luas dapat mengalami unintended actions, exfiltration, dan malicious skill execution; salah satu paper menyebut 36,4% built-in skills dalam analisis mereka masuk kategori high/critical risk. ([arXiv][4])

## Materi

Pelajari:

```text id="fg37el"
1. Threat model OpenClaw.
2. Prompt injection dari external content.
3. Skill supply-chain risk.
4. Exec/shell risk.
5. Browser profile risk.
6. Secret handling.
7. Sandboxing.
8. Least privilege.
9. Channel/session isolation.
10. Incident response.
```

## Praktik

Buat audit report pertama:

```text id="me5dnd"
audits/2026-05-openclaw-security-audit.md
```

Gunakan format:

```markdown id="68gq6a"
# OpenClaw Security Audit

## Scope
Workspace, tools, skills, memory, channels.

## Verified Facts
- ...

## Assumptions
- ...

## Summary Risk
Low / Medium / High / Critical

## Findings

### Finding: ...
Severity:
Area:
Evidence:
Risk:
Recommendation:
Safe Next Step:

## Priority Fixes
1.
2.
3.
```

Buat juga:

```text id="l9harp"
audits/security-checklist.md
```

Isi minimal:

```text id="u17jld"
[ ] Exec disabled in main agent.
[ ] External send requires confirmation.
[ ] MEMORY.md has no secrets.
[ ] Third-party skills reviewed.
[ ] Channel DM uses allowlist/pairing.
[ ] Group memory disabled.
[ ] Backups exist.
[ ] Browser profile isolated.
```

## Output Minggu 5

```text id="hbknm9"
audits/2026-05-openclaw-security-audit.md
audits/security-checklist.md
TOOLS.md diperkuat
SKILL.md auditor diperkuat
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="88lig7"
1. Apa itu prompt injection di agentic AI?
2. Kenapa malicious skill berbahaya walau cuma Markdown?
3. Kenapa no-secret-in-memory penting?
4. Kenapa exec harus dipisah dari main agent?
5. Kenapa sandbox bukan pengganti confirmation gate?
```

---

# Minggu 6 — Multi-Agent

## Tujuan

Belajar memecah OpenClaw menjadi beberapa agent dengan batas yang jelas.

Jangan mulai multi-agent terlalu cepat. Multi-agent yang baik bukan menambah nama agent, tapi memisahkan:

```text id="41hc4b"
- tools,
- memory,
- workspace,
- channel,
- risk level,
- workflow.
```

OpenClaw mendukung multi-agent side-by-side dalam satu Gateway, dengan agent sebagai scope lengkap yang mencakup workspace, state/auth/model registry/config/session store, lalu channel/account diarahkan ke agent melalui bindings. ([GitHub][1])

## Materi

Pelajari:

```text id="9192r0"
1. Single-agent vs multi-agent.
2. Agent boundaries.
3. Workspace per agent.
4. Memory per agent.
5. Tools per agent.
6. Skills per agent.
7. Channel routing/bindings.
8. Sub-agent delegation risk.
9. Public vs private channel trust.
10. Security agent read-only.
```

## Praktik

Rancang, belum harus langsung aktifkan semua:

```text id="l9tj3z"
Personal Agent
Coding Agent
Research Agent
Security Agent
Writing Agent
Maintenance Agent
```

Buat file:

```text id="xqxebk"
notes/multi-agent-design.md
```

Isi:

```markdown id="75d8p0"
# Multi-Agent Design

## Personal Agent
Purpose:
Tools:
Memory:
Channels:
Risks:

## Coding Agent
Purpose:
Tools:
Memory:
Channels:
Risks:

## Security Agent
Purpose:
Tools:
Memory:
Channels:
Risks:
```

Jika sudah siap, mulai dari 2 agent saja:

```text id="8ef2qp"
1. Personal Agent
2. Security Agent
```

Lalu tambahkan Coding Agent setelah workspace dan sandbox siap.

## Output Minggu 6

```text id="m93r7a"
notes/multi-agent-design.md
workspace-security/ draft
workspace-coding/ draft
agent boundary checklist
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="7v3cwp"
1. Kapan cukup satu agent?
2. Kapan perlu multi-agent?
3. Kenapa coding agent tidak perlu memory personal?
4. Kenapa security agent default read-only?
5. Kenapa public channel tidak boleh masuk high-privilege agent?
```

---

# Minggu 7 — Automation Workflow

## Tujuan

Mendesain automation secara aman.

Fokusnya bukan “apa saja yang bisa otomatis”, tapi:

```text id="2q7lwz"
Apa yang aman untuk diotomatisasi?
Apa yang harus tetap manual?
Apa yang butuh approval?
Apa yang harus dicatat di logs?
```

OpenClaw automation mencakup cron untuk jadwal presisi, heartbeat untuk monitoring rutin batch berkala, hooks untuk event tertentu, dan standing orders untuk konteks/authority boundaries. Karena automation dapat berjalan tanpa user sedang memperhatikan, scope dan failure handling harus jelas. ([GitHub][1])

## Materi

Pelajari:

```text id="p1fq7s"
1. Cron jobs.
2. Heartbeat.
3. Hooks.
4. Standing orders.
5. Reminder vs automation.
6. Output destination.
7. Failure handling.
8. Read-only automation.
9. Automation memory risk.
10. Automation security policy.
```

## Praktik

Buat file:

```text id="i86o01"
workflows/automation-policy.md
```

Isi:

```markdown id="u2mjqv"
# Automation Policy

## Allowed Automation
- reminders,
- weekly learning review,
- monthly security report,
- read-only monitoring,
- draft generation.

## Not Allowed Automatically
- delete files,
- clear memory,
- edit config,
- run shell commands,
- send external messages,
- install/update dependencies,
- access secrets.

## Required Fields
Every automation must define:
- purpose,
- schedule,
- agent,
- allowed tools,
- output destination,
- failure handling,
- stop instruction.
```

Buat satu automation rendah risiko secara konseptual:

```text id="ze5bhv"
Weekly OpenClaw Learning Review
```

Template:

```markdown id="bpg5os"
# Weekly OpenClaw Learning Review

Purpose:
Summarize what user learned this week about OpenClaw.

Schedule:
Every Sunday evening.

Allowed Tools:
- read learning notes,
- write weekly summary.

Not Allowed:
- edit config,
- run shell,
- send to group,
- clear memory.

Output:
notes/weekly-openclaw-review.md
```

## Output Minggu 7

```text id="lzosdp"
workflows/automation-policy.md
workflows/weekly-learning-review.md
audits/automation-risk-register.md
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="srb08r"
1. Apa beda cron, heartbeat, hooks, dan standing orders?
2. Kenapa automation tidak boleh destructive default?
3. Kenapa automation memory write berisiko?
4. Apa saja field wajib automation?
5. Kenapa output destination harus jelas?
```

---

# Minggu 8 — Build Personal OpenClaw System

## Tujuan

Menyatukan semuanya menjadi sistem OpenClaw pribadi yang rapi, aman, dan bisa dikembangkan.

Output akhirnya bukan cuma catatan, tapi **blueprint personal OpenClaw system**.

---

## Materi

Review semua:

```text id="17zcbw"
- mental model,
- workspace,
- bootstrap,
- tools,
- skills,
- memory,
- context,
- security,
- multi-agent,
- automation,
- troubleshooting,
- audit checklist.
```

## Praktik

Buat blueprint final:

```text id="73wk35"
notes/personal-openclaw-system-blueprint.md
```

Isi:

```markdown id="bzobv2"
# Personal OpenClaw System Blueprint

## 1. Purpose
What this OpenClaw setup is for.

## 2. Core Agent
Main personal agent role.

## 3. Channels
Allowed channels and policies.

## 4. Workspace Structure
File/folder layout.

## 5. Tools Policy
Allowed, restricted, prohibited tools.

## 6. Skills
Enabled local skills.

## 7. Memory Policy
What to store and not store.

## 8. Security Policy
Hardening rules.

## 9. Workflow List
- learning
- research
- memory curator
- audit
- coding
- writing

## 10. Multi-Agent Roadmap
What agents to add later.

## 11. Automation Roadmap
Safe automations to add later.

## 12. Troubleshooting Runbook
First commands/checks.

## 13. Audit Checklist
Monthly checklist.

## 14. Next 30 Days
Practical implementation plan.
```

## Output Minggu 8

Minimal:

```text id="98kmuc"
notes/personal-openclaw-system-blueprint.md
AGENTS.md
SOUL.md
TOOLS.md
USER.md
MEMORY.md
skills/openclaw-deep-auditor/SKILL.md
workflows/
audits/
```

## Indikator Paham

Kamu paham kalau bisa menjawab:

```text id="ugytcr"
1. Apa setup OpenClaw yang paling cocok untukmu sekarang?
2. Tool apa yang sengaja tidak kamu aktifkan dulu?
3. Skill apa yang wajib ada?
4. Memory apa yang boleh disimpan?
5. Channel mana yang boleh memicu agent?
6. Apa yang harus dilakukan kalau agent mulai agresif?
7. Apa langkah pertama kalau channel tidak merespons?
8. Kapan kamu baru perlu multi-agent?
```

---

# Checklist Kelulusan 8 Minggu

Kalau selesai roadmap ini, kamu harus punya:

```text id="wff9t8"
[ ] Mental model OpenClaw 1 halaman.
[ ] Workspace rapi.
[ ] AGENTS.md.
[ ] SOUL.md.
[ ] TOOLS.md.
[ ] USER.md.
[ ] MEMORY.md ringkas.
[ ] memory/projects.md.
[ ] Skill openclaw-deep-auditor.
[ ] Tool risk register.
[ ] Security checklist.
[ ] Memory curator workflow.
[ ] Research workflow.
[ ] Coding workflow.
[ ] Automation policy.
[ ] Multi-agent design.
[ ] Troubleshooting runbook.
[ ] Personal OpenClaw System Blueprint.
```

Kalau semua ini ada, kamu bukan cuma “pakai OpenClaw”. Kamu sudah mulai **mendesain sistem OpenClaw**.

---

# Rencana Harian Ringkas

Kalau 8 minggu terasa besar, gunakan ritme harian:

```text id="0cmd5n"
Hari 1: baca/pahami 1 konsep
Hari 2: buat catatan
Hari 3: buat file/template
Hari 4: test dengan contoh
Hari 5: audit risiko
Hari 6: perbaiki
Hari 7: review mingguan
```

Pola ini bisa dipakai setiap minggu.

---

# Kesalahan Belajar yang Perlu Dihindari

## 1. Terlalu Cepat Aktifkan Banyak Tools

Masalah:

```text id="b1f24l"
Kamu belum paham boundary, tapi agent sudah bisa bertindak.
```

Solusi:

```text id="lta40i"
Aktifkan tools bertahap. Mulai read-only.
```

---

## 2. Bikin Banyak Agent Terlalu Cepat

Masalah:

```text id="4ox74s"
Kamu punya banyak agent, tapi belum jelas siapa melakukan apa.
```

Solusi:

```text id="jqqrzz"
Mulai satu agent, lalu pecah berdasarkan risiko nyata.
```

---

## 3. Menganggap Memory Itu Ajaib

Masalah:

```text id="tp9crd"
Agent tetap lupa karena memory tidak disimpan/dimuat/diringkas.
```

Solusi:

```text id="jmr9an"
Buat checkpoint summary dan open loops.
```

---

## 4. Mengabaikan Security

Masalah:

```text id="3v7tcg"
Agentic system tanpa security policy adalah ide bagus yang sedang menunggu jadi insiden.
```

Solusi:

```text id="fyn098"
TOOLS.md, read-only first, no secrets, confirmation gate.
```

---

## 5. Mengandalkan Prompt Panjang Saja

Masalah:

```text id="zg83oc"
Prompt panjang tidak otomatis membuat sistem stabil.
```

Solusi:

```text id="5nbip4"
Ubah prompt berulang menjadi workflow dan skill.
```

---

# Roadmap Setelah 8 Minggu

Setelah selesai, lanjutkan ke fase pengembangan:

## Bulan 2 — Stabilization

```text id="76cghl"
- audit memory,
- audit tools,
- test skills,
- rapikan workflows,
- backup workspace,
- dokumentasikan config.
```

## Bulan 3 — Multi-Agent Expansion

```text id="re9xlh"
- tambah Security Agent,
- tambah Coding Agent,
- tambah Research Agent,
- pisahkan workspace,
- sandbox coding agent.
```

## Bulan 4 — Automation Limited

```text id="065sca"
- weekly review,
- monthly audit,
- reminder belajar,
- no destructive automation,
- logging lebih rapi.
```

## Bulan 5 — Personal System Optimization

```text id="jcckqi"
- dashboard/notes system,
- better workflows,
- skill improvement,
- prompt library,
- incident runbook.
```

---

# Ringkasan Bagian 19

Roadmap belajar OpenClaw harus bertahap:

```text id="wtxm0u"
Minggu 1: Mental Model
Minggu 2: Workspace dan Bootstrap
Minggu 3: Skills dan Tools
Minggu 4: Memory dan Context
Minggu 5: Security dan Audit
Minggu 6: Multi-Agent
Minggu 7: Automation Workflow
Minggu 8: Build Personal OpenClaw System
```

Prinsip paling penting:

```text id="tnv9cz"
Jangan mengejar fitur dulu.
Kejar pemahaman, struktur, safety, dan auditability.
```

OpenClaw kuat karena bisa bertindak lewat tools, channel, skills, dan automation. Tapi kekuatan itu juga membuatnya perlu dibangun dengan least privilege, sandboxing, confirmation gate, review skills, memory hygiene, dan audit berkala. Laporan publik dan riset terbaru menyoroti risiko nyata seperti malicious skills, prompt injection, credential exfiltration, dan unsafe autonomy, jadi roadmap belajar harus memasukkan security sejak awal, bukan setelah semuanya berjalan. ([The Verge][2])

Opini teknisku: **kalau kamu mengikuti roadmap ini, kamu tidak cuma jadi pengguna OpenClaw—kamu mulai berpikir seperti arsitek sistem agentic AI.** Dan itu level yang jauh lebih kuat, karena kamu tidak hanya tahu cara “menjalankan agent”, tapi tahu cara membuat agent tetap berguna, aman, dan bisa dipulihkan saat terjadi masalah.

Bagian berikutnya kita akan membahas **Bagian 20 — Output Akhir**, yaitu ringkasan 1 halaman, diagram mental model OpenClaw, checklist audit, template final `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `BOOTSTRAP.md`, `SKILL.md`, roadmap ringkas, dan rekomendasi setup terbaik   untuk kebutuhanmu.

Ke [Bagian 20: Output Akhir](20-output-akhir.md)

[1]: https://github.com/openclaw/openclaw?utm_source=chatgpt.com "OpenClaw — Personal AI Assistant"
[2]: https://www.theverge.com/news/874011/openclaw-ai-skill-clawhub-extensions-security-nightmare?utm_source=chatgpt.com "OpenClaw's AI 'skill' extensions are a security nightmare"
[3]: https://en.wikipedia.org/wiki/OpenClaw?utm_source=chatgpt.com "OpenClaw"
[4]: https://arxiv.org/abs/2603.28807?utm_source=chatgpt.com "SafeClaw-R: Towards Safe and Secure Multi-Agent Personal Assistants"
