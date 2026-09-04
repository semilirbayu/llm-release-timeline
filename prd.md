# PRD: LLM Release Timeline

**Status:** Draft v0.3 · 2026-09-04 — M1+M2 selesai (`models.json` 85 node + positioning/hype); M3 (review manusia) menunggu kamu
**Owner:** kamu (editorial) + agent (drafting & build)

---

## 1. Ringkasan

Satu halaman web statis berisi **timeline rilis model LLM** dari ~2018 hingga hari ini, dengan fokus pada era pasca-ChatGPT (Nov 2022→). Setiap node model menampilkan **satu kalimat positioning editorial**: kenapa model itu penting, apa yang ia gugur, atau kenapa hype-nya gagap. Timeline disusun sebagai swimlane per lab sehingga dinamika "siapa membalas siapa" terlihat sekilas.

**One-liner:** *Bukan datasheet. Riwayat persaingan LLM dalam satu halaman.*

## 2. Masalah & Motivasi

- Informasi rilis LLM tersebar di puluhan berita, changelog, benchmark leaderboard; tidak ada narasi kronologis.
- Tabel benchmark (LMArena, HELM) kehilangan konteks: angka tinggi ≠ momen penting. Node "DeepSeek R1 → Nvidia -$600B" tidak ada di leaderboard mana pun.
- Audiens: engineer, PM, investor yang butuh *mental map* industri, bukan spesifikasi API.

## 3. Tujuan & Non-Tujuan

**Tujuan**
1. Membacakan kronologi lengkap era modern (2022→) dalam <10 menit.
2. Setiap node punya kalimat positioning yang dapat dipertanggungjawabkan, bukan marketing copy.
3. Satu halaman statis: buka URL → langsung terbaca, tanpa backend, tanpa login, tanpa scroll yang membingungkan.

**Non-Tujuan**
- Live leaderboard benchmark / angka yang selalu segar.
- Meliputi varian minor (refresh, point-release, `-latest`).
- Komprensif pra-2022 (era BC diringkas 1–2 node).
- Tidak ada kolaborasi multi-user / CMS. Konten = satu file JSON yang di-commit.

## 4. Keputusan Scope (terkunci sesuai diskusi)

| Keputusan | Nilai | Alasan |
|---|---|---|
| Cakupan model | Flagship per lab, ~60 node era modern (riset M1: 63 flagship + 22 cadangan `mid`) | Cadence 2026 ternyata ~3x lipat 2023; kepadatan cerita > kelengkapan dataset |
| Lane lab | `openai`, `google`, `anthropic`, `meta`, `mistral`, `xai`, `deepseek`, `alibaba`, `others` | Satu lane per lab utama; lane "Others" menampung sisanya tanpa pecahan lane |
| Rentang waktu | 2018–sekarang; 2018–2022 ringkas | Era modern adalah inti minat |
| Layout | Swimlane horizontal per lab + band era | Menunjukkan perlombaan antar lab |
| Sumber narasi | AI membuat draf `positioning`, manusia mengedit | Skala 30+ node; kamu tetap editor akhir |
| Bahasa | **EN** (final) | Dikunci 2026-09-04; audiens global, ID bisa menyusul sebagai lapisan terjemahan |
| Stack | Satu HTML + vanilla JS + `models.json`, statis | Tanpa dependensi; dapat di-hosting di mana saja; mudah di-maintain |

## 5. Model Data

```jsonc
// models.json — satu array, diurutkan berdasarkan tanggal
{
  "id": "deepseek-r1",
  "org": "deepseek",           // kunci lane; daftar org tetap di konstanta
  "name": "DeepSeek R1",
  "date": "2025-01-20",        // tanggal rilis publik (bukan paper, bukan preview)
  "tier": "flagship",          // flagship | mid   (mid = cadangan, MVP hanya flagship)
  "weights": "open",           // open | closed
  "tags": ["reasoning", "price-disrupt"],
  "stats": {                   // boleh sebagian null — tidak semua angka terlacak
    "context": "128k",
    "mmlu": null,
    "pricePerMTok": { "in": 0.55, "out": 2.19 }  // USD, harga API saat rilis
  },
  "positioning": "Pertama kali reasoning open-weights masuk kelas top…",   // 1–2 kalimat, WAJIB
  "hype": "Hari rilis: Nvidia turun ~17%, kejatuhan kapitalisasi $600B…",  // 0–1 kalimat, opsional
  "refs": ["https://…"]        // minimal 1 sumber per node; positioning tanpa sumber ditandai
}
```

**Aturan konten**
- `positioning` selalu *komparatif vs model sebelumnya* atau *momen pertama X*. Pola yang disetujui: "pertama yang…", "menggusur…", "menjatuhkan harga…", "gagal karena…".
- Node tanpa cerita → jangan dimasukkan. Node = acara, bukan rilis.
- Setiap klaim memiliki `refs`. Kamu menandai klaim yang meragukan saat melakukan review draf.

## 6. UI Spec

**Tampilan default (atas ke bawah)**
1. **Judul + ringkasan** satu kalimat + legenda lane.
2. **Stat strip**: jumlah node, rentang tanggal, "hari rata-rata antar flagship".
3. **Timeline swimlane** — horizontal scroll, snap per tahun; tiap node = titik, lebar tergantung jumlah model per periode.
4. **Band era** di belakang lane: *Pre-ChatGPT* → *ChatGPT boom* → *Multimodal war* → *Reasoning & agent era*.
5. **Filter bar** (sticky): toggle per lab, toggle open-weights, dan cari nama model.

**Interaksi**
- Hover node → kartu kecil: nama, tanggal, 1 kalimat positioning, satu angka benchmark.
- Klik node → panel samping: detail lengkap + hype + refs + perbandingan delta vs pendahulu langsung di lane yang sama.
- Node pra-2022 diringkas menjadi satu node "BC era" yang dapat diperluas.
- Mobile: swimlane berubah menjadi spine kronologis vertikal (media query); konten kartu tetap sama.

**Prinsip visual**: satu warna per lab, monokrom selain itu; angka benchmark hanya muncul saat hover (tidak menjadi kekacauan visual utama).

## 7. Milestone

| Fase | Isi | Output |
|---|---|---|
| M1 | Susun daftar node: ~30–40 flagship + tanggal + refs (riset) | `models.json` mentah, tanpa positioning |
| M2 | Membuat draf positioning + hype per node (AI) | `models.json` lengkap, siap direview |
| M3 | Editing manusia: tandai klaim, rapikan kalimat | v1 konten terkunci |
| M4 | Build halaman: swimlane, hover/klik, band era, filter | Halaman yang bisa dijalankan lokal |
| M5 | Mobile spine, polish visual, hosting | URL publik |

M1–M2 bisa jalan paralel sebagian (riset tanggal bisa sekaligus mengisi draf kasar); M4 tidak menunggu M3 selesai — bisa dibangun dengan data parsial.

## 8. Risiko

- **Akurasi tanggal**: tanggal pengumuman vs tanggal ketersediaan API sering berbeda beberapa hari → patok konvensi: *tanggal pertama publik bisa diakses*; catat pengecualiannya di refs.
- **Klaim positioning bias**: draf AI cenderung memberikan pujian pada semua pihak → aturan penyuntingan: setiap kalimat harus bisa dijawab "menurut siapa?"; jika tidak, ganti dengan fakta (benchmark, harga, kejadian).
- **Kadaraluasaan**: industri bergerak cepat → pembaruan = menambah satu objek JSON + satu commit; halaman tidak butuh perawatan struktural.
- **Scope creep benchmark**: hanya masukkan angka di *waktu rilis*, jangan bandingkan angka antar generasi (metodologi berubah).

## 9. Definisi Selesai (MVP)

- ≥30 node era modern, masing-masing dengan `positioning` + `refs` yang telah direview.
- Swimlane + band era + hover/klik + filter per lab, berfungsi di desktop & mobile.
- Hosting statis dengan URL publik.

---

**Keputusan terkunci (2026-09-04)**
1. Bahasa: **EN final**.
2. Lane tetap: OpenAI, Google, Anthropic, Meta, Mistral, xAI, DeepSeek, Alibaba (Qwen); sisanya (Cohere, AI21, Moonshot, Zhipu, dll.) masuk lane **"Others"**.
3. M1 dieksekusi setelah keputusan ini; output: `models.json` mentah — tanggal + refs, tanpa positioning.
