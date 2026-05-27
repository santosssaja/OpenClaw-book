# Bagian 18 — Rekomendasi Konfigurasi OpenClaw

Bagian ini fokus ke **setup yang masuk akal berdasarkan level pengguna**. Bukan “satu config untuk semua orang”, karena konfigurasi OpenClaw sangat tergantung pada:

```text
- kamu pakai channel apa,
- agent dipakai sendiri atau banyak orang,
- tools apa yang aktif,
- ada shell/exec atau tidak,
- ada browser login atau tidak,
- memory dipakai sejauh apa,
- butuh multi-agent atau belum,
- automation jalan otomatis atau tidak,
- workspace berisi data sensitif atau tidak.
```

Dokumentasi OpenClaw sendiri menyebut config umum dipakai untuk menghubungkan channel, mengatur siapa yang boleh mengirim pesan ke bot, memilih model/tools/sandboxing/automation, serta menyesuaikan sessions, media, networking, dan UI. Kalau config belum ada, OpenClaw memakai safe defaults, tetapi begitu kamu menambah channel/tools/automation, kamu harus mulai eksplisit soal boundary. ([OpenClaw][1])

Prinsip besarnya:

> **Konfigurasi OpenClaw yang baik bukan yang paling canggih, tapi yang paling sesuai dengan risiko dan kebutuhan.**

---

# 18.1 Tiga Level Konfigurasi

Kita bagi menjadi tiga level:

```text
Level 1 — Pemula
Fokus: aman, sederhana, satu agent, tools minimal.

Level 2 — Menengah
Fokus: skills khusus, memory rapi, workflow terbatas, logging, backup.

Level 3 — Advanced
Fokus: multi-agent, routing, sandbox, permission separation, automation, audit berkala.
```

Kalau kamu baru mulai, jangan langsung lompat ke advanced. Multi-agent + browser + shell + automation + banyak channel itu seperti belajar nyetir sambil bawa truk gandeng di jalan pegunungan. Bisa, tapi kenapa harus hari pertama?

---

# 18.2 Prinsip Konfigurasi yang Berlaku untuk Semua Level

Sebelum masuk level, ini prinsip universal.

```text
1. Mulai dari satu agent dulu.
2. Aktifkan channel sedikit dulu.
3. Jangan open DM dengan tools kuat.
4. Jangan aktifkan exec di main personal agent.
5. Gunakan read-only first untuk audit.
6. Pisahkan coding/shell dari personal assistant.
7. Jangan pakai browser profile pribadi untuk agent.
8. Jangan simpan secret di prompt/memory.
9. Backup workspace sebelum perubahan besar.
10. Gunakan confirmation gate untuk aksi berisiko.
```

OpenClaw security docs menekankan batas trust Gateway, risiko prompt injection dari konten tidak tepercaya, kebutuhan least privilege, sandboxing, dan kontrol terhadap tool-enabled agents. Ini penting karena OpenClaw bukan hostile multi-tenant isolation di satu shared gateway; kamu tetap perlu mendesain boundary sendiri dengan benar. ([OpenClaw][2])

---

# 18.3 Level Pemula — Aman, Sederhana, Sedikit Tools

## Tujuan

Level pemula cocok kalau kamu:

```text
- baru belajar OpenClaw,
- memakai sendiri,
- belum butuh multi-agent,
- belum butuh shell/exec,
- belum butuh automation kompleks,
- ingin memahami dasar dulu,
- ingin menghindari risiko rusak/bocor.
```

Targetnya bukan “agent bisa melakukan semuanya”, tapi:

```text
Agent bisa membantu belajar, mencatat, menjawab, membuat rencana, dan melakukan riset ringan dengan aman.
```

---

## Arsitektur Pemula

```text
Telegram DM / WebChat lokal
        ↓
Gateway
        ↓
Main Personal Agent
        ↓
Workspace utama
        ↓
Tools minimal:
- read notes
- write notes jika diminta
- web_search/web_fetch
- no exec
- no browser login
- no external send otomatis
```

---

## Channel Setup Pemula

Rekomendasi:

```text
- WebChat lokal untuk eksperimen.
- Telegram DM pribadi jika ingin mobile.
- Group dimatikan dulu.
- DM hanya allowlist atau pairing.
```

Dokumentasi config OpenClaw mencontohkan penggunaan allowlist seperti `allowFrom` untuk membatasi siapa yang dapat mengirim pesan, dan dokumentasi channel/security juga menekankan pairing/allowlist sebagai bagian penting dari kontrol akses channel. ([OpenClaw][3])

Contoh pola config ilustratif:

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace"
    }
  },

  session: {
    dmScope: "per-channel-peer"
  },

  channels: {
    telegram: {
      enabled: true,
      dmPolicy: "allowlist",
      allowFrom: ["OWNER_TELEGRAM_ID"],
      groupPolicy: "disabled"
    }
  }
}
```

Catatan penting: contoh di atas adalah **pola konfigurasi**, bukan jaminan field final untuk semua versi. Untuk editing config aktual, cek schema/dokumentasi versi OpenClaw yang kamu pakai.

---

## Tools Pemula

Aktifkan sedikit saja:

```text
Boleh:
- read file workspace non-sensitive
- write notes jika user meminta
- web_search/web_fetch
- message reply ke session saat ini

Jangan dulu:
- exec
- process
- browser login
- external email/message send
- gateway control tools
- session reset tools
- destructive file tools
```

Mengapa `exec` jangan dulu? Karena shell/terminal punya blast radius besar. Bahkan kalau file write/edit dimatikan, shell yang aktif tetap bisa mengubah file lewat command. Jadi untuk main personal agent, paling aman: **no exec by default**. Prinsip ini selaras dengan hardening resmi OpenClaw yang menekankan strict tool policy dan sandbox/least privilege untuk tool-enabled agents. ([OpenClaw][2])

---

## Workspace Pemula

Struktur cukup begini:

```text
workspace/
  AGENTS.md
  SOUL.md
  TOOLS.md
  USER.md
  MEMORY.md

  memory/
    projects.md
    decisions.md
    stale.md

  notes/
    openclaw-learning.md
    app-ideas.md

  skills/
    openclaw-deep-auditor/
      SKILL.md

  audits/
    security-checklist.md
```

Jangan langsung bikin terlalu banyak folder. Fondasi dulu.

---

## `TOOLS.md` Pemula

Isi minimal yang harus ada:

```markdown
# TOOLS.md

## Core Rule

Use the minimum tool necessary.
Start read-only.
Ask confirmation before risky actions.

## Allowed Automatically

- Answer from available context.
- Read relevant non-sensitive workspace files.
- Create notes when user explicitly asks.
- Search public web information when needed.

## Requires Confirmation

- Edit important files.
- Update long-term memory.
- Send external messages.
- Create automation.
- Use browser login.
- Run shell commands.
- Delete or overwrite files.

## Disabled by Default

- exec
- process
- destructive actions
- external send
- browser login

## Secret Handling

Never print API keys, tokens, passwords, private keys, cookies, or credentials.
```

---

## Memory Pemula

Memory jangan ambisius.

Simpan:

```text
- preferensi komunikasi,
- proyek aktif,
- progress belajar,
- keputusan eksplisit.
```

Jangan simpan:

```text
- emosi sesaat sebagai label permanen,
- rahasia pribadi,
- token/password/API key,
- semua isi percakapan,
- izin destructive action.
```

Contoh `MEMORY.md`:

```markdown
# MEMORY.md

## Durable Preferences
- User prefers Indonesian.
- User likes deep, structured explanations for complex technical topics.
- User values honesty about uncertainty.

## Active Projects
- Learning OpenClaw as an agentic AI system.

## Standing Decisions
- Start OpenClaw audit tasks in read-only mode.
- Ask confirmation before destructive actions or external communication.

## Open Loops
- Continue OpenClaw deep-dive.
```

---

## Backup Pemula

Minimal:

```text
- copy workspace sebelum perubahan besar,
- simpan backup AGENTS.md/SOUL.md/TOOLS.md/MEMORY.md,
- jangan edit semua file sekaligus.
```

Lebih baik:

```bash
git init
git add .
git commit -m "baseline workspace"
```

Jalankan hanya jika kamu yakin berada di folder workspace yang benar.

---

## Checklist Level Pemula

```text
[ ] Hanya 1 agent utama.
[ ] Hanya 1–2 channel.
[ ] DM pakai allowlist/pairing.
[ ] Group dimatikan dulu.
[ ] Exec disabled.
[ ] Browser login disabled.
[ ] External send wajib konfirmasi.
[ ] MEMORY.md ringkas.
[ ] TOOLS.md punya confirmation gate.
[ ] Workspace dibackup.
[ ] OpenClaw Deep Auditor skill dibuat lokal, bukan global.
```

---

# 18.4 Level Menengah — Skills Khusus, Memory Rapi, Workflow Terbatas

## Tujuan

Level menengah cocok kalau kamu sudah:

```text
- paham workspace,
- punya beberapa workflow berulang,
- mulai pakai skill,
- ingin coding agent terbatas,
- ingin security audit rutin,
- mulai butuh logging/backup,
- ingin automation ringan.
```

Di level ini, kamu mulai membagi peran, tapi belum full multi-agent kompleks.

---

## Arsitektur Menengah

```text
Telegram DM / WebChat
        ↓
Main Personal Agent
        ↓
Workspace utama
        ↓
Skills:
- openclaw-deep-auditor
- researcher
- memory-curator
- learning-coach

Opsional:
Coding workflow masih bisa manual,
atau mulai pisahkan coding-agent kecil.
```

Kalau mulai ada coding serius:

```text
Personal Agent
  - no exec
  - notes/memory/web

Coding Agent
  - repo read/write
  - apply_patch
  - exec test/lint terbatas
  - sandbox
```

OpenClaw mendukung multi-agent side-by-side dalam satu Gateway, dan skill dapat dimuat dari workspace agent plus shared roots lalu difilter dengan allowlist efektif per agent. Ini berarti di level menengah kamu sudah bisa mulai membatasi skill per agent, bukan semua skill terlihat oleh semua agent. ([OpenClaw][4])

---

## Skills Menengah

Struktur:

```text
workspace/
  skills/
    openclaw-deep-auditor/
      SKILL.md
    researcher/
      SKILL.md
    memory-curator/
      SKILL.md
    learning-coach/
      SKILL.md
```

Aturan:

```text
- Jangan install skill random secara global.
- Review SKILL.md sebelum aktif.
- Skill harus punya “When to Use” dan “When Not to Use”.
- Skill tidak boleh override TOOLS.md.
- Skill audit/security default read-only.
```

---

## Memory Menengah

Mulai pisahkan memory:

```text
memory/
  projects.md
  decisions.md
  preferences.md
  stale.md
  2026-05-27.md
```

`MEMORY.md` tetap ringkas:

```text
MEMORY.md = ringkasan tahan lama
memory/   = detail/progress/catatan temporal
notes/    = catatan biasa
```

Tambahkan policy:

```markdown
## Memory Policy

Only store durable and useful information.
Use candidate memory before promoting to MEMORY.md.
Do not store sensitive data without explicit permission.
Mark stale information.
```

---

## Workflow Menengah

Buat folder:

```text
workflows/
  openclaw-audit.md
  memory-curator.md
  research-agent.md
  coding-agent.md
  learning-agent.md
```

Workflow yang harus punya SOP:

```text
- audit OpenClaw,
- cleanup memory,
- riset,
- coding/debugging,
- learning/progress tracking.
```

Prinsip:

```text
Workflow dulu, automation belakangan.
```

Jangan otomatisasi proses yang SOP-nya belum jelas. Automation OpenClaw mencakup cron, heartbeat, hooks, dan standing orders; masing-masing punya fungsi berbeda, jadi sebelum diaktifkan kamu harus jelas apakah butuh jadwal presisi, monitoring rutin, reaksi event, atau boundary permanen. ([OpenClaw][5])

---

## Tools Menengah

Main Personal Agent:

```text
Allow:
- read notes
- write notes
- web_search/web_fetch
- memory update terbatas
- create draft

Deny:
- exec
- process
- destructive actions
- browser login default
- external send without confirmation
```

Coding Agent opsional:

```text
Allow:
- read repo
- edit/apply_patch repo
- exec test/lint/build terbatas
- git status/diff

Require confirmation:
- install/update dependency
- database migration
- delete/move files
- broad refactor
```

Security/OpenClaw Auditor:

```text
Allow:
- read workspace/config/logs yang relevan
- web_search official docs

Deny:
- write
- edit
- apply_patch
- exec default
- external message
```

---

## Sandbox Menengah

Mulai gunakan sandbox untuk agent yang berisiko:

```text
Coding Agent:
- sandbox enabled
- workspaceAccess rw hanya untuk repo/proyek

Security Agent:
- sandbox enabled
- workspaceAccess ro

Research Agent:
- browser isolated jika butuh
- no exec
```

OpenClaw sandboxing bersifat opsional dan dikontrol lewat konfigurasi seperti `agents.defaults.sandbox` atau per-agent sandbox; tujuannya mengurangi blast radius tool execution, sementara Gateway tetap berjalan di host. ([OpenClaw][6])

Contoh pola ilustratif:

```json5
{
  agents: {
    list: [
      {
        id: "security",
        workspace: "~/.openclaw/workspace-security",
        sandbox: {
          mode: "all",
          workspaceAccess: "ro"
        },
        tools: {
          allow: ["read", "web_search", "web_fetch"],
          deny: ["write", "edit", "apply_patch", "exec", "process", "browser", "message"]
        }
      },
      {
        id: "coding",
        workspace: "~/.openclaw/workspace-coding",
        sandbox: {
          mode: "all",
          workspaceAccess: "rw"
        },
        tools: {
          allow: ["read", "write", "edit", "apply_patch", "exec", "web_search"],
          deny: ["message"]
        }
      }
    ]
  }
}
```

Tetap verifikasi field aktual dengan dokumentasi/config schema OpenClaw versimu.

---

## Automation Menengah

Boleh mulai automation ringan.

Contoh aman:

```text
- reminder belajar mingguan,
- weekly review progress,
- laporan audit read-only bulanan,
- ringkasan memory candidate,
- backup reminder.
```

Jangan dulu:

```text
- auto delete files,
- auto edit config,
- auto send messages to group,
- auto run shell commands,
- auto install/update,
- auto memory promotion tanpa review.
```

Cron OpenClaw adalah scheduler bawaan Gateway yang menyimpan job, membangunkan agent pada waktu yang tepat, dan dapat mengirim output kembali ke chat channel atau webhook endpoint. Karena cron dapat menghasilkan output otomatis, scope dan destination harus sangat jelas. ([OpenClaw][7])

---

## Logging dan Audit Menengah

Buat folder:

```text
audits/
  2026-05-openclaw-audit.md
  tool-risk-register.md
  memory-audit.md
  channel-audit.md

logs/
  incident-notes.md
  manual-debug-notes.md
```

Minimal audit bulanan:

```text
[ ] Tools aktif.
[ ] Exec aktif di agent mana.
[ ] Channel dmPolicy.
[ ] Group policy.
[ ] Memory sensitif.
[ ] Skills baru.
[ ] Automation aktif.
[ ] Backup terakhir.
```

---

## Checklist Level Menengah

```text
[ ] Main personal agent tetap no exec.
[ ] Skill lokal sudah dibuat.
[ ] Skill per-agent mulai dibatasi.
[ ] Memory dipisah: MEMORY.md vs memory/.
[ ] Workflow ditulis sebelum automation.
[ ] Coding agent dipisah jika butuh shell.
[ ] Security auditor read-only.
[ ] Sandbox mulai dipakai.
[ ] Backup/git aktif.
[ ] Audit bulanan ada.
[ ] Automation hanya read-only/ringan.
```

---

# 18.5 Level Advanced — Multi-Agent, Routing, Sandbox, Audit Berkala

## Tujuan

Level advanced cocok kalau kamu:

```text
- sudah paham OpenClaw cukup kuat,
- punya banyak workflow,
- butuh pemisahan tools/memory/channel,
- ingin coding agent serius,
- ingin research/writing/security agent terpisah,
- punya automation,
- ingin setup lebih production-like.
```

Di level ini, kamu tidak lagi mengandalkan satu agent serbaguna.

---

## Arsitektur Advanced

```text
Telegram DM Owner
  → Personal Agent

Discord #coding / CLI dev
  → Coding Agent

WebChat Admin / CLI
  → Security Agent

Slack/Discord #research
  → Research Agent

WebChat writing / notes
  → Writing Agent

Cron/Heartbeat limited
  → Maintenance Agent
```

Diagram:

```text
                 ┌────────────────────┐
Telegram DM  ───→│ Personal Agent      │
                 │ notes + memory      │
                 └────────────────────┘

                 ┌────────────────────┐
Discord coding ─→│ Coding Agent        │
CLI dev       ──→│ repo + sandbox exec │
                 └────────────────────┘

                 ┌────────────────────┐
WebChat admin ──→│ Security Agent      │
CLI admin     ──→│ read-only audit     │
                 └────────────────────┘

                 ┌────────────────────┐
Research room ──→│ Research Agent      │
                 │ web + reports       │
                 └────────────────────┘

                 ┌────────────────────┐
Cron/Heartbeat ─→│ Maintenance Agent   │
                 │ monitor + report    │
                 └────────────────────┘
```

Dokumentasi multi-agent OpenClaw menjelaskan bahwa Gateway dapat menjalankan satu agent atau banyak agent side-by-side; sebuah agent adalah scope penuh per persona, termasuk workspace files, auth profiles, model registry, dan session store, sementara binding memetakan akun channel ke agent tertentu. ([OpenClaw][4])

---

## Struktur Workspace Advanced

```text
~/.openclaw/
  openclaw.json

  workspace-personal/
    AGENTS.md
    SOUL.md
    TOOLS.md
    USER.md
    MEMORY.md
    memory/
    notes/
    skills/

  workspace-coding/
    AGENTS.md
    SOUL.md
    TOOLS.md
    MEMORY.md
    repos/
    decisions/
    skills/

  workspace-research/
    AGENTS.md
    SOUL.md
    TOOLS.md
    MEMORY.md
    reports/
    sources/
    skills/

  workspace-security/
    AGENTS.md
    SOUL.md
    TOOLS.md
    MEMORY.md
    audits/
    risk-register.md
    skills/

  workspace-writing/
    AGENTS.md
    SOUL.md
    TOOLS.md
    MEMORY.md
    books/
    outlines/
    drafts/

  workspace-maintenance/
    AGENTS.md
    SOUL.md
    TOOLS.md
    MEMORY.md
    maintenance-reports/
    incident-notes/
```

---

## Agent Roles Advanced

| Agent       | Tugas                     | Tools                             | Memory           | Risiko Utama          |
| ----------- | ------------------------- | --------------------------------- | ---------------- | --------------------- |
| Personal    | planning, notes, learning | notes, web, memory                | preferensi user  | privacy leak          |
| Coding      | repo, patch, test         | read/write/exec terbatas          | keputusan teknis | file damage           |
| Security    | audit/hardening           | read-only config/logs             | audit findings   | secret exposure       |
| Research    | riset/laporan             | web_search/fetch/browser isolated | source notes     | misinformation        |
| Writing     | buku/naskah               | read/write drafts                 | progress naskah  | keluar outline        |
| Maintenance | health check              | read logs/status                  | incident history | auto-repair berbahaya |

---

## Tool Separation Advanced

Main rule:

```text
Tidak ada agent yang boleh punya semua tools.
```

Personal Agent:

```text
Allow:
- read/write notes
- web_search
- memory update terbatas
- draft

Deny:
- exec
- config edit
- gateway control
- external send without confirmation
```

Coding Agent:

```text
Allow:
- read/write repo
- apply_patch
- exec test/lint/build
- git diff/status

Deny:
- personal memory
- email/calendar
- external message
```

Security Agent:

```text
Allow:
- read config/logs/workspace
- web_search official docs

Deny:
- write/edit/apply_patch default
- exec default
- external message
```

Research Agent:

```text
Allow:
- web_search
- web_fetch
- browser isolated
- write reports

Deny:
- exec
- private memory
- config access
```

Maintenance Agent:

```text
Allow:
- read logs/status/config
- write reports

Require confirmation:
- restart
- edit config
- clear session
- delete logs
- run command
```

---

## Channel Trust Advanced

Gunakan trust level.

```text
Level 0 — Public / unknown
- no memory
- no write
- no exec
- no private files

Level 1 — Group trusted
- group-relevant answers
- web only
- no personal memory

Level 2 — Owner private DM
- personal memory allowed
- notes allowed
- risky actions still require confirmation

Level 3 — Admin local
- diagnostics
- config review
- read-only audit
- risky repair requires confirmation
```

OpenClaw security docs menekankan pentingnya channel/session boundaries dan trust model Gateway, terutama saat remote access, DM policy, reverse proxy, atau public exposure diubah. ([OpenClaw][2])

---

## Multi-Agent Binding Advanced

Contoh pola ilustratif:

```json5
{
  agents: {
    list: [
      {
        id: "personal",
        default: true,
        workspace: "~/.openclaw/workspace-personal",
        skills: ["personal-planner", "memory-curator"]
      },
      {
        id: "coding",
        workspace: "~/.openclaw/workspace-coding",
        skills: ["coding-assistant"]
      },
      {
        id: "security",
        workspace: "~/.openclaw/workspace-security",
        skills: ["openclaw-deep-auditor"]
      },
      {
        id: "research",
        workspace: "~/.openclaw/workspace-research",
        skills: ["researcher"]
      }
    ]
  },

  bindings: [
    {
      agentId: "personal",
      match: { channel: "telegram", accountId: "personal" }
    },
    {
      agentId: "coding",
      match: { channel: "discord", accountId: "coding" }
    },
    {
      agentId: "security",
      match: { channel: "webchat", accountId: "admin" }
    },
    {
      agentId: "research",
      match: { channel: "slack", accountId: "research" }
    }
  ]
}
```

Karena config schema bisa berubah, gunakan contoh ini sebagai desain konseptual. Dokumentasi resmi menyarankan memakai configuration reference/schema lookup untuk field-level docs sebelum mengedit config aktual. ([GitHub][8])

---

## Sandbox Advanced

Pola sandbox:

```text
Personal Agent:
- no shell
- sandbox optional

Coding Agent:
- sandbox all
- workspaceAccess rw
- exec test/lint only

Security Agent:
- sandbox all
- workspaceAccess ro
- no write/exec

Research Agent:
- browser isolated
- no exec

Public Agent:
- no filesystem
- no memory private
```

OpenClaw sandboxing bisa menjalankan tools di sandbox backend untuk mengurangi blast radius; ini opsional dan dikontrol lewat config agent default atau per-agent. ([OpenClaw][6])

---

## Automation Advanced

Boleh pakai automation, tapi harus sangat jelas.

Cron:

```text
- weekly learning review,
- monthly security audit report,
- daily summary ke DM owner,
- scheduled reminder.
```

Heartbeat:

```text
- monitoring ringan,
- batch check berkala,
- no destructive action.
```

Hooks:

```text
- event-driven logging,
- session reset notes,
- tool call audit,
- no risky automatic repair.
```

OpenClaw automation docs membedakan cron untuk jadwal presisi, heartbeat untuk monitoring rutin dalam batch berkala, hooks untuk event tertentu, dan standing orders untuk konteks serta authority boundaries. Ini berarti automation harus dipilih sesuai kebutuhan, bukan semuanya dinyalakan. ([OpenClaw][5])

---

## Policy Automation Advanced

```markdown
# Automation Policy

Automation may:
- summarize,
- remind,
- report,
- monitor read-only,
- create draft outputs.

Automation must not:
- delete files,
- clear memory,
- edit config,
- send external messages to third parties,
- run risky shell commands,
- install/update dependencies,
- access secrets,
unless explicitly designed, reviewed, and confirmed.

Every automation must define:
- purpose,
- schedule,
- allowed tools,
- output destination,
- failure handling,
- stop instruction.
```

---

## Audit Advanced

Audit berkala:

```text
Harian:
- cek failed jobs/error ringan jika automation aktif.

Mingguan:
- cek memory open loops,
- cek logs/error,
- cek channel health.

Bulanan:
- audit tools,
- audit skills,
- audit memory,
- audit channel policy,
- audit automation,
- backup review.

Sebelum perubahan besar:
- backup config/workspace,
- audit current state,
- test di sandbox,
- rollout kecil.
```

---

## Checklist Level Advanced

```text
[ ] Multi-agent boundary jelas.
[ ] Workspace per-agent terpisah.
[ ] Memory per-agent terpisah.
[ ] Tools per-agent berbeda.
[ ] Skills per-agent dibatasi.
[ ] Channel bindings jelas.
[ ] Public/group channel low privilege.
[ ] Security agent read-only.
[ ] Coding agent sandboxed.
[ ] Research/browser isolated.
[ ] Automation scope kecil.
[ ] Cron/heartbeat/hook punya policy.
[ ] Logs dan audit reports tersedia.
[ ] Backup/git aktif.
[ ] Incident response runbook ada.
[ ] Token/secret tidak ada di prompt/memory/log.
```

---

# 18.6 Rekomendasi Naik Level

Jangan naik level berdasarkan rasa penasaran saja. Naik level berdasarkan kebutuhan.

## Dari Pemula ke Menengah

Naik kalau:

```text
- kamu sudah paham workspace dasar,
- main agent stabil,
- TOOLS.md sudah jelas,
- kamu punya workflow berulang,
- mulai butuh skill khusus,
- memory mulai perlu dirapikan.
```

Jangan naik kalau:

```text
- channel saja belum stabil,
- agent masih sering lupa karena memory belum rapi,
- belum ada backup,
- TOOLS.md belum ada confirmation gate.
```

---

## Dari Menengah ke Advanced

Naik kalau:

```text
- coding butuh exec/test command,
- research sering butuh browser/web,
- personal memory harus dipisah dari repo,
- kamu punya channel berbeda,
- automation mulai dibutuhkan,
- ada risiko yang perlu separation.
```

Jangan naik kalau:

```text
- kamu belum bisa menjelaskan tugas tiap agent,
- semua agent akan diberi tools sama,
- belum ada sandbox,
- belum ada audit/logging,
- belum ada incident response.
```

Kalimat tajamnya:

> **Multi-agent hanya layak kalau boundary-nya jelas. Kalau tidak, itu cuma single-agent berantakan yang memakai banyak nama.**

---

# 18.7 Rekomendasi Setup Terbaik untuk Kamu

Untuk kamu sekarang, aku sarankan jalur ini.

## Tahap 1 — Sekarang

```text
Satu Main Agent:
- personal learning,
- OpenClaw deep dive,
- prompt writing,
- notes.

Tools:
- read/write notes,
- web_search,
- memory terbatas.

Disabled:
- exec,
- browser login,
- external send otomatis,
- destructive tools.

Skills:
- openclaw-deep-auditor
- memory-curator
- researcher ringan
```

Kenapa? Karena fokusmu sekarang adalah memahami sistem dan membangun fondasi. Jangan biarkan tools kuat mengganggu fase belajar.

---

## Tahap 2 — Setelah Workspace Rapi

Tambahkan:

```text
Security/Audit Agent:
- read-only,
- openclaw-deep-auditor,
- no exec,
- no write default.

Coding Agent:
- hanya untuk repo/proyek coding,
- sandbox,
- exec test/lint terbatas,
- no personal memory.
```

Ini cocok karena kamu juga punya minat aplikasi Android dan agentic AI. Coding agent perlu dipisah agar personal agent tidak ikut punya shell.

---

## Tahap 3 — Setelah Workflow Stabil

Tambahkan:

```text
Research Agent:
- web_search/web_fetch,
- browser isolated jika perlu,
- report writing,
- no shell.

Writing Agent:
- buku, outline, draft panjang,
- progress memory,
- no risky tools.
```

Ini cocok karena kamu sering membuat outline dan buku panjang. Writing agent akan menjaga struktur agar tidak mengulang dari awal atau melompat bab.

---

## Tahap 4 — Setelah Ada Automation

Tambahkan:

```text
Maintenance Agent:
- cek logs/status,
- monthly audit,
- no auto-repair,
- write report only.

Automation:
- weekly learning review,
- monthly security report,
- no destructive actions.
```

---

# 18.8 Config Philosophy untuk Kamu

Aku sarankan filosofi konfigurasi seperti ini:

```text
Main Agent:
nyaman, pintar, tapi tidak berbahaya.

Coding Agent:
kuat di repo, tapi terkurung sandbox.

Security Agent:
tajam membaca risiko, tapi tidak mengubah apa pun tanpa izin.

Research Agent:
luas mencari sumber, tapi tidak punya shell.

Writing Agent:
kuat menjaga naskah, tapi tidak menyentuh config.

Maintenance Agent:
rajin memantau, tapi tidak auto-repair.
```

Dengan kata lain:

```text
Agent boleh pintar.
Tapi izin harus sempit.
```

---

# 18.9 Ringkasan Bagian 18

Rekomendasi konfigurasi OpenClaw harus bertingkat.

```text
Level Pemula:
- satu agent,
- channel terbatas,
- tools minimal,
- no exec,
- no group,
- memory sederhana,
- manual confirmation.

Level Menengah:
- skills khusus,
- workflow terdokumentasi,
- memory rapi,
- coding/security mulai dipisah,
- sandbox mulai dipakai,
- logging dan backup.

Level Advanced:
- multi-agent,
- routing per channel,
- workspace/memory/tools separation,
- sandbox per-agent,
- automation terbatas,
- audit berkala,
- incident response.
```

Fakta penting dari dokumentasi:

```text
- Config dipakai untuk channel access, tools, sandboxing, automation, sessions, networking, dan UI.
- Sandbox bersifat opsional dan mengurangi blast radius tool execution.
- Multi-agent memungkinkan beberapa agent side-by-side dengan workspace/state/session store sendiri.
- Automation terdiri dari cron, heartbeat, hooks, dan standing orders dengan fungsi berbeda.
```

Semua itu harus dikonfigurasi sesuai kebutuhan, bukan diaktifkan semua sekaligus. ([OpenClaw][1])

Opini teknisku: **setup terbaik untukmu bukan advanced sejak awal, tapi progressive hardening**. Mulai kecil, stabilkan memory dan tools, buat skill auditor, baru pecah ke multi-agent. Dengan begitu OpenClaw-mu tumbuh seperti sistem yang matang, bukan seperti eksperimen liar yang kebetulan jalan.

Bagian berikutnya kita akan membahas **Bagian 19 — Roadmap Belajar OpenClaw 8 Minggu**, dari mental model, workspace, bootstrap, skills/tools, memory/context, security, multi-agent, automation workflow, sampai build personal OpenClaw system.

Ke [Bagian 19: Roadmap Belajar OpenClaw 8 Minggu](19-roadmap-belajar.md)

[1]: https://docs.openclaw.ai/gateway/configuration?utm_source=chatgpt.com "Configuration - OpenClaw"
[2]: https://docs.openclaw.ai/gateway/security?utm_source=chatgpt.com "Security"
[3]: https://docs.openclaw.ai/gateway/configuration-examples?utm_source=chatgpt.com "Configuration examples"
[4]: https://docs.openclaw.ai/concepts/multi-agent?utm_source=chatgpt.com "Multi-agent routing"
[5]: https://docs.openclaw.ai/automation?utm_source=chatgpt.com "Automation - OpenClaw"
[6]: https://docs.openclaw.ai/gateway/sandboxing?utm_source=chatgpt.com "Sandboxing"
[7]: https://docs.openclaw.ai/automation/cron-jobs?utm_source=chatgpt.com "Scheduled tasks"
[8]: https://github.com/openclaw/openclaw/blob/main/docs/gateway/configuration.md?utm_source=chatgpt.com "openclaw/docs/gateway/configuration.md at main"
