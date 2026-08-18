# 📖 Sei Extensions (靜)

<div align="center">

[![CI](https://github.com/wdinrev/sei-source/actions/workflows/build_push.yml/badge.svg)](https://github.com/wdinrev/sei-source/actions/workflows/build_push.yml)

**Repository Ekstensi Manga / Manhwa / Doujin Khusus Sumber Indonesia untuk Mihon, Tachiyomi, Komikku, & Dantotsu.**

</div>

---

## 📥 Cara Menambahkan Repo

Di aplikasi Anda (Mihon / Tachiyomi / Komikku / Dantotsu), masuk ke:
**Settings > Browse > Extension repositories > Add Repository**

Salin dan tempel URL berikut:
```text
https://raw.githubusercontent.com/wdinrev/sei-repo/main/index.min.json
```

*(Atau jika aplikasi Anda menggunakan format Protobuf modern: `https://raw.githubusercontent.com/wdinrev/sei-repo/main/index.pb`)*

---

## 🧩 Sumber yang Tersedia (Indonesian Sources)

Katalog lengkap berisi 80+ sumber komik, manga, manhwa, dan doujin berbahasa Indonesia (KomikIndo, Kiryuu, WestManga, Mangaku, Komiku, Komikcast, Doujindesu, dll.).

---

## 🛠️ Pengembangan & Kontribusi

1. Pastikan terinstall JDK 17 dan Android SDK.
2. Buat ekstensi baru di folder `src/id/<nama-sumber>`.
3. Jalankan build lokal:
   ```bash
   ./gradlew :src:id:<nama-sumber>:assembleDebug
   ```

---

## 📜 Lisensi
Kode ekstensi dilisensikan di bawah lisensi [Apache 2.0](LICENSE).
Semua konten gambar komik disediakan langsung oleh situs pihak ketiga dan tidak berafiliasi dengan repositori ini.
