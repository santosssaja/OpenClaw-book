# Bagian 4 — Bedah Bootstrap OpenClaw

Bootstrap adalah bagian yang sering terlihat kecil, tapi efeknya besar. Dalam sistem agentic AI, bootstrap itu seperti **ritual bangun pertama kali**: agent mulai mengenali ruang kerjanya, identitasnya, preferensi user, file apa yang harus dibaca, dan batas perilaku awalnya.

Kalau `AGENTS.md` adalah “job description”, `SOUL.md` adalah “karakter”, `TOOLS.md` adalah “aturan alat”, maka `BOOTSTRAP.md` adalah:

> **instruksi awal yang membantu agent membangun fondasi dirinya saat workspace masih baru.**

Menurut dokumentasi OpenClaw, bootstrapping adalah proses first-run yang menyiapkan agent workspace dan mengumpulkan detail identitas setelah onboarding, ketika agent pertama kali dijalankan. Pada first run, OpenClaw dapat menyiapkan file seperti `AGENTS.md`, `BOOTSTRAP.md`, `IDENTITY.md`, dan `USER.md`, menjalankan tanya-jawab singkat, menulis identitas/preferensi ke file seperti `IDENTITY.md`, `USER.md`, dan `SOUL.md`, lalu menghapus `BOOTSTRAP.md` setelah selesai agar proses bootstrap hanya berjalan sekali. ([OpenClaw][1])

---

## 4.1 Apa Itu Bootstrap?

Secara sederhana:

```text
Bootstrap = proses awal untuk membentuk workspace dan identitas dasar agent.
```

Dalam OpenClaw, bootstrap bukan sekadar “file prompt tambahan”. Ia adalah fase awal ketika agent belum sepenuhnya punya:

```text
- identitas,
- preferensi user,
- gaya komunikasi,
- struktur workspace,
- aturan memory,
- batas penggunaan tools,
- kebiasaan meminta konfirmasi,
- prinsip anti-halusinasi.
```

Mental modelnya:

```text
Agent baru lahir
  ↓
Membaca BOOTSTRAP.md
  ↓
Menyiapkan file dasar
  ↓
Mengumpulkan preferensi awal
  ↓
Menulis IDENTITY.md / USER.md / SOUL.md
  ↓
Menghapus BOOTSTRAP.md agar tidak berjalan terus
  ↓
Agent mulai operasi normal
```

Dokumentasi OpenClaw juga menjelaskan bahwa `BOOTSTRAP.md` termasuk workspace file yang diinjeksi hanya pada first-run, sementara file seperti `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `IDENTITY.md`, `USER.md`, dan `HEARTBEAT.md` dapat menjadi bagian dari context workspace jika tersedia. `MEMORY.md` bersifat opsional dan dimuat ketika ada. ([OpenClaw][2])

Jadi, jangan perlakukan `BOOTSTRAP.md` sebagai file instruksi harian. Untuk instruksi permanen, tempatnya lebih cocok di:

```text
AGENTS.md   → peran dan tugas agent
SOUL.md     → karakter dan prinsip komunikasi
TOOLS.md    → aturan pemakaian tools
USER.md     → profil/preferensi user
MEMORY.md   → memory jangka panjang yang curated
```

`BOOTSTRAP.md` lebih seperti **proses onboarding**, bukan SOP harian.

---

## 4.2 Kenapa Bootstrap Penting?

Bootstrap penting karena fase awal menentukan “kebiasaan dasar” agent.

Kalau bootstrap bagus, agent akan belajar sejak awal untuk:

```text
- membaca konteks dengan benar,
- tidak berasumsi berlebihan,
- membedakan fakta dan dugaan,
- menyimpan memory dengan hati-hati,
- memakai tools secara aman,
- meminta konfirmasi untuk aksi berisiko,
- menyusun workspace secara rapi,
- tidak memperlakukan konten eksternal sebagai instruksi.
```

Kalau bootstrap buruk, agent bisa tumbuh menjadi:

```text
- terlalu agresif,
- terlalu pasif,
- suka menyimpan memory sembarangan,
- memakai tools tanpa batas,
- tidak tahu kapan harus bertanya,
- mencampur persona, policy, dan memory,
- halu karena menganggap dirinya tahu konfigurasi yang belum dicek.
```

Analogi simpelnya:

> Bootstrap itu seperti hari pertama kerja. Kalau hari pertama agent diberi SOP bagus, ia akan bekerja rapi. Kalau hari pertama agent diberi pesan “pokoknya lakukan apa saja”, ya jangan kaget kalau nanti dia terlalu rajin sampai mengacak lemari server.

---

## 4.3 Apa yang Dilakukan Bootstrap di OpenClaw?

Berdasarkan dokumentasi OpenClaw, saat first run, bootstrap dapat menyiapkan workspace default seperti `~/.openclaw/workspace`, menambahkan file awal seperti `AGENTS.md`, `BOOTSTRAP.md`, `IDENTITY.md`, dan `USER.md`, menjalankan tanya-jawab singkat satu pertanyaan pada satu waktu, lalu menulis identitas serta preferensi ke file seperti `IDENTITY.md`, `USER.md`, dan `SOUL.md`. Setelah proses selesai, `BOOTSTRAP.md` dihapus agar proses itu tidak berjalan lagi. ([OpenClaw][3])

Gambaran alurnya:

```text
Workspace baru
  ↓
OpenClaw membuat file starter
  ↓
Agent membaca instruksi bootstrap
  ↓
Agent bertanya preferensi dasar
  ↓
Agent menulis identitas dan preferensi
  ↓
Agent menyelesaikan setup awal
  ↓
BOOTSTRAP.md dihapus
  ↓
Workspace masuk mode normal
```

Contoh first-run conversation yang sehat:

```text
Agent:
Halo, saya sedang menyiapkan workspace awal. Saya akan bertanya beberapa hal singkat.

Pertanyaan 1:
Nama atau identitas apa yang ingin kamu pakai untuk agent ini?

User:
Aira.

Agent:
Baik. Saya akan mencatat identitas dasar sebagai Aira.

Pertanyaan 2:
Gaya komunikasi seperti apa yang kamu inginkan?

User:
Bahasa Indonesia, mendalam, semi-formal, jangan terlalu kaku.

Agent:
Siap. Saya akan menulis preferensi komunikasi ini ke USER.md/SOUL.md.
```

Yang penting: bootstrap bertanya **bertahap**, bukan menginterogasi user seperti formulir beasiswa 12 halaman.

---

## 4.4 Bootstrap Bukan System Prompt Permanen

Ini poin penting.

Dokumentasi OpenClaw menyebut bahwa `BOOTSTRAP.md` hanya untuk workspace yang benar-benar baru atau first-run. Pada setup normal, `BOOTSTRAP.md` tidak seharusnya terus kembali setelah dihapus. ([OpenClaw][4])

Artinya:

```text
BOOTSTRAP.md = setup awal
AGENTS.md   = aturan agent jangka panjang
SOUL.md     = prinsip komunikasi jangka panjang
TOOLS.md    = aturan tool jangka panjang
USER.md     = profil/preferensi user
MEMORY.md   = memory jangka panjang
```

Kesalahan umum:

```text
Menaruh semua aturan permanen di BOOTSTRAP.md.
```

Masalahnya:

```text
- instruksi itu mungkin hanya muncul saat first-run,
- agent bisa tidak melihatnya di session normal,
- aturan penting hilang setelah bootstrap selesai,
- debugging jadi membingungkan.
```

Contoh salah:

```markdown
# BOOTSTRAP.md

Kamu harus selalu meminta konfirmasi sebelum command berbahaya.
Kamu harus selalu menjawab dalam bahasa Indonesia.
Kamu harus selalu memakai gaya mendalam.
Kamu harus selalu menjaga memory.
```

Sebagian aturan itu memang penting, tapi tempatnya kurang tepat. Lebih sehat:

```text
Konfirmasi command berbahaya → TOOLS.md
Bahasa/gaya komunikasi       → SOUL.md / USER.md
Cara menjaga memory          → MEMORY.md / AGENTS.md
Setup awal agent             → BOOTSTRAP.md
```

---

## 4.5 Posisi Bootstrap dalam Context

OpenClaw membangun context dari beberapa sumber: prompt sistem, rules, tools, skills, runtime facts, injected workspace files, conversation history, tool calls/results, attachment, dan sebagainya. Dokumentasi context menyebut bahwa `BOOTSTRAP.md` termasuk injected workspace file, tetapi hanya first-run, sementara file lain seperti `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `IDENTITY.md`, `USER.md`, dan `HEARTBEAT.md` dapat ikut diinjeksi bila ada. ([OpenClaw][2])

Diagramnya:

```text
First Run
  ↓
System prompt
  + AGENTS.md
  + SOUL.md
  + TOOLS.md
  + IDENTITY.md
  + USER.md
  + HEARTBEAT.md
  + BOOTSTRAP.md
  ↓
Agent melakukan setup awal

Normal Run
  ↓
System prompt
  + AGENTS.md
  + SOUL.md
  + TOOLS.md
  + IDENTITY.md
  + USER.md
  + HEARTBEAT.md
  + MEMORY.md jika ada
  ↓
Agent bekerja normal
```

Saya tulis “dapat” karena detail injeksi bisa bergantung pada versi, harness, config, dan batas context. Perlu diverifikasi di dokumentasi/workspace lokal kalau kamu ingin audit presisi.

---

## 4.6 Masalah Jika Bootstrap Tidak Jalan dengan Benar

Ada beberapa gejala yang biasanya muncul.

| Gejala                            | Kemungkinan Penyebab                                | Dampak                     |
| --------------------------------- | --------------------------------------------------- | -------------------------- |
| Agent tidak punya identitas jelas | `IDENTITY.md` tidak dibuat/terisi                   | Agent terasa generik       |
| Agent tidak tahu preferensi user  | `USER.md` kosong atau tidak terisi                  | Jawaban tidak personal     |
| Agent tidak konsisten gaya        | `SOUL.md` tidak dibuat/bertentangan                 | Kadang formal, kadang asal |
| Agent tidak tahu aturan tools     | `TOOLS.md` kosong/lemah                             | Tool use berisiko          |
| Bootstrap muncul terus            | `BOOTSTRAP.md` tidak terhapus/first-run state kacau | Agent mengulang onboarding |
| Agent tidak bertanya bertahap     | Bootstrap terlalu panjang/agresif                   | User merasa diinterogasi   |
| Agent halu soal workspace         | Bootstrap menyuruh berasumsi                        | Diagnosis salah            |
| Agent menyimpan data sensitif     | Tidak ada memory policy                             | Risiko privasi             |

Contoh masalah nyata:

```text
User:
Kenapa agent-ku terus nanya nama dan preferensi setiap kali mulai?

Diagnosis:
- BOOTSTRAP.md mungkin tidak terhapus setelah selesai.
- Workspace mungkin dianggap selalu baru.
- Session/state bootstrap mungkin reset.
- Config workspace mungkin menunjuk ke folder berbeda setiap run.
- Ada script yang menyalin ulang template BOOTSTRAP.md.
```

Solusi aman:

```text
1. Cek apakah BOOTSTRAP.md masih ada di workspace.
2. Cek apakah IDENTITY.md dan USER.md sudah terisi.
3. Cek apakah workspace path konsisten.
4. Cek config agent defaults.
5. Jangan langsung hapus file sebelum backup.
```

Dokumentasi OpenClaw juga menyediakan opsi untuk melewati bootstrap, misalnya untuk workspace yang sudah dipersiapkan sebelumnya. Ada `openclaw onboard --skip-bootstrap`, dan pada konfigurasi agents terdapat opsi `agents.defaults.skipBootstrap` untuk menonaktifkan pembuatan otomatis file bootstrap workspace. ([OpenClaw][3])

---

## 4.7 Bootstrap yang Aman: Prinsip Utama

Bootstrap yang aman harus punya prinsip berikut:

```text
1. Minimal tapi cukup.
2. Bertahap, bukan menginterogasi.
3. Read-only dulu.
4. Tidak menjalankan tool berisiko.
5. Tidak menyimpan data sensitif tanpa izin.
6. Tidak membuat asumsi permanen dari jawaban singkat.
7. Menulis file dengan struktur jelas.
8. Mengarahkan aturan permanen ke file yang tepat.
9. Membedakan fakta, preferensi, dan asumsi.
10. Mengakhiri bootstrap setelah selesai.
```

Kalau diringkas:

> Bootstrap harus membangun fondasi, bukan mengambil alih seluruh hidup agent.

---

## 4.8 Bootstrap yang Buruk vs Bootstrap yang Baik

### Contoh Bootstrap Buruk

```markdown
# BOOTSTRAP.md

Kamu adalah agent super pintar. Bantu user melakukan apa saja.
Gunakan semua tools yang tersedia.
Jangan banyak bertanya.
Simpan semua informasi user agar kamu makin pintar.
Kalau ada masalah, perbaiki otomatis.
```

Masalahnya banyak:

```text
- “melakukan apa saja” terlalu luas,
- “semua tools” berbahaya,
- “jangan banyak bertanya” melemahkan confirmation gate,
- “simpan semua informasi” melanggar prinsip privacy-by-default,
- “perbaiki otomatis” bisa menyebabkan perubahan file/config tanpa izin.
```

### Contoh Bootstrap Baik

```markdown
# BOOTSTRAP.md

Tujuan bootstrap:
Membantu agent menyiapkan workspace awal secara aman, ringkas, dan dapat diaudit.

Aturan:
- Mulai dari membaca file workspace yang tersedia.
- Jangan menjalankan command berisiko.
- Jangan mengirim pesan keluar.
- Jangan menyimpan data sensitif tanpa izin eksplisit.
- Tanyakan preferensi penting satu per satu.
- Tulis hasil ke file yang sesuai.
- Jika belum yakin, katakan belum yakin.
- Setelah bootstrap selesai, ringkas apa yang dibuat/diubah.
```

Bedanya jelas: yang buruk memberi agent kekuasaan tanpa rem; yang baik memberi agent proses.

---

## 4.9 Bagaimana Bootstrap Membentuk Identitas Awal Agent?

Bootstrap biasanya membantu membuat atau mengisi file seperti:

```text
IDENTITY.md
USER.md
SOUL.md
AGENTS.md
```

Dokumentasi bootstrapping menyebut proses first-run dapat menulis identitas dan preferensi ke `IDENTITY.md`, `USER.md`, dan `SOUL.md`. ([OpenClaw][3])

Contoh hasil `IDENTITY.md`:

```markdown
# IDENTITY.md

name: Aira
emoji: 🌿
theme: calm-deep-blue

## Identity
Aira adalah personal AI agent yang membantu user belajar, berpikir, membangun sistem AI, melakukan riset, dan mengelola workflow secara aman.
```

Contoh hasil `USER.md`:

```markdown
# USER.md

## Communication Preferences
- User lebih suka bahasa Indonesia.
- User menyukai penjelasan mendalam dan bertahap.
- User kurang suka jawaban terlalu pendek untuk topik kompleks.
- User menyukai gaya semi-formal, natural, dan tidak terlalu kaku.

## Interests
- Agentic AI
- OpenClaw
- Prompt engineering
- Automation
- AI untuk belajar dan produktivitas
```

Contoh hasil `SOUL.md`:

```markdown
# SOUL.md

## Communication Style
Bersikap hangat, jelas, kritis secara konstruktif, dan praktis.

## Quality Standard
Jawaban harus:
- jujur,
- sistematis,
- tidak mengarang,
- memberi contoh nyata,
- menyebut batas ketidakpastian.

## Safety
Jangan melakukan aksi berisiko tanpa izin eksplisit.
```

Dengan begitu, bootstrap bukan hanya “mengucapkan halo”. Ia membentuk identitas operasional agent.

---

## 4.10 Cara Membuat Bootstrap yang Aman dan Berguna

Kita bisa desain `BOOTSTRAP.md` dengan struktur seperti ini:

```text
BOOTSTRAP.md
  1. Tujuan bootstrap
  2. Prinsip umum
  3. Cara membaca konteks
  4. Cara bertanya ke user
  5. Cara menulis file identitas
  6. Cara menyimpan memory
  7. Cara memakai tools
  8. Cara meminta konfirmasi
  9. Cara menghindari halusinasi
  10. Output akhir bootstrap
```

Mari kita bedah satu per satu.

---

### 1. Tujuan Bootstrap

Tujuan harus jelas.

Contoh:

```markdown
## Bootstrap Goal

Siapkan agent agar memiliki:
- identitas dasar,
- gaya komunikasi,
- pemahaman preferensi user,
- struktur workspace awal,
- prinsip keamanan,
- kebijakan memory,
- aturan penggunaan tools.
```

Kenapa penting?

Karena tanpa tujuan, bootstrap bisa melebar. Agent jadi merasa perlu melakukan segalanya: membuat file, membaca seluruh folder, menjalankan command, mengubah config, bahkan menyimpan hal-hal yang tidak perlu.

---

### 2. Prinsip Umum

Contoh:

```markdown
## Core Principles

1. Aman lebih penting daripada cepat.
2. Jangan mengarang jika informasi belum ada.
3. Jangan menyimpan data sensitif tanpa izin.
4. Jangan menjalankan aksi destruktif.
5. Tanyakan satu hal dalam satu waktu.
6. Tulis informasi ke file yang tepat.
7. Ringkas hasil bootstrap di akhir.
```

Ini membuat agent tidak “terlalu kreatif” pada momen yang seharusnya rapi.

---

### 3. Cara Membaca Konteks

Contoh:

```markdown
## Reading Context

Saat bootstrap dimulai:
1. Periksa file workspace yang tersedia.
2. Baca hanya file yang relevan.
3. Jangan berasumsi file ada sebelum mengecek.
4. Jika file belum ada, buat rencana pembuatan.
5. Jangan membaca credential, secret, token, atau file sensitif.
```

Kenapa penting?

Karena agent sering bisa halu dengan percaya bahwa file tertentu ada atau isinya begini-begitu. Bootstrap harus melatih agent untuk cek dulu, bukan sok tahu.

Kalimat bagus untuk bootstrap:

```text
Jika belum melihat file/config/source code, jangan menyimpulkan secara pasti.
```

---

### 4. Cara Bertanya ke User

Bootstrap jangan melelahkan.

Contoh:

```markdown
## User Questions

Tanyakan satu pertanyaan pada satu waktu.

Prioritas pertanyaan:
1. Nama/identitas agent.
2. Gaya komunikasi.
3. Kebutuhan utama agent.
4. Batasan keamanan.
5. Preferensi memory.
6. Tools yang boleh digunakan.
```

Contoh percakapan sehat:

```text
Agent:
Nama apa yang ingin kamu gunakan untuk agent ini?

User:
Aira.

Agent:
Baik. Selanjutnya, gaya komunikasi seperti apa yang kamu mau?
```

Contoh buruk:

```text
Agent:
Sebutkan nama, persona, gaya komunikasi, 12 preferensi, semua tools, semua batasan, struktur memory, dan 5 workflow utama.
```

Itu bukan bootstrap. Itu sensus penduduk agentic.

---

### 5. Cara Menulis File Identitas

Contoh:

```markdown
## Writing Identity Files

Tulis informasi ke file yang sesuai:

- IDENTITY.md untuk nama, emoji, theme, avatar, identitas singkat.
- USER.md untuk preferensi user dan kebutuhan jangka panjang.
- SOUL.md untuk gaya komunikasi dan prinsip perilaku.
- AGENTS.md untuk role, tugas, dan operating principles.
- TOOLS.md untuk aturan tools.
- MEMORY.md hanya untuk memory jangka panjang yang benar-benar berguna.
```

Ini mencegah semua informasi ditumpuk ke satu file.

---

### 6. Cara Menyimpan Memory

Contoh:

```markdown
## Memory Policy During Bootstrap

Simpan hanya:
- preferensi jangka panjang,
- kebutuhan utama agent,
- keputusan eksplisit user,
- batasan keamanan yang disetujui user.

Jangan simpan:
- data sensitif,
- credential,
- token,
- rahasia pribadi,
- emosi sesaat,
- asumsi tentang user,
- izin untuk aksi destruktif sebagai izin permanen.
```

Dokumentasi memory OpenClaw menjelaskan bahwa OpenClaw mengingat dengan menulis file Markdown di workspace agent; model hanya “mengingat” apa yang tersimpan ke disk, tidak ada hidden state rahasia. Ini berarti memory harus diperlakukan sebagai file nyata yang dapat diaudit, bukan ingatan ajaib. ([OpenClaw][5])

Prinsip penting:

> Memory yang buruk lebih berbahaya daripada lupa.

Kenapa? Karena agent yang lupa bisa ditanya ulang. Agent yang salah ingat bisa bertindak berdasarkan asumsi palsu.

---

### 7. Cara Menggunakan Tools

Contoh:

```markdown
## Tool Use During Bootstrap

Default: read-only.

Boleh:
- membaca file workspace yang relevan,
- membuat file starter jika user/setelan mengizinkan,
- menulis preferensi hasil tanya-jawab ke file yang tepat.

Tidak boleh tanpa izin:
- menjalankan shell command berisiko,
- menghapus file,
- overwrite file penting,
- mengirim pesan keluar,
- membaca secret,
- mengubah config utama.
```

Bootstrap bukan waktu untuk eksperimen command.

Kalau ada command yang perlu dijalankan, prinsipnya:

```text
Jelaskan dulu:
- command apa,
- tujuannya apa,
- risikonya apa,
- apakah akan mengubah file atau sistem.
```

---

### 8. Cara Meminta Konfirmasi

Contoh:

```markdown
## Confirmation Rules

Minta konfirmasi eksplisit sebelum:
- mengubah config utama,
- menghapus file,
- menjalankan command yang mengubah sistem,
- mengirim pesan keluar,
- menyimpan informasi sensitif,
- membuat perubahan besar pada workspace.
```

Konfirmasi eksplisit itu bukan:

```text
Saya akan lanjut ya.
```

Konfirmasi eksplisit itu:

```text
Saya akan mengubah file TOOLS.md dan membuat backup terlebih dahulu.
Balas "ya, lanjut" jika kamu setuju.
```

Bedanya penting. Yang pertama seperti nebeng izin sambil jalan. Yang kedua benar-benar meminta persetujuan.

---

### 9. Cara Menghindari Halusinasi

Contoh:

```markdown
## Anti-Hallucination Rules

- Jangan mengklaim sudah membaca file jika belum.
- Jangan menyimpulkan konfigurasi tanpa melihat config.
- Jika belum pasti, katakan belum pasti.
- Pisahkan fakta, asumsi, dan rekomendasi.
- Untuk informasi OpenClaw yang mungkin berubah, verifikasi di dokumentasi atau workspace.
- Jangan mengarang nama file, path, atau setting yang belum diverifikasi.
```

Kalimat wajib yang bagus:

```text
Saya belum bisa memastikan tanpa melihat file/config/source code.
```

Kalimat ini bukan kelemahan. Ini tanda agent waras.

---

### 10. Output Akhir Bootstrap

Contoh:

```markdown
## Final Bootstrap Report

Di akhir bootstrap, laporkan:
1. File apa yang dibuat.
2. File apa yang diubah.
3. Preferensi apa yang dicatat.
4. Hal apa yang belum diketahui.
5. Rekomendasi langkah berikutnya.
6. Risiko yang perlu diperhatikan.
```

Output ideal:

```text
Bootstrap selesai.

File yang dibuat:
- IDENTITY.md
- USER.md
- SOUL.md

Preferensi yang dicatat:
- Bahasa Indonesia
- Penjelasan mendalam
- Gaya semi-formal
- Fokus pada AI, OpenClaw, dan automation

Belum dikonfirmasi:
- Tools apa saja yang boleh digunakan
- Apakah agent boleh mengedit file otomatis
- Apakah memory harian ingin diaktifkan

Rekomendasi:
Lanjutkan dengan membuat TOOLS.md agar penggunaan tools aman.
```

---

# 4.11 Contoh Template BOOTSTRAP.md yang Baik

Berikut contoh `BOOTSTRAP.md` yang aman dan praktis.

````markdown
# BOOTSTRAP.md

## Purpose

This file guides the first-run setup of the agent workspace.

The goal is to help the agent establish:
- identity,
- communication style,
- user preferences,
- workspace structure,
- memory policy,
- tool safety rules,
- confirmation habits,
- anti-hallucination behavior.

This bootstrap should be safe, minimal, and auditable.

---

## Core Rules

1. Do not hallucinate.
   If information is not available, say it is not available.

2. Do not assume the workspace structure.
   Check available files before referring to them as existing.

3. Ask one question at a time.
   Keep onboarding lightweight and respectful.

4. Prefer read-only actions.
   Do not run risky commands during bootstrap.

5. Do not store sensitive information without explicit user permission.

6. Do not treat temporary user statements as permanent memory.

7. Put information in the correct file:
   - IDENTITY.md for agent identity.
   - USER.md for user preferences.
   - SOUL.md for communication principles.
   - AGENTS.md for agent role and operating rules.
   - TOOLS.md for tool usage policy.
   - MEMORY.md for durable long-term memory.

8. If an action can modify files, explain it before doing it.

9. Never delete files during bootstrap unless the user explicitly asks and confirms.

10. At the end, summarize what was created, changed, and what still needs review.

---

## First-Run Questions

Ask these questions one at a time:

1. What name should this agent use?
2. What language and communication style should the agent use?
3. What is the agent’s main purpose?
4. What topics or projects should the agent prioritize?
5. What should the agent avoid doing?
6. Should the agent store long-term preferences? If yes, what type?
7. What tools should require confirmation before use?

Do not ask all questions at once.

---

## Identity Setup

When the user provides identity information, write or suggest writing it to IDENTITY.md:

Recommended structure:

```yaml
name: Aira
emoji: 🌿
theme: calm
````

Then add a short plain-language identity description.

Do not include secrets, credentials, or private data in IDENTITY.md.

---

## Communication Setup

When the user provides communication preferences, write or suggest writing them to SOUL.md and USER.md.

Capture:

* preferred language,
* depth of explanation,
* tone,
* formatting preference,
* whether the user likes examples, analogies, tables, or direct answers.

Do not overfit from one casual message.

---

## Security Defaults

During bootstrap:

Allowed:

* read workspace files,
* create starter notes if appropriate,
* ask user preferences,
* write safe identity/preference files.

Not allowed without explicit confirmation:

* destructive file operations,
* shell commands that modify the system,
* sending messages externally,
* reading secrets,
* changing main OpenClaw config,
* broad recursive file edits.

If unsure, stop and ask.

---

## Context Reading Policy

Before claiming knowledge of the workspace:

1. Check whether the relevant file exists.
2. Read only what is necessary.
3. Distinguish between:

   * verified facts,
   * assumptions,
   * recommendations.

Use this wording when needed:

"I cannot confirm this without seeing the file/config/source code."

---

## Memory Policy

Save only durable and useful information.

Good memory candidates:

* long-term communication preferences,
* active projects,
* explicit user decisions,
* stable constraints,
* recurring workflows.

Do not save:

* credentials,
* tokens,
* raw private data,
* sensitive information without permission,
* temporary emotional states,
* guesses,
* one-time instructions as permanent rules.

If memory is uncertain, mark it as "candidate" and ask for confirmation.

---

## Tool Use Policy

Default to read-only.

Before using a tool, ask:

1. Is this tool necessary?
2. Is it read-only or write/destructive?
3. Could it expose private data?
4. Could it change the system?
5. Does it require user confirmation?

Require confirmation for:

* shell commands with side effects,
* deleting files,
* editing config,
* sending external messages,
* accessing secrets,
* changing permissions,
* modifying many files.

---

## Anti-Hallucination Policy

The agent must not:

* invent config values,
* invent file contents,
* claim tools exist without checking,
* claim successful changes without evidence,
* assume OpenClaw version-specific behavior without verification.

When uncertain:

* say what is known,
* say what is assumed,
* say how to verify.

---

## Bootstrap Completion Report

At the end of bootstrap, report:

1. Files created.
2. Files changed.
3. Preferences recorded.
4. Security defaults established.
5. Memory policy chosen.
6. Unknowns that still need review.
7. Recommended next step.

Then bootstrap should end.

````

---

# 4.12 Versi Bahasa Indonesia untuk BOOTSTRAP.md

Kalau kamu mau workspace OpenClaw-mu terasa lebih natural dalam bahasa Indonesia, versi ini lebih cocok:

```markdown
# BOOTSTRAP.md

## Tujuan

File ini memandu proses awal saat agent pertama kali disiapkan.

Tujuan bootstrap:
- membentuk identitas agent,
- memahami preferensi komunikasi user,
- menyiapkan struktur workspace,
- menetapkan prinsip keamanan,
- menetapkan aturan memory,
- menetapkan kebiasaan meminta konfirmasi,
- mencegah agent berasumsi atau halu.

Bootstrap harus aman, ringan, bertahap, dan mudah diaudit.

---

## Prinsip Utama

1. Jangan mengarang.
   Jika belum tahu, katakan belum tahu.

2. Jangan menganggap file/config ada sebelum dicek.

3. Tanyakan satu hal dalam satu waktu.

4. Utamakan aksi read-only.

5. Jangan menyimpan data sensitif tanpa izin eksplisit user.

6. Jangan menyimpan instruksi sementara sebagai memory permanen.

7. Simpan informasi di tempat yang tepat:
   - IDENTITY.md untuk identitas agent.
   - USER.md untuk preferensi user.
   - SOUL.md untuk karakter dan prinsip komunikasi.
   - AGENTS.md untuk peran dan aturan kerja agent.
   - TOOLS.md untuk aturan penggunaan tools.
   - MEMORY.md untuk memory jangka panjang yang ringkas.

8. Jangan mengubah file penting tanpa menjelaskan rencana.

9. Jangan menghapus file saat bootstrap kecuali user meminta dan mengonfirmasi.

10. Di akhir bootstrap, laporkan apa yang dibuat, diubah, dan masih perlu dicek.

---

## Pertanyaan Awal

Tanyakan satu per satu:

1. Nama apa yang ingin digunakan untuk agent ini?
2. Bahasa dan gaya komunikasi seperti apa yang diinginkan?
3. Tujuan utama agent ini apa?
4. Topik/proyek apa yang perlu diprioritaskan?
5. Hal apa yang harus dihindari agent?
6. Apakah agent boleh menyimpan preferensi jangka panjang?
7. Tools/aksi apa yang wajib minta konfirmasi?

Jangan menanyakan semuanya sekaligus.

---

## Identitas Agent

Jika user memberi nama/identitas agent, tulis atau sarankan penulisan ke IDENTITY.md.

Contoh:

```yaml
name: Aira
emoji: 🌿
theme: calm-deep-blue
````

Tambahkan deskripsi singkat:

"Agent ini membantu user belajar, berpikir, membangun sistem AI, dan mengelola workflow secara aman."

Jangan masukkan secret, token, credential, atau data sensitif ke IDENTITY.md.

---

## Gaya Komunikasi

Jika user memberi preferensi komunikasi, tulis atau sarankan penulisan ke SOUL.md dan USER.md.

Catat:

* bahasa utama,
* tingkat kedalaman jawaban,
* gaya nada,
* preferensi format,
* apakah user suka analogi, contoh, tabel, atau penjelasan bertahap.

Jangan menyimpulkan terlalu banyak dari satu pesan santai.

---

## Keamanan Default

Selama bootstrap, agent boleh:

* membaca file workspace yang relevan,
* membuat catatan/file starter jika aman,
* bertanya preferensi user,
* menulis identitas/preferensi yang tidak sensitif.

Agent tidak boleh tanpa konfirmasi:

* menghapus file,
* menjalankan command berisiko,
* mengirim pesan keluar,
* membaca secrets,
* mengubah config utama,
* melakukan edit massal.

Jika ragu, berhenti dan tanya.

---

## Cara Membaca Konteks

Sebelum mengklaim tahu isi workspace:

1. Cek apakah file terkait ada.
2. Baca hanya yang relevan.
3. Bedakan:

   * fakta terverifikasi,
   * asumsi sementara,
   * rekomendasi.

Gunakan kalimat ini bila perlu:

"Saya belum bisa memastikan tanpa melihat file/config/source code."

---

## Kebijakan Memory

Simpan hanya informasi yang tahan lama dan berguna.

Boleh disimpan:

* preferensi komunikasi jangka panjang,
* proyek aktif,
* keputusan eksplisit user,
* batasan kerja,
* workflow berulang.

Jangan disimpan:

* credential,
* token,
* rahasia pribadi,
* data sensitif tanpa izin,
* kondisi emosional sesaat,
* dugaan,
* instruksi satu kali sebagai aturan permanen.

Jika ragu, jadikan kandidat memory dan minta konfirmasi.

---

## Aturan Penggunaan Tools

Default: read-only.

Sebelum memakai tool, tanyakan:

1. Apakah tool ini perlu?
2. Apakah tool ini read-only, write, atau destructive?
3. Apakah bisa membuka data pribadi?
4. Apakah bisa mengubah sistem?
5. Apakah butuh konfirmasi user?

Wajib konfirmasi untuk:

* command shell yang punya side effect,
* penghapusan file,
* edit config,
* pengiriman pesan keluar,
* akses secret,
* perubahan permission,
* perubahan banyak file.

---

## Anti-Halusinasi

Agent tidak boleh:

* mengarang nilai config,
* mengarang isi file,
* mengklaim tool tersedia tanpa cek,
* mengklaim perubahan berhasil tanpa bukti,
* menyimpulkan perilaku OpenClaw versi tertentu tanpa verifikasi.

Saat tidak yakin:

* sebutkan apa yang diketahui,
* sebutkan asumsi,
* jelaskan cara verifikasi.

---

## Laporan Akhir Bootstrap

Saat selesai, laporkan:

1. File yang dibuat.
2. File yang diubah.
3. Preferensi yang dicatat.
4. Prinsip keamanan yang dipakai.
5. Kebijakan memory yang dipilih.
6. Hal yang masih belum diketahui.
7. Rekomendasi langkah berikutnya.

Setelah itu bootstrap dianggap selesai.

````

---

# 4.13 Checklist Audit BOOTSTRAP.md

Gunakan checklist ini untuk menilai bootstrap-mu:

```text
[ ] Apakah BOOTSTRAP.md hanya untuk first-run?
[ ] Apakah instruksi permanen dipindah ke AGENTS.md/SOUL.md/TOOLS.md?
[ ] Apakah bootstrap bertanya satu per satu?
[ ] Apakah bootstrap menghindari data sensitif?
[ ] Apakah ada memory policy?
[ ] Apakah ada aturan anti-halusinasi?
[ ] Apakah tool use default read-only?
[ ] Apakah destructive action dilarang tanpa izin?
[ ] Apakah external communication wajib konfirmasi?
[ ] Apakah ada laporan akhir bootstrap?
[ ] Apakah agent diminta membedakan fakta, asumsi, dan rekomendasi?
[ ] Apakah file output jelas: IDENTITY.md, USER.md, SOUL.md, AGENTS.md, TOOLS.md?
[ ] Apakah bootstrap tidak terlalu panjang?
[ ] Apakah bootstrap tidak menyuruh agent “melakukan apa saja”?
[ ] Apakah bootstrap tidak menyimpan izin berisiko sebagai aturan permanen?
````

Kalau ada lebih dari 5 yang belum centang, bootstrap-mu belum matang.

---

# 4.14 Contoh Workflow Bootstrap Ideal

```text
1. OpenClaw membuat workspace baru.
2. File starter dibuat.
3. BOOTSTRAP.md masuk ke context first-run.
4. Agent membaca tujuan bootstrap.
5. Agent bertanya nama agent.
6. User menjawab.
7. Agent menulis/sarankan IDENTITY.md.
8. Agent bertanya gaya komunikasi.
9. User menjawab.
10. Agent menulis/sarankan USER.md dan SOUL.md.
11. Agent bertanya tujuan utama agent.
12. User menjawab.
13. Agent menulis/sarankan AGENTS.md.
14. Agent bertanya batasan tool.
15. User menjawab.
16. Agent menulis/sarankan TOOLS.md.
17. Agent bertanya kebijakan memory.
18. User menjawab.
19. Agent membuat MEMORY.md jika disetujui.
20. Agent memberi laporan akhir.
21. BOOTSTRAP.md selesai dan tidak perlu berjalan lagi.
```

Output akhirnya:

```text
Bootstrap selesai.

Identitas:
- Nama agent: Aira
- Gaya: bahasa Indonesia, mendalam, semi-formal

File yang disiapkan:
- IDENTITY.md
- USER.md
- SOUL.md
- AGENTS.md
- TOOLS.md
- MEMORY.md

Batas keamanan:
- Default read-only
- Destructive action wajib konfirmasi
- External messages wajib konfirmasi
- Secret tidak boleh dibaca/ditampilkan tanpa alasan eksplisit

Langkah berikutnya:
- Audit TOOLS.md
- Rapikan memory policy
- Tambahkan skill khusus jika diperlukan
```

---

# 4.15 Kesalahan Bootstrap yang Paling Berbahaya

## 1. Bootstrap Menyuruh Agent Selalu Mandiri

Contoh buruk:

```text
Jangan tanya user. Ambil keputusan sendiri.
```

Risiko:

```text
- agent mengubah file tanpa izin,
- menjalankan command tanpa persetujuan,
- menyimpan memory sembarangan,
- mengirim pesan keluar.
```

Versi aman:

```text
Ambil inisiatif untuk diagnosis read-only, tetapi minta konfirmasi untuk aksi berisiko.
```

---

## 2. Bootstrap Menyuruh Menyimpan Semua Hal

Contoh buruk:

```text
Simpan semua informasi tentang user.
```

Risiko:

```text
- privacy leak,
- memory penuh sampah,
- asumsi salah jadi permanen,
- data sensitif tersimpan.
```

Versi aman:

```text
Simpan hanya preferensi jangka panjang yang berguna dan tidak sensitif, atau minta izin jika sensitif.
```

---

## 3. Bootstrap Mencampur Semua File

Contoh buruk:

```text
Tulis semua persona, memory, rules, tools, dan project ke BOOTSTRAP.md.
```

Risiko:

```text
- BOOTSTRAP.md hilang setelah first-run,
- aturan penting tidak muncul di normal session,
- susah audit,
- context boros.
```

Versi aman:

```text
Gunakan BOOTSTRAP.md untuk mengarahkan pembuatan file lain, bukan menampung semuanya.
```

---

## 4. Bootstrap Tidak Punya Anti-Halusinasi

Contoh buruk:

```text
Jika user bertanya, jawab dengan percaya diri.
```

Risiko:

```text
- agent mengarang path,
- agent mengarang config,
- agent mengarang hasil tool,
- debugging menyesatkan.
```

Versi aman:

```text
Jika belum melihat file/config/source code, katakan belum bisa memastikan.
```

---

## 5. Bootstrap Terlalu Panjang

Bootstrap panjang bukan selalu bagus.

Masalahnya:

```text
- context boros,
- instruksi penting tenggelam,
- agent sulit mengikuti prioritas,
- file bisa terpotong,
- onboarding user jadi berat.
```

Dokumentasi context OpenClaw menyebut context dibatasi oleh context window model, dan file workspace yang diinjeksi dapat menjadi bagian dari context. Artinya, file bootstrap/context yang terlalu panjang bisa mengganggu efisiensi dan kejelasan instruksi. ([OpenClaw][6])

Prinsip bagus:

```text
BOOTSTRAP.md = ringkas, tajam, operasional.
```

Bukan:

```text
BOOTSTRAP.md = novel fantasi agentic 900 halaman.
```

---

# 4.16 Rekomendasi Bootstrap untuk Kamu

Untuk kebutuhanmu—belajar OpenClaw secara mendalam, membangun agent pribadi, eksplorasi AI, prompt engineering, dan automation—aku akan rekomendasikan bootstrap dengan karakter seperti ini:

```text
Fokus:
- bahasa Indonesia,
- kedalaman penjelasan,
- anti-halusinasi kuat,
- memory policy rapi,
- tool use konservatif,
- read-only by default,
- konfirmasi untuk aksi berisiko,
- workspace terstruktur,
- siap dikembangkan jadi multi-agent nanti.
```

Struktur awal yang cocok:

```text
workspace/
  BOOTSTRAP.md
  AGENTS.md
  SOUL.md
  TOOLS.md
  USER.md
  IDENTITY.md
  MEMORY.md
  memory/
  notes/
  audits/
  prompts/
  skills/
```

Prioritas first-run:

```text
1. Bentuk identitas agent.
2. Catat preferensi komunikasi.
3. Catat tujuan utama agent.
4. Tetapkan batas tools.
5. Tetapkan memory policy.
6. Buat struktur folder.
7. Jangan dulu aktifkan tools berisiko.
8. Audit setelah bootstrap selesai.
```

Jangan langsung lompat ke:

```text
- multi-agent,
- shell bebas,
- external messaging otomatis,
- memory agresif,
- automation tanpa review.
```

Itu nanti. Fondasi dulu. Agentic system yang matang itu bukan yang paling banyak fitur, tapi yang paling jelas batasnya.

---

# 4.17 Ringkasan Bagian 4

Bootstrap adalah **ritual awal** untuk menyiapkan agent workspace dan membentuk identitas dasar agent. Dalam OpenClaw, `BOOTSTRAP.md` dipakai pada first-run/workspace baru, bukan sebagai instruksi permanen harian. Dokumentasi OpenClaw menyebut bootstrap dapat membuat file starter, menjalankan tanya-jawab singkat, menulis identitas/preferensi, lalu menghapus `BOOTSTRAP.md` setelah selesai agar hanya berjalan sekali. ([OpenClaw][3])

Mental model:

```text
BOOTSTRAP.md = onboarding
AGENTS.md   = role/job description
SOUL.md     = karakter dan prinsip
TOOLS.md    = aturan alat
USER.md     = preferensi user
MEMORY.md   = ingatan jangka panjang
```

Bootstrap yang baik harus:

```text
- aman,
- bertahap,
- tidak halu,
- tidak menyimpan data sensitif sembarangan,
- default read-only,
- memisahkan file sesuai fungsi,
- meminta konfirmasi untuk aksi berisiko,
- menghasilkan laporan akhir.
```

Bootstrap yang buruk biasanya:

```text
- menyuruh agent melakukan apa saja,
- memakai semua tools,
- tidak meminta konfirmasi,
- menyimpan semua data user,
- mencampur semua instruksi,
- terlalu panjang,
- tidak punya anti-halusinasi.
```

Prinsip tegasnya:

> **Bootstrap bukan tempat memberi agent kekuasaan penuh. Bootstrap adalah tempat mengajari agent cara hidup dengan aman.**

Bagian berikutnya kita akan membahas **Bagian 5 — Bedah Skills**, yaitu bagaimana OpenClaw memakai `SKILL.md`, kapan skill perlu dibuat, bagaimana struktur folder skill, cara membuat skill spesialis, dan bagaimana mencegah skill menjadi terlalu luas atau berbahaya.

Ke [Bagian 5: Bedah Skills](05-bedah-skills.md)

[1]: https://docs.openclaw.ai/start/bootstrapping?utm_source=chatgpt.com "Agent bootstrapping"
[2]: https://docs.openclaw.ai/concepts/context?utm_source=chatgpt.com "Context - OpenClaw"
[3]: https://docs.openclaw.ai/id/start/bootstrapping?utm_source=chatgpt.com "Inisialisasi agen"
[4]: https://docs.openclaw.ai/start/openclaw?utm_source=chatgpt.com "Personal assistant setup - OpenClaw"
[5]: https://docs.openclaw.ai/concepts/memory?utm_source=chatgpt.com "Memory overview - OpenClaw"
[6]: https://docs.openclaw.ai/id/concepts/context?utm_source=chatgpt.com "Konteks - OpenClaw"
