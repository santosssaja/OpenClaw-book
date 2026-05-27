# Bagian 11 — Bedah Workflow Nyata OpenClaw

Sekarang kita masuk bagian yang paling praktis: **bagaimana OpenClaw dipakai dalam workflow nyata**.

Kalau bagian sebelumnya membahas komponen, sekarang kita susun menjadi alur kerja operasional.

Mental modelnya:

```text
Komponen terpisah:
Gateway, channel, session, workspace, memory, tools, skills, agent runtime

Menjadi workflow:
User memberi tugas
  ↓
OpenClaw memilih session/agent
  ↓
Agent membaca konteks
  ↓
Agent memilih skill
  ↓
Agent memakai tools
  ↓
Agent menyimpan hasil bila perlu
  ↓
Agent memberi respons/action
```

OpenClaw sendiri menjelaskan bahwa Gateway adalah proses yang memiliki messaging surfaces seperti Telegram, WhatsApp, Slack, Discord, Signal, iMessage, dan WebChat; control-plane client seperti CLI/web UI terhubung ke Gateway lewat WebSocket. Tools adalah callable actions, skills mengajari agent cara bekerja, dan memory disimpan sebagai file Markdown di workspace agent. Jadi workflow nyata harus merangkai **channel → Gateway → agent → context/memory → skill/tool → output** dengan aman. ([OpenClaw][1])

---

# 11.1 Prinsip Umum Workflow OpenClaw

Sebelum masuk contoh, pegang dulu prinsip desain workflow:

```text
1. Mulai dari tujuan user.
2. Tentukan agent yang tepat.
3. Tentukan channel yang aman.
4. Tentukan konteks yang dibutuhkan.
5. Tentukan tools yang boleh dipakai.
6. Tentukan memory apa yang boleh dibaca/ditulis.
7. Tentukan batas konfirmasi.
8. Tentukan output akhir.
9. Tentukan logging/audit.
10. Tentukan recovery kalau salah.
```

Workflow yang bagus tidak cuma menjawab:

```text
"Agent melakukan apa?"
```

Tapi juga:

```text
"Apa yang tidak boleh dilakukan agent?"
"Kalau ragu, agent harus berhenti di mana?"
"Kalau tool gagal, agent lapor apa?"
"Kalau ada data sensitif, agent harus bagaimana?"
```

Ini yang membedakan workflow agentic yang matang dari “prompt panjang yang berharap semuanya lancar”.

---

# 11.2 Template Dasar Workflow OpenClaw

Setiap workflow bisa ditulis dengan template ini:

```markdown
# Workflow Name

## Goal
Tujuan workflow.

## Trigger
Kapan workflow dijalankan.

## Channel
Dari channel mana workflow boleh dipicu.

## Agent
Agent yang menangani.

## Required Context
Informasi/file/memory yang dibutuhkan.

## Allowed Tools
Tools yang boleh dipakai.

## Restricted Tools
Tools yang tidak boleh dipakai atau butuh konfirmasi.

## Steps
1. Langkah pertama.
2. Langkah kedua.
3. Langkah ketiga.

## Memory Policy
Apa yang boleh disimpan, apa yang tidak.

## Safety Rules
Batas keamanan.

## Output
Format hasil akhir.

## Failure Handling
Apa yang dilakukan kalau gagal.
```

Kalau kamu bikin workflow tanpa bagian “Restricted Tools” dan “Failure Handling”, biasanya workflow itu belum matang.

---

# 11.3 Workflow 1 — AI Personal Assistant

## Tujuan

AI Personal Assistant membantu user dalam urusan pribadi sehari-hari:

```text
- menjawab pertanyaan,
- membuat catatan,
- menyusun rencana,
- mengingat preferensi,
- membantu jadwal,
- membuat draft pesan,
- membantu belajar,
- merapikan ide,
- membuat reminder/automation terbatas.
```

Ini cocok untuk agent utama seperti:

```text
Aira Personal
```

Namun agent personal punya risiko privasi tinggi karena ia dekat dengan memory user, catatan pribadi, dan mungkin channel DM.

---

## Arsitektur Workflow

```text
User
  ↓
Telegram DM / WebChat lokal
  ↓
Gateway
  ↓
Personal Assistant Agent
  ↓
USER.md + MEMORY.md + notes/
  ↓
Skills:
- personal-planner
- memory-curator
- learning-coach
  ↓
Tools:
- read/write notes
- web_search
- calendar/reminder bila tersedia
- message draft
  ↓
Response / note / reminder
```

---

## Trigger

Contoh trigger:

```text
"Aira, bantu aku susun rencana belajar minggu ini."
"Aira, catat ide aplikasi catatan Android-ku."
"Aira, ingatkan aku besok untuk review OpenClaw."
"Aira, bantu bikin draft pesan ke temanku."
```

---

## Channel yang Cocok

```text
Cocok:
- Telegram DM owner
- WebChat lokal
- CLI lokal untuk admin

Tidak cocok:
- group publik
- channel komunitas
- open DM
```

Kenapa? Karena personal assistant bisa memakai memory pribadi. Memory pribadi tidak boleh bocor ke group/public channel.

---

## Allowed Tools

```text
Boleh:
- read/write notes,
- update memory terbatas,
- web_search untuk info umum,
- calendar/reminder jika user meminta,
- draft message/email.

Butuh konfirmasi:
- mengirim pesan/email,
- membuat automation/reminder berulang,
- mengubah memory permanen,
- membaca file personal sensitif,
- mengubah jadwal.

Dilarang default:
- shell/exec,
- edit config OpenClaw,
- delete file,
- browser login tanpa izin.
```

---

## Langkah Kerja

```text
1. Pahami maksud user.
2. Tentukan apakah ini:
   - pertanyaan,
   - catatan,
   - rencana,
   - reminder,
   - draft pesan,
   - memory update.
3. Ambil konteks yang relevan dari USER.md/MEMORY.md/notes.
4. Jangan ambil memory pribadi jika channel tidak aman.
5. Buat output:
   - jawaban,
   - rencana,
   - catatan,
   - draft,
   - checklist,
   - reminder.
6. Jika perlu menyimpan sesuatu, cek memory policy.
7. Jika aksi eksternal, tampilkan preview dan minta konfirmasi.
8. Ringkas hasil.
```

---

## Contoh Workflow: Membuat Catatan

User:

```text
Aira, catat ide: aku mau bikin aplikasi catatan Android yang offline, simpel, tapi UI-nya cantik.
```

Agent sehat:

```text
1. Menganggap ini sebagai request membuat note.
2. Membuat catatan di notes/app-ideas.md atau notes/android-notes-app.md.
3. Tidak menyimpan semua detail ke MEMORY.md.
4. Hanya memasukkan ke MEMORY.md jika proyek ini aktif/jangka panjang.
5. Memberi ringkasan:
   - catatan dibuat,
   - lokasi file,
   - poin utama.
```

Output ideal:

```text
Sudah dicatat sebagai ide proyek aplikasi catatan Android.

Intinya:
- offline-first,
- tanpa login/sync/cloud dulu,
- UI cantik dan simpel,
- fokus penggunaan pribadi.

Aku tidak memasukkannya ke memory permanen kecuali kamu ingin proyek ini jadi active project.
```

---

## Contoh Workflow: Membuat Reminder

User:

```text
Aira, ingatkan aku besok jam 8 malam untuk lanjut belajar OpenClaw.
```

Agent sehat:

```text
1. Deteksi ini scheduled reminder.
2. Pastikan waktu jelas.
3. Buat reminder jika tool tersedia.
4. Tidak perlu menyimpan detail panjang ke MEMORY.md.
5. Jika reminder recurring, minta konfirmasi pola.
```

Output ideal:

```text
Siap, aku akan mengingatkan besok pukul 20.00 untuk lanjut belajar OpenClaw.
```

---

## Memory Policy untuk Personal Assistant

Simpan:

```text
- preferensi komunikasi,
- proyek aktif,
- gaya belajar,
- keputusan jangka panjang,
- batasan penting,
- rutinitas yang user minta.
```

Jangan simpan:

```text
- emosi sesaat sebagai label permanen,
- data sensitif,
- isi chat pribadi lengkap,
- credential,
- nomor rekening,
- hal pribadi orang lain,
- izin destructive action.
```

OpenClaw menyimpan memory sebagai file Markdown di workspace; model hanya mengingat apa yang benar-benar tersimpan ke disk, jadi memory harus curated, bukan tempat membuang semua percakapan. ([OpenClaw][2])

---

## Risiko

```text
1. Memory terlalu personal.
2. Salah kirim pesan.
3. Reminder/automation berlebihan.
4. Channel group membocorkan memory pribadi.
5. Agent terlalu mengatur user.
6. Menyimpan asumsi sebagai fakta.
```

---

## Mitigasi

```text
- channel pribadi only,
- update memory dengan izin,
- external send wajib preview,
- reminder jelas waktunya,
- jangan simpan data sensitif,
- gunakan notes untuk detail, MEMORY.md untuk ringkasan.
```

---

# 11.4 Workflow 2 — Coding Agent

## Tujuan

Coding Agent membantu mengerjakan repo atau project teknis:

```text
- membaca repo,
- memahami issue,
- debugging,
- membuat patch,
- menjalankan test,
- menulis dokumentasi,
- merangkum perubahan.
```

Coding Agent harus dipisahkan dari Personal Agent karena ia butuh tools lebih kuat: file read/write, patch, dan mungkin shell command.

---

## Arsitektur Workflow

```text
User
  ↓
CLI / Discord #coding / WebChat dev
  ↓
Gateway
  ↓
Coding Agent
  ↓
Workspace coding / repo
  ↓
Skills:
- coding-assistant
- repo-debugger
  ↓
Tools:
- read
- edit/apply_patch
- exec terbatas
- web_search docs
  ↓
Patch + test result + summary
```

---

## Trigger

Contoh:

```text
"Cek bug login."
"Tambahkan fitur dark mode."
"Review struktur repo ini."
"Jalankan test dan cari error."
"Refactor bagian notes storage."
```

---

## Required Context

Coding Agent butuh:

```text
- struktur repo,
- file relevan,
- error log,
- issue/requirement,
- test command,
- style guide,
- dependency/framework,
- batas perubahan.
```

Ia tidak butuh:

```text
- memory pribadi user,
- catatan relasi,
- jadwal personal,
- email/calendar.
```

---

## Allowed Tools

```text
Boleh:
- read repo,
- edit/apply_patch,
- write file baru,
- exec untuk test/lint/build yang jelas,
- git status/diff,
- web_search dokumentasi.

Butuh konfirmasi:
- install/update dependency,
- migration database,
- command dengan side effect besar,
- delete file,
- edit config besar,
- broad rewrite.

Dilarang default:
- membaca .env/secrets,
- mengirim external message,
- akses memory personal,
- elevated command.
```

OpenClaw security docs menekankan bahwa `exec` tetap bisa mengubah filesystem meskipun write/edit tools dimatikan, sehingga agent yang benar-benar read-only juga harus membatasi `exec`/`process`. Untuk coding agent, `exec` sebaiknya tetap dibatasi ke test/lint/build yang jelas, idealnya dalam sandbox. ([OpenClaw][3])

---

## Langkah Kerja Coding Agent

```text
1. Pahami tugas user.
2. Inspect repo secara read-only.
3. Cari file relevan.
4. Baca file secukupnya.
5. Buat diagnosis/hipotesis.
6. Buat rencana perubahan minimal.
7. Jika perubahan kecil dan jelas, apply patch.
8. Jika perubahan besar, minta konfirmasi dulu.
9. Jalankan test/lint/build jika aman.
10. Jika test gagal, laporkan jujur.
11. Ringkas:
    - file diubah,
    - alasan perubahan,
    - hasil test,
    - risiko sisa.
```

---

## Contoh Workflow: Fix Bug Login

User:

```text
Cek kenapa login gagal setelah user restart aplikasi. Jangan ubah file dulu sebelum kamu tahu penyebabnya.
```

Agent sehat:

```text
1. Baca struktur repo.
2. Cari modul auth/session/storage.
3. Baca file terkait.
4. Cari flow login dan persistence.
5. Tidak mengedit dulu.
6. Buat diagnosis.
7. Minta izin patch.
```

Output ideal sebelum patch:

```text
Aku menemukan kemungkinan penyebabnya di session storage: token login tampaknya disimpan di memory runtime, tapi tidak dipersist ke local storage setelah restart.

Belum ada file yang aku ubah.

Rencana patch:
1. Simpan token/session ke storage lokal setelah login berhasil.
2. Load ulang session saat app start.
3. Tambahkan guard kalau token invalid.
4. Jalankan test auth/session.

Boleh aku lanjut buat patch minimal?
```

---

## Contoh Output Setelah Patch

```text
Perubahan selesai.

File diubah:
- src/auth/sessionStore.ts
- src/auth/loginFlow.ts
- tests/auth/sessionStore.test.ts

Yang diperbaiki:
- session sekarang dipersist setelah login,
- app mencoba restore session saat startup,
- token invalid akan membersihkan session.

Test:
- auth session tests: pass
- full test suite: belum dijalankan karena membutuhkan emulator Android.

Risiko sisa:
- perlu uji manual di perangkat untuk flow restart aplikasi.
```

---

## Risiko Coding Workflow

```text
1. Agent edit terlalu banyak.
2. Test tidak dijalankan.
3. Secret terbaca.
4. Dependency berubah tanpa izin.
5. Command berbahaya.
6. Patch tidak minimal.
7. Agent mengklaim berhasil tanpa bukti.
```

---

## Mitigasi

```text
- read-only inspection first,
- patch minimal,
- no secret reading,
- explain commands,
- test result wajib jujur,
- git diff summary,
- sandbox untuk exec,
- no broad rewrite tanpa izin.
```

---

# 11.5 Workflow 3 — Research Agent

## Tujuan

Research Agent mencari informasi, memverifikasi sumber, membuat ringkasan/laporan, dan memberi rekomendasi berdasarkan bukti.

Cocok untuk:

```text
- membandingkan tools,
- mencari dokumentasi terbaru,
- riset teknologi,
- mencari alternatif OpenClaw,
- memverifikasi klaim,
- menyusun laporan.
```

---

## Arsitektur Workflow

```text
User
  ↓
WebChat / Slack #research / Telegram DM
  ↓
Research Agent
  ↓
Skills:
- researcher
- source-verifier
  ↓
Tools:
- web_search
- web_fetch
- browser jika perlu
- write report
  ↓
Report + citations + caveats
```

Tools di OpenClaw mencakup web_search/web_fetch untuk riset publik, browser untuk situs yang butuh JavaScript/login, dan skills untuk memberi prosedur kerja. ([OpenClaw][4])

---

## Trigger

Contoh:

```text
"Cari alternatif OpenClaw untuk agentic AI."
"Bandingkan OpenClaw vs LangGraph."
"Verifikasi apakah OpenClaw mendukung multi-agent."
"Cari dokumentasi terbaru soal session.dmScope."
```

---

## Required Context

Research Agent butuh:

```text
- pertanyaan riset,
- batas scope,
- kriteria perbandingan,
- sumber prioritas,
- apakah butuh info terbaru,
- format output.
```

---

## Allowed Tools

```text
Boleh:
- web_search,
- web_fetch,
- browser isolated bila perlu,
- read/write report,
- memory search untuk laporan sebelumnya.

Butuh konfirmasi:
- login ke situs,
- download file,
- menyimpan laporan permanen,
- mengirim hasil ke channel lain.

Dilarang default:
- shell/exec,
- akses config pribadi,
- baca memory personal sensitif.
```

---

## Langkah Kerja Research Agent

```text
1. Definisikan pertanyaan riset.
2. Tentukan apakah informasi perlu terbaru.
3. Cari sumber primer/resmi dulu.
4. Cari sumber pembanding.
5. Baca sumber relevan.
6. Catat fakta penting.
7. Bandingkan klaim antar sumber.
8. Pisahkan:
   - fakta,
   - interpretasi,
   - rekomendasi.
9. Sertakan citation.
10. Jelaskan caveat/batas ketidakpastian.
```

---

## Contoh Workflow: Riset Alternatif OpenClaw

User:

```text
Cari alternatif OpenClaw untuk agentic AI, tapi fokus ke arsitektur dan keamanan.
```

Agent sehat:

```text
1. Search sumber resmi/GitHub/docs.
2. Kelompokkan alternatif:
   - agent framework,
   - coding agent platform,
   - automation agent,
   - local/self-hosted system.
3. Bandingkan:
   - channel support,
   - tool use,
   - memory,
   - multi-agent,
   - sandbox/security,
   - extensibility.
4. Jangan hanya menyebut nama.
5. Beri caveat kalau info belum lengkap.
```

Output ideal:

```text
Aku kelompokkan alternatifnya menjadi 4 kategori:

1. Agent framework
   Cocok kalau kamu ingin membangun sendiri.
2. Coding agent environment
   Cocok untuk repo dan development.
3. Automation/orchestration system
   Cocok untuk workflow dan integrasi.
4. Personal AI gateway/self-hosted assistant
   Paling dekat dengan OpenClaw.

Untuk kebutuhanmu, OpenClaw unik karena fokusnya bukan hanya agent framework, tapi gateway multi-channel yang bisa menghubungkan chat apps ke agent runtime.
```

---

## Risiko Research Workflow

```text
1. Sumber outdated.
2. Sumber tidak kredibel.
3. Citation palsu.
4. Overclaim.
5. Prompt injection dari web page.
6. Menggunakan browser login pribadi.
```

---

## Mitigasi

```text
- sumber primer dulu,
- citation wajib,
- tanggal diperhatikan,
- web content sebagai data,
- browser isolated,
- jangan login kecuali perlu,
- sebutkan batas riset.
```

---

# 11.6 Workflow 4 — OpenClaw Maintenance Agent

## Tujuan

OpenClaw Maintenance Agent menjaga kesehatan sistem OpenClaw:

```text
- memeriksa config,
- mengecek logs,
- mendeteksi error,
- mengecek channel status,
- mengecek skill/memory/tools,
- mendeteksi performa lambat,
- memberi rekomendasi perbaikan.
```

Ini agent yang harus **read-only default**. Maintenance agent boleh membantu diagnosis, tapi tidak boleh sembarangan “memperbaiki” tanpa izin.

---

## Arsitektur Workflow

```text
User/Admin
  ↓
WebChat admin / CLI
  ↓
Maintenance Agent
  ↓
Workspace maintenance
  ↓
Tools:
- read logs
- read config
- status/health check
- list skills/channels
  ↓
Diagnosis + safe recommendations
```

OpenClaw menyediakan health/status guidance seperti `openclaw status` untuk ringkasan lokal dan `openclaw status --all` untuk diagnosis lokal read-only yang aman dipaste untuk debugging; ini cocok sebagai bagian dari workflow maintenance. ([OpenClaw][5])

---

## Trigger

Contoh:

```text
"Cek kenapa bot Telegram tidak merespons."
"Cek kenapa skill tidak terbaca."
"Cek kenapa event_loop_delay tinggi."
"Cek apakah memory terlalu panjang."
"Cek config channel dan session."
```

---

## Required Context

Maintenance agent butuh:

```text
- gejala,
- waktu kejadian,
- logs,
- config relevant snippets,
- channel status,
- agent id,
- workspace path,
- perubahan terakhir,
- tool/skill yang bermasalah.
```

---

## Allowed Tools

```text
Boleh:
- read logs,
- read config,
- list workspace,
- list skills,
- check channel status,
- run safe status command bila diizinkan.

Butuh konfirmasi:
- restart gateway,
- edit config,
- delete logs,
- clear session,
- update package,
- run diagnostic command dengan side effect.

Dilarang default:
- destructive command,
- broad edit,
- expose secret,
- external send.
```

---

## Langkah Kerja Maintenance Agent

```text
1. Pahami gejala.
2. Kumpulkan bukti read-only.
3. Tentukan area:
   - channel,
   - gateway,
   - session,
   - workspace,
   - tool,
   - skill,
   - memory,
   - model/provider,
   - performance.
4. Buat daftar kemungkinan penyebab.
5. Prioritaskan penyebab paling mungkin.
6. Beri solusi aman.
7. Jangan ubah file/service tanpa konfirmasi.
8. Buat laporan maintenance.
```

---

## Contoh Workflow: Channel Tidak Merespons

User:

```text
Bot Telegram-ku tidak membalas.
```

Agent sehat:

```text
1. Cek apakah Gateway running.
2. Cek channel status.
3. Cek token/credential tanpa menampilkan secret.
4. Cek dmPolicy/allowlist/pairing.
5. Cek apakah sender ID masuk allowFrom.
6. Cek logs error.
7. Cek apakah session masuk tapi agent gagal.
```

OpenClaw docs memperingatkan agar tidak memakai session listing sebagai indikator socket-health channel, karena sessions mencerminkan stored conversation rows, bukan runtime provider state. Untuk health channel, gunakan status/probe yang sesuai. ([OpenClaw][6])

Output ideal:

```text
Kemungkinan utama:

1. Sender belum masuk allowlist/pairing.
2. Gateway jalan, tapi Telegram provider tidak connected.
3. Bot token salah/expired.
4. Pesan masuk, tapi route/session error.
5. Agent runtime gagal setelah pesan diterima.

Langkah aman:
- cek channel status/probe,
- cek logs Telegram provider,
- cek allowFrom,
- kirim pesan test dari owner,
- jangan reset session dulu sebelum tahu pesan masuk atau tidak.
```

---

## Contoh Workflow: Event Loop Delay / CPU Tinggi

User:

```text
Aku dapat liveness warning: event_loop_delay, CPU tinggi, agent macet.
```

Agent sehat:

```text
1. Minta/cek logs sekitar waktu kejadian.
2. Identifikasi active work:
   - tool exec,
   - browser,
   - model prewarm,
   - long-running process,
   - large file read,
   - too many channels,
   - infinite loop/retry.
3. Cek queue active/waiting.
4. Jangan langsung restart jika logs diperlukan.
5. Sarankan mitigasi:
   - stop long task,
   - batasi tools,
   - kurangi channels,
   - cek skill/automation,
   - restart setelah capture logs.
```

Output ideal:

```text
Dari gejala event_loop_utilization mendekati 1.0 dan CPU tinggi, kemungkinan ada pekerjaan CPU-bound atau tool/run yang memblokir event loop. Perlu dicek apakah sedang ada browser task, exec command panjang, model prewarm, atau automation yang loop.

Langkah aman:
1. simpan log sekitar warning,
2. cek active/waiting/queued tasks,
3. cek tool terakhir yang berjalan,
4. hentikan task jika jelas macet,
5. restart Gateway hanya setelah log penting diamankan.
```

---

## Risiko Maintenance Workflow

```text
1. Agent restart service tanpa izin.
2. Agent menghapus logs sebelum audit.
3. Agent mengubah config salah.
4. Agent expose token dari config.
5. Agent clear session/memory tanpa backup.
```

---

## Mitigasi

```text
- diagnosis read-only,
- backup sebelum perubahan,
- redact secrets,
- no restart/delete/clear tanpa izin,
- laporan bukti dulu,
- perubahan bertahap.
```

---

# 11.7 Workflow 5 — Learning Agent

## Tujuan

Learning Agent membantu user belajar secara bertahap:

```text
- membuat kurikulum,
- melacak progres,
- menguji pemahaman,
- membuat latihan,
- menyusun catatan belajar,
- memberi review berkala,
- menyesuaikan level kesulitan.
```

Ini cocok banget untuk kamu, karena kamu suka belajar hal baru secara mendalam dan bertahap.

---

## Arsitektur Workflow

```text
User
  ↓
Telegram DM / WebChat
  ↓
Learning Agent
  ↓
USER.md + MEMORY.md + learning-notes/
  ↓
Skills:
- learning-coach
- researcher
- memory-curator
  ↓
Tools:
- notes
- memory
- web_search jika perlu
- quiz generator
  ↓
Kurikulum + latihan + progress
```

---

## Trigger

Contoh:

```text
"Ajarin aku OpenClaw dari nol sampai mahir."
"Buat roadmap belajar agentic AI 8 minggu."
"Tes pemahamanku tentang tools dan skills."
"Buat latihan harian untuk belajar coding Android."
```

---

## Required Context

Learning Agent butuh:

```text
- topik belajar,
- level awal,
- tujuan akhir,
- waktu tersedia,
- preferensi gaya belajar,
- progres terakhir,
- hambatan,
- output yang diinginkan.
```

---

## Allowed Tools

```text
Boleh:
- read/write learning notes,
- update progress memory,
- web_search untuk sumber belajar,
- membuat quiz,
- menyimpan roadmap.

Butuh konfirmasi:
- reminder belajar,
- automation tracking,
- menyimpan progres jangka panjang,
- mengambil data dari file pribadi.

Dilarang default:
- shell/exec,
- external send,
- membaca data sensitif.
```

---

## Langkah Kerja Learning Agent

```text
1. Tentukan topik dan tujuan.
2. Ukur level awal.
3. Pecah materi menjadi modul.
4. Susun roadmap.
5. Beri materi bertahap.
6. Beri latihan.
7. Tes pemahaman.
8. Catat progres.
9. Review hambatan.
10. Sesuaikan rencana.
```

---

## Contoh Workflow: Belajar OpenClaw 8 Minggu

User:

```text
Buatkan roadmap belajar OpenClaw dari nol sampai bisa bangun sistem agent pribadi.
```

Agent sehat:

```text
1. Menentukan tujuan akhir.
2. Membagi 8 minggu:
   - mental model,
   - workspace,
   - skills/tools,
   - memory/context,
   - security,
   - multi-agent,
   - automation,
   - final build.
3. Membuat output per minggu:
   - tujuan,
   - materi,
   - praktik,
   - output,
   - indikator paham.
4. Menawarkan tracking progress.
```

Output ideal:

```text
Roadmap 8 minggu:

Minggu 1 — Mental Model
Tujuan:
Paham OpenClaw sebagai gateway agentic, bukan chatbot.

Praktik:
Gambar diagram channel → gateway → agent → tools.

Output:
Catatan mental model 1 halaman.

Indikator paham:
Kamu bisa menjelaskan perbedaan Gateway, runtime, workspace, tools, skills, dan memory tanpa melihat catatan.
```

---

## Contoh Workflow: Tes Pemahaman

User:

```text
Tes aku tentang perbedaan tools dan skills.
```

Agent sehat:

```text
1. Buat 5–10 pertanyaan.
2. Mulai dari konsep dasar.
3. Minta user jawab dulu.
4. Beri feedback.
5. Koreksi miskonsepsi.
6. Catat topik yang perlu review jika user setuju.
```

Contoh output:

```text
Oke, jawab tanpa lihat catatan dulu.

1. Dalam OpenClaw, apa bedanya tool dan skill?
2. Kenapa `exec` lebih berisiko daripada `read`?
3. Kalau skill menyuruh agent menjalankan semua command otomatis, apa masalahnya?
4. Kenapa `TOOLS.md` harus lebih kuat daripada instruksi skill?
5. Kapan cukup prompt biasa, dan kapan perlu skill?
```

---

## Memory Policy Learning Agent

Simpan:

```text
- topik belajar aktif,
- progres modul,
- preferensi metode belajar,
- kesulitan yang berulang,
- target jangka panjang.
```

Jangan simpan:

```text
- rasa gagal sesaat sebagai fakta,
- label negatif,
- data sensitif,
- semua jawaban quiz mentah,
- asumsi kemampuan tanpa bukti.
```

Versi memory baik:

```markdown
## Learning Progress
- User sedang mempelajari OpenClaw secara bertahap.
- Topik yang sudah dibahas: overview, mental model, architecture, bootstrap, skills, tools, memory, channels, multi-agent, security.
- User ingin pembahasan lanjut ke workflow nyata dan template.
```

---

## Risiko Learning Workflow

```text
1. Roadmap terlalu berat.
2. Agent terlalu menggurui.
3. Progres tidak dicatat.
4. Materi melompat terlalu advanced.
5. Memory menyimpan label negatif.
6. Tidak ada indikator paham.
```

---

## Mitigasi

```text
- modul kecil,
- latihan bertahap,
- indikator paham,
- review berkala,
- memory progres netral,
- jangan overplan.
```

---

# 11.8 Workflow Tambahan — Memory Curator

Walau tidak masuk daftar utama, ini penting untuk OpenClaw jangka panjang.

## Tujuan

Memory Curator merapikan memory agar agent tidak lupa, tidak salah ingat, dan tidak membawa asumsi lama.

---

## Arsitektur

```text
User/Admin
  ↓
Personal/Security channel
  ↓
Memory Curator Skill/Agent
  ↓
MEMORY.md + memory/
  ↓
Read-only review
  ↓
Proposal cleanup
  ↓
User approval
  ↓
Safe edit
```

---

## Langkah Kerja

```text
1. Baca MEMORY.md.
2. Baca memory notes relevan.
3. Temukan:
   - duplikasi,
   - stale entries,
   - sensitive entries,
   - asumsi sebagai fakta,
   - open loops selesai.
4. Buat proposal.
5. Jangan edit dulu.
6. Setelah approval, backup.
7. Edit memory.
8. Laporkan perubahan.
```

---

## Output Ideal

```text
Memory review selesai.

Temuan:
1. Ada 3 preferensi duplikat.
2. Ada 1 open loop yang sudah selesai.
3. Tidak ada credential.
4. Ada 2 entry yang perlu diberi tanggal karena bisa berubah.

Rekomendasi:
- gabungkan preferensi gaya jawaban,
- pindahkan detail proyek lama ke memory/projects.md,
- tandai entry OpenClaw version sebagai perlu verifikasi.

Belum ada file yang aku ubah.
```

---

# 11.9 Workflow Tambahan — Security Audit Routine

## Tujuan

Menjalankan audit berkala terhadap OpenClaw.

---

## Trigger

```text
- manual tiap minggu,
- sebelum menambah tool baru,
- setelah install skill baru,
- setelah membuka channel baru,
- setelah mengubah config,
- setelah incident/error.
```

---

## Langkah Kerja

```text
1. Cek channel aktif.
2. Cek dmPolicy/groupPolicy.
3. Cek session.dmScope.
4. Cek tools allow/deny.
5. Cek exec/browser/email/message.
6. Cek skills baru.
7. Cek memory sensitif.
8. Cek config exposure.
9. Cek backup terakhir.
10. Buat audit report.
```

---

## Output Ideal

```text
OpenClaw Weekly Security Audit

Risk Summary:
Medium

High priority:
1. `exec` masih aktif di main agent.
2. Telegram group belum mention-only.
3. MEMORY.md terlalu panjang dan perlu diringkas.

Medium:
1. Skill researcher belum punya "When Not to Use".
2. Backup workspace terakhir 12 hari lalu.

Low:
1. USER.md bisa diringkas agar context lebih hemat.

Tidak ada secret yang ditampilkan.
```

---

# 11.10 Workflow Tambahan — Research-to-Skill Conversion

Ini menarik: ketika sebuah workflow sering dipakai, ubah menjadi skill.

OpenClaw punya konsep Skill Workshop: memory menyimpan fakta/preferensi/konteks, sedangkan skills menyimpan prosedur reusable yang agent perlu ikuti di masa depan; Skill Workshop menjembatani turn yang berguna menjadi workspace skill durable dengan safety checks dan optional approval. ([OpenClaw][7])

## Tujuan

Mengubah prompt/workflow berulang menjadi `SKILL.md`.

---

## Trigger

```text
- user sering meminta format kerja yang sama,
- prompt mulai panjang dan berulang,
- workflow butuh SOP,
- output perlu konsisten,
- task memakai tools.
```

---

## Langkah Kerja

```text
1. Identifikasi workflow yang berulang.
2. Ambil langkah-langkah inti.
3. Tentukan trigger.
4. Tentukan when not to use.
5. Tentukan allowed/restricted tools.
6. Tentukan safety rules.
7. Tentukan output format.
8. Buat SKILL.md.
9. Test dengan prompt normal, ambigu, dan berisiko.
10. Aktifkan untuk agent yang tepat saja.
```

---

## Contoh

Prompt berulang:

```text
Audit OpenClaw-ku secara menyeluruh, mulai dari workspace, tools, skills, memory, channel, session, security, dan beri severity.
```

Jadikan skill:

```text
skills/openclaw-auditor/SKILL.md
```

Dengan isi:

```text
- purpose,
- trigger,
- workflow audit,
- read-only default,
- no secret exposure,
- severity format,
- safe recommendations.
```

---

# 11.11 Workflow Design Matrix

Supaya gampang, ini matriksnya.

| Workflow           | Agent             | Channel               | Tools                      | Memory                  | Risiko Utama              | Default Safety          |
| ------------------ | ----------------- | --------------------- | -------------------------- | ----------------------- | ------------------------- | ----------------------- |
| Personal Assistant | Personal          | DM/WebChat            | notes, memory, web         | personal prefs/projects | privacy leak              | private channel only    |
| Coding             | Coding            | CLI/private coding    | repo read/write, test exec | tech decisions          | file damage/secret leak   | sandbox + minimal patch |
| Research           | Research          | WebChat/research room | web_search/fetch/browser   | research notes          | misinformation/injection  | citation + source check |
| Maintenance        | Maintenance       | admin/CLI             | read logs/config/status    | maintenance history     | config damage             | read-only first         |
| Learning           | Learning          | DM/WebChat            | notes, memory, web         | progress                | overplanning/wrong memory | small modules           |
| Memory Curator     | Personal/Security | admin/private         | read/edit memory           | memory files            | memory corruption         | proposal before edit    |
| Security Audit     | Security          | admin/CLI             | read-only config/logs      | risk register           | secret exposure           | redact + read-only      |
| Research-to-Skill  | Skill Workshop    | admin/private         | write skill files          | procedures              | bad skill                 | review + test           |

---

# 11.12 Pattern: Read-Only First, Then Act

Ini pattern terbaik untuk hampir semua workflow OpenClaw.

```text
Phase 1 — Understand
- baca request,
- baca context,
- cek file/status,
- jangan ubah apa pun.

Phase 2 — Diagnose
- temukan masalah,
- buat hipotesis,
- pisahkan fakta/asumsi.

Phase 3 — Plan
- buat rencana,
- sebutkan target,
- sebutkan risiko.

Phase 4 — Ask Approval
- jika ada aksi berisiko,
- minta konfirmasi eksplisit.

Phase 5 — Execute
- gunakan tool minimal,
- jalankan langkah kecil.

Phase 6 — Verify
- cek hasil,
- test bila perlu.

Phase 7 — Report
- file/action yang dilakukan,
- hasil,
- risiko sisa,
- langkah berikutnya.
```

Kalau dibuat diagram:

```text
Read → Diagnose → Plan → Confirm → Act → Verify → Report
```

Ini pattern emas. Simpel, tapi menyelamatkan banyak hal.

---

# 11.13 Pattern: Draft Before Send

Untuk semua workflow komunikasi keluar:

```text
User asks to send
  ↓
Agent drafts
  ↓
Agent confirms recipient/channel/content
  ↓
User approves
  ↓
Agent sends
  ↓
Agent reports sent status
```

Contoh:

```text
Aira, kirim pesan ke grup bahwa besok meeting jam 8.
```

Agent sehat:

```text
Draft:
"Besok meeting dimulai pukul 08.00. Jangan lupa hadir tepat waktu."

Target:
Grup mana?

Aku belum mengirim. Setelah kamu konfirmasi grup dan isi pesannya, baru aku kirim.
```

Prinsip:

```text
Draft otomatis boleh.
Kirim otomatis jangan.
```

---

# 11.14 Pattern: Proposal Before Cleanup

Untuk memory/workspace/config cleanup:

```text
User asks cleanup
  ↓
Agent reviews read-only
  ↓
Agent proposes changes
  ↓
User approves
  ↓
Agent backs up
  ↓
Agent edits
  ↓
Agent reports diff
```

Contoh:

```text
Rapikan semua memory.
```

Agent sehat:

```text
Aku akan review dulu dan membuat proposal. Aku tidak akan menghapus atau mengedit memory sebelum kamu setuju.
```

Ini penting karena “rapikan” sering ambigu. Rapikan bagi user bisa berarti “susun ulang”, bagi agent bisa jadi “hapus yang dianggap tidak penting”. Bahaya kecil dengan semangat besar.

---

# 11.15 Pattern: Evidence-Based Report

Untuk audit/research/debugging:

```text
Claim
  ↓
Evidence
  ↓
Risk
  ↓
Recommendation
  ↓
Safe next step
```

Format:

```markdown
### Finding: [Judul]
Severity: High
Evidence: ...
Risk: ...
Recommendation: ...
Safe Next Step: ...
```

Ini membuat laporan agent bisa diaudit.

Jangan cuma:

```text
Konfigurasimu kurang aman.
```

Lebih baik:

```text
Finding: DM policy terlalu terbuka
Severity: High
Evidence: dmPolicy = "open", allowFrom = ["*"]
Risk: siapa pun bisa memicu agent.
Recommendation: ubah ke pairing/allowlist.
Safe next step: audit sender yang sudah pernah masuk sebelum mengubah policy.
```

---

# 11.16 Pattern: Memory Candidate Before Promotion

Untuk memory:

```text
Conversation detail
  ↓
Daily note
  ↓
Candidate memory
  ↓
User confirmation / stability check
  ↓
MEMORY.md
```

Jangan langsung:

```text
User berkata sekali → masuk MEMORY.md permanen.
```

Lebih sehat:

```text
User berkata sesuatu yang mungkin berguna → kandidat.
Kalau stabil/relevan → promote.
```

---

# 11.17 Pattern: Public Channel Low Privilege

Untuk group/public channel:

```text
Public/group input
  ↓
Limited agent
  ↓
No private memory
  ↓
No shell
  ↓
No file write
  ↓
No external send
```

OpenClaw security docs menyarankan memisahkan personal-data agents dari team/shared workflows dan memakai separate agents/gateways dengan tools minimal untuk team workflows. ([OpenClaw][3])

Prinsip:

```text
Semakin publik channel, semakin kecil izin agent.
```

---

# 11.18 Workflow Failure Handling

Workflow matang harus tahu cara gagal dengan aman.

## Jika tool gagal

Jangan:

```text
Sepertinya berhasil.
```

Tapi:

```text
Tool gagal. Saya belum bisa memastikan hasilnya.
Error yang terlihat: ...
Langkah aman berikutnya: ...
```

## Jika file tidak ditemukan

Jangan:

```text
Saya sudah membaca filenya.
```

Tapi:

```text
Saya belum menemukan file itu di workspace yang terlihat. Perlu path yang benar atau cek workspace aktif.
```

## Jika context kurang

```text
Saya bisa memberi analisis sementara, tapi belum bisa memastikan tanpa melihat config/log/file terkait.
```

## Jika ada secret

```text
Saya menemukan nilai yang tampaknya secret. Saya tidak akan menampilkannya. Saya akan merujuknya sebagai [REDACTED].
```

## Jika user minta aksi ambigu

```text
Permintaan “beresin” masih ambigu. Aku akan mulai dari audit read-only dan membuat proposal, bukan langsung mengubah file.
```

---

# 11.19 Contoh File Workflow Documentation

Kamu bisa bikin folder:

```text
workspace/
  workflows/
    personal-assistant.md
    coding-agent.md
    research-agent.md
    maintenance-agent.md
    learning-agent.md
    security-audit.md
```

Contoh `workflows/coding-agent.md`:

```markdown
# Coding Agent Workflow

## Goal
Help inspect, debug, patch, and test code safely.

## Trigger
Use when user asks to fix, inspect, implement, refactor, or test code.

## Agent
coding-agent

## Allowed Tools
- read
- edit
- apply_patch
- exec for test/lint/build only
- web_search for documentation

## Restricted Tools
- dependency install/update requires confirmation
- delete requires confirmation
- migration requires confirmation
- no email/message external
- no personal memory

## Steps
1. Understand task.
2. Inspect repo read-only.
3. Read relevant files.
4. Diagnose.
5. Plan minimal change.
6. Patch.
7. Test.
8. Report.

## Safety
- Do not read secrets.
- Do not run untrusted scripts.
- Do not broad rewrite.
- Do not claim tests passed unless run.

## Output
- Diagnosis
- Files changed
- Test result
- Risk remaining
```

Ini berguna supaya workflow tidak cuma hidup di kepala agent, tapi bisa diaudit manusia.

---

# 11.20 Rekomendasi Workflow Awal untuk Kamu

Untuk tahap kamu sekarang, aku sarankan mulai dengan 5 workflow inti:

```text
1. OpenClaw Learning Workflow
2. OpenClaw Security Audit Workflow
3. Memory Curator Workflow
4. Coding Agent Workflow
5. Research Report Workflow
```

Kenapa ini?

Karena kebutuhanmu sekarang adalah:

```text
- memahami OpenClaw secara mendalam,
- menjaga agar agent tidak halu/lupa konteks,
- membangun sistem pribadi aman,
- eksplorasi coding/app,
- riset tools/alternatif agentic AI.
```

Urutan implementasi terbaik:

```text
Minggu 1:
- tulis workflow learning dan audit.

Minggu 2:
- tulis memory curator workflow.

Minggu 3:
- tulis coding workflow.

Minggu 4:
- tulis research workflow.

Setelah itu:
- ubah workflow yang sering dipakai menjadi skill.
```

---

# 11.21 Checklist Workflow

Gunakan ini untuk menilai workflow OpenClaw:

```text
[ ] Tujuan workflow jelas.
[ ] Trigger jelas.
[ ] Agent yang menangani jelas.
[ ] Channel yang boleh memicu jelas.
[ ] Context yang dibutuhkan jelas.
[ ] Tools yang boleh dipakai jelas.
[ ] Tools yang dilarang jelas.
[ ] Aksi yang butuh konfirmasi jelas.
[ ] Memory policy jelas.
[ ] Output format jelas.
[ ] Failure handling jelas.
[ ] Risiko utama disebutkan.
[ ] Ada langkah verifikasi.
[ ] Ada logging/audit.
[ ] Ada rollback/backup untuk aksi berisiko.
```

Kalau workflow tidak punya minimal 10 dari 15 poin ini, workflow itu belum siap untuk automation serius.

---

# 11.22 Ringkasan Bagian 11

Workflow nyata OpenClaw adalah cara merangkai:

```text
Channel
  ↓
Gateway
  ↓
Agent
  ↓
Context / Memory
  ↓
Skill
  ↓
Tools
  ↓
Output / Action
  ↓
Logging / Memory / Report
```

Kita membedah lima workflow utama:

```text
1. AI Personal Assistant
2. Coding Agent
3. Research Agent
4. OpenClaw Maintenance Agent
5. Learning Agent
```

Ditambah workflow pendukung:

```text
- Memory Curator
- Security Audit Routine
- Research-to-Skill Conversion
```

Prinsip paling penting:

```text
1. Read-only first.
2. Draft before send.
3. Proposal before cleanup.
4. Evidence-based report.
5. Memory candidate before promotion.
6. Public channel = low privilege.
7. Confirm before risky action.
8. Report honestly when failing.
```

Opini teknisku: **workflow adalah bagian yang mengubah OpenClaw dari “agent pintar” menjadi “sistem kerja yang bisa diandalkan”.** Tanpa workflow, agent hanya improvisasi. Kadang bagus, kadang ngawur. Dengan workflow, agent punya rel kerja—bukan rel kereta cepat yang nabrak kalau belok, tapi rel berpikir yang membuat aksi lebih aman dan konsisten.

Bagian berikutnya kita akan membahas **Bagian 12 — Bedah File dan Folder Ideal**, yaitu menyusun struktur workspace OpenClaw yang rapi: `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `USER.md`, `IDENTITY.md`, `HEARTBEAT.md`, `BOOTSTRAP.md`, `memory/`, `notes/`, `logs/`, `audits/`, `prompts/`, dan `skills/`.

Ke [Bagian 12: Bedah File dan Folder Ideal](12-bedah-file-dan-folder-ideal.md)

[1]: https://docs.openclaw.ai/concepts/architecture?utm_source=chatgpt.com "Gateway architecture"
[2]: https://docs.openclaw.ai/concepts/memory?utm_source=chatgpt.com "Memory overview - OpenClaw"
[3]: https://docs.openclaw.ai/gateway/security?utm_source=chatgpt.com "Security"
[4]: https://docs.openclaw.ai/tools?utm_source=chatgpt.com "Overview - OpenClaw"
[5]: https://docs.openclaw.ai/gateway/health?utm_source=chatgpt.com "Health checks - OpenClaw"
[6]: https://docs.openclaw.ai/cli/channels?utm_source=chatgpt.com "Channels - OpenClaw"
[7]: https://docs.openclaw.ai/plugins/skill-workshop?utm_source=chatgpt.com "Skill workshop plugin"
