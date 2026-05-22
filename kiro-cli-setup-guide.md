# Panduan Setup Kiro CLI — Workspace-Level Configuration

> **Versi:** Kiro CLI 2.3.0 | **Terakhir diperbarui:** Mei 2026
> **Sumber:** kiro.dev/docs, changelog, dan komunitas

---

## Daftar Isi

1. [Struktur Direktori .kiro/](#1-struktur-direktori-kiro)
2. [Steering Files](#2-steering-files-kirosteering)
3. [Hooks](#3-hooks)
4. [Custom Agents / Subagents](#4-custom-agents--subagents-kiroagents)
5. [Slash Commands Reference](#5-slash-commands-reference)
6. [Workflow Harian — Best Practices](#6-workflow-harian--best-practices)
7. [Tips Pro](#7-tips-pro)
8. [Quick Reference Card](#8-quick-reference-card)
9. [Referensi](#9-referensi)

---

## 1. Struktur Direktori .kiro/

Semua konfigurasi ditempatkan di **root project** Anda:

    your-project/
    ├── .kiro/
    │   ├── steering/              # Instruksi persisten untuk Kiro
    │   │   ├── 00-product.md
    │   │   ├── 10-architecture.md
    │   │   ├── 20-coding-standards.md
    │   │   ├── 30-testing.md
    │   │   ├── 40-git-workflow.md
    │   │   └── 50-api-conventions.md
    │   ├── hooks/                 # Automasi event-driven (Kiro IDE)
    │   │   ├── auto-lint.kiro.hook
    │   │   └── auto-test.kiro.hook
    │   ├── agents/                # Custom subagents
    │   │   ├── code-reviewer.md
    │   │   └── test-generator.md
    │   └── specs/                 # Spec-driven development artifacts
    │       └── feature-xyz/
    │           ├── requirements.md
    │           ├── design.md
    │           └── tasks.md
    ├── src/
    └── ...

**Naming Convention:** Gunakan prefix numerik (00-, 10-, 20-) untuk mengontrol urutan loading dan memudahkan organisasi visual.

---

## 2. Steering Files (.kiro/steering/)

### Apa Itu Steering?

Steering memberikan Kiro **pengetahuan persisten** tentang project Anda melalui file markdown. Tanpa steering, Anda harus menjelaskan konvensi di setiap session baru. Dengan steering, Kiro otomatis mengikuti pola dan standar yang sudah Anda tetapkan.

### Mode Inclusion (Frontmatter)

Setiap steering file memiliki **mode inclusion** yang dikontrol via YAML frontmatter di bagian atas file:

| Mode | Kapan Aktif | Use Case |
|------|-------------|----------|
| always | Selalu di-load setiap interaksi | Konvensi utama, tech stack, arsitektur |
| fileMatch | Hanya saat Kiro membaca file yang cocok glob pattern | Aturan API saat context menyangkut file tertentu |
| manual | Hanya saat Anda invoke via slash command | Deployment guide, referensi jarang dipakai |

### Format Dasar

Setiap steering file diawali YAML frontmatter, diikuti konten markdown:

    ---
    inclusion: always
    ---

    # Judul Steering

    Isi instruksi Anda di sini...

Untuk mode fileMatch, tambahkan field fileMatch dengan glob pattern:

    ---
    inclusion: fileMatch
    fileMatch: tests/**/*
    ---

### Referensi File Eksternal

Anda bisa referensi file lain (OpenAPI spec, GraphQL schema, dsb.) agar Kiro membacanya sebagai konteks tambahan. Gunakan syntax:

    #[[file:docs/openapi.yaml]]

### Prioritas

Jika ada konflik antara global (~/.kiro/steering/) dan workspace (.kiro/steering/), **workspace steering selalu menang**. Ini memungkinkan Anda punya aturan global yang bisa di-override per project.

---

### Contoh Steering Files

Berikut adalah template yang bisa Anda adaptasi untuk project apapun. Salin isi di bawah setiap heading ke file yang sesuai.

---

### File: .kiro/steering/00-product.md

    ---
    inclusion: always
    ---

    # Product Context

    ## Tentang Aplikasi
    - Nama: [Nama Project Anda]
    - Deskripsi: [Deskripsi singkat tujuan aplikasi]
    - Target user: [Siapa yang menggunakan aplikasi ini]
    - Domain: [Domain bisnis — e-commerce, fintech, edtech, dll.]

    ## Fitur Utama
    - [Fitur 1]
    - [Fitur 2]
    - [Fitur 3]

    ## Batasan / Constraint
    - [Batasan teknis atau bisnis yang perlu diketahui Kiro]

---

### File: .kiro/steering/10-architecture.md

    ---
    inclusion: always
    ---

    # Architecture & Tech Stack

    ## Stack
    - Language: [contoh: TypeScript, PHP, Python, Go]
    - Framework: [contoh: Next.js, Laravel, FastAPI, Gin]
    - Database: [contoh: PostgreSQL, MySQL, MongoDB]
    - Cache/Queue: [contoh: Redis, RabbitMQ]
    - Infrastructure: [contoh: AWS, GCP, Docker, Kubernetes]

    ## Design Patterns
    - [Pola arsitektur — Repository Pattern, Clean Architecture, CQRS, dll.]
    - [Jelaskan layer/separation of concerns yang diterapkan]

    ## Struktur Direktori
    - [Jelaskan konvensi penamaan folder]
    - [Di mana setiap jenis kode ditempatkan]

    ## Database Conventions
    - [Naming convention untuk tabel dan kolom]
    - [Aturan migrasi]
    - [Aturan relasi dan constraint]

---

### File: .kiro/steering/20-coding-standards.md

    ---
    inclusion: always
    ---

    # Coding Standards

    ## Umum
    - [Aturan formatting — indentasi, max line length, dll.]
    - [Aturan typing — strict types, type hints, dll.]
    - [Aturan import/dependency ordering]

    ## Naming Conventions
    - Class/Component: [contoh: PascalCase]
    - Function/Method: [contoh: camelCase]
    - Variable: [contoh: camelCase]
    - Constant: [contoh: UPPER_SNAKE_CASE]
    - File: [contoh: kebab-case.ts atau PascalCase.tsx]
    - Database: [contoh: snake_case]

    ## Best Practices
    - [Aturan spesifik framework Anda]
    - [Aturan error handling]
    - [Aturan logging]

    ## Anti-patterns (JANGAN dilakukan)
    - [Daftar pola yang harus dihindari, sertakan contoh jika perlu]

---

### File: .kiro/steering/30-testing.md

    ---
    inclusion: fileMatch
    fileMatch: tests/**/*
    ---

    # Testing Standards

    ## Struktur
    - [Di mana test ditempatkan]
    - [Konvensi penamaan file test]

    ## Konvensi
    - [Format nama test function/method]
    - [Aturan assertion]
    - [Aturan mock/stub]
    - [Aturan test data]

    ## Coverage
    - [Minimum coverage yang diharapkan]
    - [Apa saja yang WAJIB di-test]

---

### File: .kiro/steering/40-git-workflow.md

    ---
    inclusion: manual
    ---

    # Git Workflow

    ## Branch Naming
    - Feature: feature/nama-fitur
    - Bugfix: fix/deskripsi-bug
    - Hotfix: hotfix/deskripsi
    - Release: release/v1.x.x

    ## Commit Message (Conventional Commits)
    - feat: deskripsi fitur baru
    - fix: deskripsi perbaikan bug
    - refactor: deskripsi refactoring
    - docs: deskripsi perubahan dokumentasi
    - test: deskripsi penambahan/perubahan test
    - chore: maintenance task

    ## Rules
    - [Aturan branching — dari mana branch dibuat]
    - [Aturan PR/MR — siapa yang review, merge strategy]
    - [Aturan release]

---

### File: .kiro/steering/50-api-conventions.md

    ---
    inclusion: fileMatch
    fileMatch: src/api/**/*
    ---

    # API Conventions

    ## Response Format
    [Definisikan format standar response API — success dan error]

    ## HTTP Status Codes
    - 200: Success
    - 201: Created
    - 204: No Content
    - 400: Bad Request
    - 401: Unauthorized
    - 403: Forbidden
    - 404: Not Found
    - 422: Validation Error
    - 500: Server Error

    ## Pagination
    [Format pagination yang digunakan]

    ## Versioning
    [Strategi versioning API — URL path, header, dll.]

    ## Authentication
    [Mekanisme auth — Bearer token, API key, OAuth, dll.]

---

## 3. Hooks

Hooks adalah automasi yang dijalankan pada event tertentu. Ada dua konteks hooks di Kiro:

### 3.1 Hooks di Kiro IDE (.kiro/hooks/)

Setiap hook adalah file tersendiri di .kiro/hooks/ dengan struktur when/then — event apa yang memicu, dan aksi apa yang dilakukan.

**Trigger types:**

| Trigger | Kapan Aktif |
|---------|-------------|
| File Saved | Saat file di-save |
| File Created | Saat file baru dibuat |
| File Deleted | Saat file dihapus |
| Manual | Saat dipanggil manual oleh user |
| Pre Task Execution | Sebelum spec task dijalankan |
| Post Task Execution | Setelah spec task selesai |

**Action types:**

| Action | Fungsi |
|--------|--------|
| runCommand | Eksekusi shell command |
| askAgent | Kirim prompt ke Kiro agent untuk ditindaklanjuti |

**Contoh: Auto-format saat file disimpan (.kiro/hooks/auto-lint.kiro.hook):**

    {
      "title": "Auto Format on Save",
      "description": "Jalankan formatter saat file disimpan",
      "triggers": [
        {
          "type": "file_saved",
          "filePath": "src/**/*"
        }
      ],
      "actions": [
        {
          "type": "runCommand",
          "command": "npm run format -- ${filePath}"
        }
      ]
    }

**Contoh: Auto-generate test saat file baru dibuat (.kiro/hooks/auto-test.kiro.hook):**

    {
      "title": "Generate Test for New File",
      "description": "Saat file source baru dibuat, generate test-nya",
      "triggers": [
        {
          "type": "file_created",
          "filePath": "src/**/*"
        }
      ],
      "actions": [
        {
          "type": "askAgent",
          "prompt": "Buatkan unit test untuk file yang baru dibuat di ${filePath}. Ikuti testing standards di steering."
        }
      ]
    }

### 3.2 Hooks di Kiro CLI (Agent Config JSON)

Di Kiro CLI, hooks dikonfigurasi di dalam **agent configuration file** dan berjalan pada lifecycle agent:

    {
      "hooks": {
        "agentSpawn": [
          {
            "command": "echo 'Agent started'",
            "timeout_ms": 5000
          }
        ],
        "preToolUse": [
          {
            "command": "echo 'Akan menggunakan tool'",
            "timeout_ms": 3000
          }
        ],
        "postToolUse": [
          {
            "command": "npm run lint -- --quiet",
            "timeout_ms": 10000
          }
        ]
      }
    }

**CLI Hook Trigger Points:**

| Trigger | Kapan |
|---------|-------|
| agentSpawn | Saat agent/subagent pertama kali mulai |
| preToolUse | Sebelum tool dieksekusi |
| postToolUse | Setelah tool selesai dieksekusi |

---

## 4. Custom Agents / Subagents (.kiro/agents/)

### Apa Itu Subagent?

Subagent adalah agent anak yang berjalan dengan **context terisolasi** sendiri. Gunakan subagent ketika ingin:

- Menjaga percakapan utama tetap ringkas
- Menjalankan beberapa task independen secara paralel
- Memanfaatkan agent yang dioptimalkan untuk task spesifik
- Membuat pipeline (chain) agent berurutan

### Cara Kerja

    ┌───────────────────────────────────────────┐
    │          Agent Utama (Chat Anda)          │
    │                                           │
    │   "Refactor modul auth dan update docs"   │
    │          │                  │             │
    │   ┌──────▼───────┐  ┌──────▼───────┐    │
    │   │  Subagent A  │  │  Subagent B  │    │
    │   │  (Refactor)  │  │  (Docs)      │    │
    │   └──────────────┘  └──────────────┘    │
    │          │                  │             │
    │          └───── Hasil ──────┘             │
    └───────────────────────────────────────────┘

### Format Custom Agent (Markdown)

Lokasi: .kiro/agents/nama-agent.md

Setiap file agent terdiri dari YAML frontmatter + system prompt di body:

    ---
    name: Nama Agent
    description: Deskripsi singkat kapabilitas agent (Kiro memilih agent berdasarkan ini)
    tools:
      - read
      - write
      - bash
      - grep
      - glob
    ---

    System prompt untuk agent dituliskan di sini.
    Jelaskan peran, aturan, dan cara kerja agent secara detail.

### Contoh: .kiro/agents/code-reviewer.md

    ---
    name: Code Reviewer
    description: Reviews code for best practices, security vulnerabilities, and performance issues
    tools:
      - read
      - grep
      - glob
    ---

    Anda adalah code reviewer yang teliti. Tugas Anda:

    1. Periksa apakah kode mengikuti coding standards di steering
    2. Deteksi performance issues (N+1 queries, unnecessary loops, memory leaks)
    3. Identifikasi security vulnerabilities (injection, XSS, mass assignment, dll.)
    4. Periksa error handling yang proper
    5. Cek apakah separation of concerns diterapkan dengan benar
    6. Identifikasi code smells dan duplikasi

    Output format:
    - Yang sudah baik (OK)
    - Warning (sebaiknya diperbaiki)
    - Critical (harus diperbaiki segera)
    - Suggestion (opsional, untuk improvement)

### Contoh: .kiro/agents/test-generator.md

    ---
    name: Test Generator
    description: Generates comprehensive unit and integration tests
    tools:
      - read
      - write
      - bash
      - grep
    ---

    Anda adalah test generator. Tugas Anda:

    1. Baca file source yang diberikan
    2. Identifikasi semua public methods/functions dan edge cases
    3. Generate test cases yang mencakup:
       - Happy path (normal flow)
       - Validation errors (invalid input)
       - Authorization/permission checks
       - Edge cases (empty, null, boundary values)
       - Error handling (exceptions, timeouts)

    Ikuti testing standards yang ada di steering files project ini.

### Cara Menggunakan Subagent

**Otomatis:** Kiro akan otomatis meluncurkan subagent yang sesuai berdasarkan field description.

**Manual via prompt:**

    > Run subagents to review the auth module and generate tests simultaneously

**Via slash command:**

    /agent list              → lihat semua agent tersedia
    /agent create my-agent   → buat agent baru
    /agent edit my-agent     → edit agent yang ada

### Pattern: Player-Coach

Pola populer dimana satu subagent menghasilkan output (player), dan subagent lain me-review hasilnya (coach). Feedback dari coach dikirim kembali ke player untuk iterasi — menghasilkan output yang lebih berkualitas.

---

## 5. Slash Commands Reference

### Perintah Dalam Chat Session

| Command | Fungsi |
|---------|--------|
| /help | Tampilkan bantuan (/help --legacy untuk list klasik) |
| /compact | Ringkas percakapan untuk membebaskan context window |
| /plan | Masuk ke Plan Agent (breakdown ide jadi structured tasks) |
| /tools | Kelola permission tools |
| /tools trust | Trust tool tertentu untuk session ini |
| /tools trust-all | Trust semua tools tanpa konfirmasi |
| /tools untrust | Revoke trust untuk tool tertentu |
| /tools reset | Reset semua permission ke default |
| /agent list | Lihat daftar agent tersedia |
| /agent create | Buat custom agent baru |
| /agent edit | Edit agent yang sudah ada |
| /session-id | Tampilkan ID session saat ini |
| /editor | Buka editor untuk prompt multi-baris panjang |

### Keyboard Shortcuts

| Shortcut | Fungsi |
|----------|--------|
| Shift + Tab | Toggle Plan mode dan Execution mode |
| Alt + Enter | Baris baru di input (multi-line prompt) |
| Ctrl + C | Cancel response yang sedang berjalan |

### File References

Gunakan @path/to/file untuk menyertakan konten file langsung ke prompt tanpa tool call (hemat token):

    > Refactor fungsi ini agar lebih readable: @src/utils/calculate.ts

---

## 6. Workflow Harian — Best Practices

### A. Setup Awal Project (Sekali)

    # 1. Buat struktur .kiro
    mkdir -p .kiro/steering .kiro/hooks .kiro/agents

    # 2. Buat steering files sesuai contoh di atas
    # 3. (Opsional) Buat hooks dan custom agents

    # 4. Commit ke repository agar tim bisa pakai
    git add .kiro/
    git commit -m "chore: add kiro workspace configuration"

### B. Fitur Baru (Kompleks) — Gunakan Plan / Spec

Jalankan kiro-cli, lalu di dalam chat:

    > /plan
    > Saya ingin membangun fitur [deskripsi fitur] dengan requirement:
    > 1. [requirement 1]
    > 2. [requirement 2]
    > 3. [requirement 3]

Plan Agent akan breakdown menjadi Requirements, Design, dan Tasks. Tekan Shift+Tab untuk masuk execution mode dan mulai implementasi.

### C. Bug Fix / Task Kecil — Langsung Chat

    > Ada bug di [deskripsi]. Expected behavior: [X]. Actual behavior: [Y].
    > File terkait: @src/path/to/file

### D. Review Code — Delegasi ke Subagent

    > Run subagents to review all changes in src/modules/auth/
    > for security issues and coding standard violations

### E. Context Window Penuh — Compact

    > /compact

Kiro meringkas history tapi mempertahankan informasi kunci.

### F. Resume Session Sebelumnya

    kiro-cli chat --resume              # resume session terakhir dari direktori ini
    kiro-cli chat --resume-id <ID>      # resume session spesifik

### G. Menggunakan Agent Tertentu

    kiro-cli --agent code-reviewer      # mulai session dengan agent tertentu

---

## 7. Tips Pro

| Tips | Penjelasan |
|------|-----------|
| Steering spesifik lebih baik dari generik | "Method max 20 baris" lebih baik dari "tulis kode yang clean" |
| Gunakan anti-patterns | Tunjukkan contoh BAIK vs BURUK di steering agar Kiro paham konteks |
| fileMatch hemat token | Steering testing hanya di-load saat context menyangkut file test |
| manual untuk referensi jarang | Deployment guide tidak perlu selalu di-load di setiap interaksi |
| 1 concern = 1 file | Pisahkan steering per topik, jangan campur dalam satu file besar |
| Prefix numerik | 00-, 10-, 20- untuk urutan prioritas dan organisasi visual |
| Reference file external | Gunakan #[[file:docs/spec.yaml]] untuk inject konteks dari file lain |
| Trust tools di awal session | /tools trust-all agar tidak terus-menerus diminta konfirmasi |
| Plan sebelum execute | /plan dulu untuk fitur kompleks, langsung chat untuk task kecil |
| Commit .kiro/ ke repo | Agar seluruh tim mendapat benefit yang sama dari konfigurasi Kiro |
| Iterasi steering | Update steering seiring project berkembang — ini dokumen hidup |

---

## 8. Quick Reference Card

    ┌──────────────────────────────────────────────────────────┐
    │                   KIRO CLI CHEATSHEET                     │
    ├──────────────────────────────────────────────────────────┤
    │                                                          │
    │  MEMULAI                                                 │
    │    kiro-cli                     → mulai chat             │
    │    kiro-cli --agent <name>      → mulai dengan agent     │
    │    kiro-cli chat --resume       → lanjutkan session      │
    │    kiro-cli chat --resume-id X  → resume session by ID   │
    │                                                          │
    │  DALAM CHAT                                              │
    │    /plan          → masuk mode planning                  │
    │    /compact       → ringkas history percakapan           │
    │    /tools         → kelola permissions                   │
    │    /agent list    → lihat agents tersedia                │
    │    /agent create  → buat agent baru                      │
    │    /help          → bantuan                              │
    │    /session-id    → lihat ID session                     │
    │    /editor        → editor untuk prompt panjang          │
    │    Shift+Tab      → toggle plan / execute                │
    │    Alt+Enter      → baris baru (multi-line)              │
    │    @file/path     → include file content ke prompt       │
    │                                                          │
    │  SUBAGENTS                                               │
    │    "Run subagents to..."   → delegasi task               │
    │    .kiro/agents/*.md       → custom subagent definitions │
    │                                                          │
    │  STEERING                                                │
    │    .kiro/steering/*.md     → instruksi persisten         │
    │    inclusion: always       → selalu aktif                │
    │    inclusion: fileMatch    → aktif saat pattern cocok    │
    │    inclusion: manual       → aktif saat dipanggil        │
    │    #[[file:path]]          → referensi file external     │
    │                                                          │
    │  HOOKS (IDE)                                             │
    │    Lokasi: .kiro/hooks/*.kiro.hook                       │
    │    Triggers: file_saved, file_created, file_deleted,     │
    │              manual, pre/post_task_execution              │
    │    Actions: runCommand, askAgent                         │
    │                                                          │
    │  HOOKS (CLI — di agent config JSON)                      │
    │    agentSpawn    → saat agent mulai                      │
    │    preToolUse    → sebelum tool dijalankan               │
    │    postToolUse   → setelah tool selesai                  │
    │                                                          │
    └──────────────────────────────────────────────────────────┘

---

## 9. Referensi

| Topik | URL |
|-------|-----|
| Docs utama | https://kiro.dev/docs/ |
| CLI Docs | https://kiro.dev/docs/cli/ |
| Steering (CLI) | https://kiro.dev/docs/cli/steering/ |
| Steering (IDE) | https://kiro.dev/docs/steering/ |
| Hooks (CLI) | https://kiro.dev/docs/cli/hooks/ |
| Hooks (IDE) | https://kiro.dev/docs/hooks/ |
| Hook Examples | https://kiro.dev/docs/hooks/examples/ |
| Hook Types | https://kiro.dev/docs/hooks/types/ |
| Slash Commands | https://kiro.dev/docs/cli/reference/slash-commands/ |
| CLI Commands | https://kiro.dev/docs/cli/reference/cli-commands/ |
| Built-in Tools | https://kiro.dev/docs/cli/reference/built-in-tools/ |
| Subagents (CLI) | https://kiro.dev/docs/cli/chat/subagents/ |
| Subagents (IDE) | https://kiro.dev/docs/chat/subagents/ |
| Custom Agents — Creating | https://kiro.dev/docs/cli/custom-agents/creating/ |
| Custom Agents — Config | https://kiro.dev/docs/cli/custom-agents/configuration-reference/ |
| Agent Examples | https://kiro.dev/docs/cli/custom-agents/examples |
| Plan Agent | https://kiro.dev/docs/cli/chat/planning-agent/ |
| Specs | https://kiro.dev/docs/specs/ |
| Specs Best Practices | https://kiro.dev/docs/specs/best-practices/ |
| First Project | https://kiro.dev/docs/getting-started/first-project |
| File References | https://kiro.dev/docs/cli/chat/file-references/ |
| Session Management | https://kiro.dev/docs/cli/chat/session-management/ |
| Tool Permissions | https://kiro.dev/docs/cli/chat/permissions/ |
| Headless Mode (CI/CD) | https://kiro.dev/blog/introducing-headless-mode/ |
| CLI Changelog | https://kiro.dev/changelog/cli/ |
