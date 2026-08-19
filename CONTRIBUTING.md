# ☄️ Panduan Kontribusi - Sei Extensions (靜)

Terima kasih atas minat Anda untuk berkontribusi pada Sei. Repositori ini khusus merawat dan mengembangkan ekstensi manga, manhwa, manhua, dan komik berbahasa Indonesia untuk Mihon, Tachiyomi, Komikku, dan Dantotsu.

---

## Daftar Isi
1. [Prasyarat](#prasyarat)
2. [Struktur Repositori](#struktur-repositori)
3. [Menulis Ekstensi Baru](#menulis-ekstensi-baru)
   - [Struktur Folder](#1-struktur-folder)
   - [Konfigurasi build.gradle.kts](#2-konfigurasi-buildgradlekts)
   - [Kelas Utama (ParsedHttpSource)](#3-kelas-utama-parsedhttpsource)
   - [Multi-Source Themes (lib-multisrc)](#4-multi-source-themes-lib-multisrc)
4. [Penanganan Konten Dewasa (NSFW)](#penanganan-konten-dewasa-nsfw)
5. [Testing & Build Lokal](#testing--build-lokal)
6. [Aturan Versi](#aturan-versi)
7. [Membuat Pull Request](#membuat-pull-request)

---

## Prasyarat
Sebelum mulai mengembangkan ekstensi, pastikan Anda telah menginstal:
* Java Development Kit (JDK) 17 (Temurin / OpenJDK 17).
* Android SDK (API Level 34+).
* Android Studio atau VS Code dengan ekstensi Kotlin & Gradle.
* Git.

---

## Struktur Repositori

```text
sei-source/
├── lib/               # Library utilitas bersama (keiyoushi.utils, cryptoaes, dll.)
├── lib-multisrc/      # Tema scraper bersama (MangaThemesia, Madara, WPMangaStream, dll.)
├── src/
│   └── id/            # Murni seluruh ekstensi Komik/Manga Indonesia
│       ├── komikindo/
│       ├── kiryuu/
│       ├── westmanga/
│       └── <nama-ekstensi-baru>/
└── .github/workflows/ # Pipeline CI/CD untuk otomatis build & release APK
```

---

## Menulis Ekstensi Baru

### 1. Struktur Folder
Setiap ekstensi berada di dalam direktori `src/id/<nama-sumber>/`:

```text
src/id/contoh/
├── build.gradle.kts
├── res/
│   ├── mipmap-hdpi/ic_launcher.png
│   ├── mipmap-mdpi/ic_launcher.png
│   ├── mipmap-xhdpi/ic_launcher.png
│   ├── mipmap-xxhdpi/ic_launcher.png
│   └── mipmap-xxxhdpi/ic_launcher.png
└── src/eu/kanade/tachiyomi/extension/id/contoh/
    ├── Contoh.kt
    └── Filters.kt   (opsional)
```

### 2. Konfigurasi build.gradle.kts
Contoh konfigurasi standar Kotlin DSL:

```kotlin
plugins {
    alias(kei.plugins.extension)
}

ext {
    name = "ContohKomik"
    pkgName = "contoh"
    extClass = ".ContohKomik"
    extVersionCode = 1
    isNsfw = false // ubah ke true jika berisi konten 18+
}
```

### 3. Kelas Utama (ParsedHttpSource / HttpSource)
Implementasi dasar scraper manga:

```kotlin
package eu.kanade.tachiyomi.extension.id.contoh

import eu.kanade.tachiyomi.source.model.FilterList
import eu.kanade.tachiyomi.source.model.MangasPage
import eu.kanade.tachiyomi.source.model.Page
import eu.kanade.tachiyomi.source.model.SChapter
import eu.kanade.tachiyomi.source.model.SManga
import eu.kanade.tachiyomi.source.online.ParsedHttpSource
import okhttp3.Request
import org.jsoup.nodes.Document
import org.jsoup.nodes.Element

class ContohKomik : ParsedHttpSource() {
    override val name = "ContohKomik"
    override val baseUrl = "https://contohkomik.id"
    override val lang = "id"
    override val supportsLatest = true

    // 1. Popular Manga
    override fun popularMangaRequest(page: Int): Request = GET("$baseUrl/manga?page=$page&order=popular")
    override fun popularMangaFromElement(element: Element): SManga = ...
    override fun popularMangaNextPageSelector(): String? = "a.next"

    // 2. Latest Updates
    override fun latestUpdatesRequest(page: Int): Request = GET("$baseUrl/manga?page=$page&order=update")
    override fun latestUpdatesFromElement(element: Element): SManga = ...
    override fun latestUpdatesNextPageSelector(): String? = "a.next"

    // 3. Search Manga
    override fun searchMangaRequest(page: Int, query: String, filters: FilterList): Request = ...
    override fun searchMangaFromElement(element: Element): SManga = ...
    override fun searchMangaNextPageSelector(): String? = "a.next"

    // 4. Manga Details
    override fun mangaDetailsParse(document: Document): SManga = ...

    // 5. Chapter List
    override fun chapterListSelector(): String = "div.chapters li"
    override fun chapterFromElement(element: Element): SChapter = ...

    // 6. Page List (Daftar Gambar Chapter)
    override fun pageListParse(document: Document): List<Page> = ...
    override fun imageUrlParse(document: Document): String = ""
}
```

### 4. Multi-Source Themes (lib-multisrc)
Banyak website komik Indonesia menggunakan template WordPress yang sama (misalnya tema MangaThemesia, Madara, atau FTSys). 
Jika website target menggunakan salah satu tema tersebut, cukup daftarkan ekstensi Anda di file tema terkait di dalam `lib-multisrc/<nama-tema>/` tanpa perlu menulis kode scraper HTML dari awal.

---

## Penanganan Konten Dewasa (NSFW)
Ekstensi yang menyediakan konten dewasa (18+ / Doujin / Pornhwa) wajib mencantumkan:
```kotlin
ext {
    ...
    isNsfw = true
}
```

---

## Testing & Build Lokal

Untuk menguji apakah ekstensi Anda bisa di-compile tanpa error:

```bash
# Build APK versi Debug
./gradlew :src:id:<nama-ekstensi>:assembleDebug

# Jalankan format & linter
./gradlew spotlessApply
```
File APK hasil build lokal akan berada di:
`src/id/<nama-ekstensi>/build/outputs/apk/debug/<nama-ekstensi>-debug.apk`

Anda dapat menginstal file APK tersebut langsung ke HP atau Emulator Android untuk menguji bacaan chapter di aplikasi Mihon, Komikku, Tachiyomi, atau Dantotsu.

---

## Aturan Versi

Ketika Anda melakukan perbaikan atau pembaruan domain:
1. Wajib menaikkan `extVersionCode` sebanyak +1 pada `build.gradle.kts` ekstensi terkait.
2. Mihon / Tachiyomi akan mendeteksi perubahan ini dan menampilkan tombol Update kepada pengguna.

---

## Membuat Pull Request

1. Buat branch baru dari `main`:
   ```bash
   git checkout -b feat/tambah-sumber-xyz
   ```
2. Lakukan commit dengan pesan yang jelas (mengikuti Conventional Commits):
   * `feat(id/xyz): add new XYZ manga source`
   * `fix(id/xyz): update baseUrl and fix image extraction`
3. Pastikan `./gradlew spotlessApply` sudah dijalankan sebelum commit.
4. Buka Pull Request ke repository `wdinrev/sei-source` branch `main`.
5. CI akan otomatis memverifikasi build ekstensi Anda.
