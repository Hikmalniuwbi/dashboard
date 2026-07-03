# 👨‍🏫 BEDAH KODE: PENJELASAN 

Dokumen ini berisi rangkuman "rahasia" di balik baris-baris kode dasbor Robot Soccer. Saat peserta sudah selesai melakukan *Copy-Paste* dan melihat *Dashboard* mereka menyala, ajak mereka membedah rahasia logika tersebut satu per satu:

---

### 1. Rahasia `app/page.js` (Otak & Nyawa Sistem)
Pusat kendali seluruh dasbor ada di file `page.js`. Coba perhatikan dua hal ini:
* **`useState` (Memori Jangka Pendek)**: Ini adalah sel memori. Komputer harus mengingat angka persentase baterai saat ini. Kehebatan `useState` di React adalah: tiap kali angkanya kita ubah (misal baterai dari 100 jadi 99), layar akan **otomatis mengganti tampilannya** tanpa perlu kita klik tombol *Refresh* (F5).
* **`useEffect` & `setInterval` (Detak Jantung)**: Karena saat ini dasbor belum terkoneksi ke robot asli, kita harus membuat "robot simulasi" di dalam laptop kita. Kode `setInterval` bertugas layaknya nyawa tak terlihat yang *diam-diam* memotong baterai sebanyak `-1%` setiap `5000` milidetik (5 detik). Inilah yang membuat UI kita seolah-olah hidup secara *realtime*!

---

### 2. Rahasia `TelemetryPanel.jsx` (UI yang Cerdas)
Di dalam komponen ini, bagaimana caranya tulisan bisa berganti Hijau saat online dan Abu-abu saat offline?
* **Ternary Operator (`? :`)**: Lihat kode `${connected ? "bg-emerald-500" : "bg-zinc-500"}`. Ini adalah cara komputer mengambil keputusan cepat.
Layar bertanya: *"Apakah statusnya connected (true)?"*
  * Jika **Ya**, suntikkan warna Hijau (`emerald`).
  * Jika **Tidak**, suntikkan warna Abu-abu (`zinc`).
Jadi, programmer tidak perlu membuat 2 komponen yang berbeda, cukup 1 komponen yang bisa berubah warna dengan pintar bergantung pada data aslinya!

---

### 3. Rahasia `ActionButtons.jsx` (Ilusi Fisik)
Saat kalian menekan tombol "KICK", mengapa rasanya seperti menekan tombol mesin ding-dong betulan?
* **Jeda Waktu (`setTimeout`)**: Ketika diklik, tombol akan diperintahkan untuk berubah warna menjadi sangat terang, lalu kita menyalakan *timer* (waktu mundur) diam-diam. Dalam **200 milidetik**, warna tombol dipaksa kembali padam atau kembali normal. 
Perubahan warna yang terjadi secepat kilat ini menciptakan "Ilusi Visual" di mata manusia bahwa tombol web di layar itu punya sensasi fisik (*mentul*).

---

### 4. Rahasia `Joystick.jsx` (Matematika Analog & IoT)
Ini adalah komponen paling jenius di dalam proyek ini. Ada dua batasan fisika yang terjadi di sini:
* **Rumus Pythagoras (`Math.sqrt`)**: Kenapa saat kalian menarik tongkat analog pakai mouse sampai mentok ke monitor, tongkatnya tidak copot keluar dari lingkaran analog?
Jawabannya karena kita menggunakan rumus Pythagoras ($a^2 + b^2 = c^2$). Kode akan menghitung seberapa jauh tarikan kursor Anda dari titik tengah. Jika jaraknya melebihi `60 pixel`, sistem akan menahannya secara paksa tepat di batas 60 pixel. Keren, kan?
* **Anti-Spam IoT (`Date.now() - lastLogTime > 150`)**: Sensor kursor itu super sensitif. Jika kalian menggerakkan kursor 1 milimeter saja, dia bisa merekam ribuan data koordinat. Jika data sebanyak itu langsung dikirim mentah-mentah ke WiFi Robot, mesin / mikrokontroler robot akan *crash* atau *lag* parah.
Oleh karena itu, ada logika "Satpam" (*Throttle*) di mana perintah pergerakan analog **hanya boleh dikirim maksimal 1 kali dalam 0.15 detik (150ms)**. Kecepatan transmisi ini sudah cukup akurat untuk mengontrol pergerakan mobil, namun sangat aman dari risiko membuat modul WiFi nge-hang.

---

### 5. Rahasia `ScoreBoard.jsx` (Papan Skor Digital)
* **`padStart(2, "0")`**: Ini adalah trik pemrograman rahasia untuk papan skor digital. Trik ini memaksa angka berapapun nilainya untuk harus memiliki dua digit teks. Jika sebuah tim baru mencetak skor `1`, sistem akan menempelkan angka nol di depannya menjadi `01`.

---

**💡 Tips Presentasi (Tinkering Challenge):** 
Biar pesertanya makin sadar fungsi kode tersebut, suruh mereka mengotak-atik angkanya! 
*"Coba ganti angka Throttle `150` di Joystick jadi `2000` (2 detik), lalu coba tarik tongkat analognya ke depan! Pasti nanti pergerakan angkanya di layar log terminal menjadi sangat patah-patah (hanya update tiap 2 detik)!"* 

Hal eksperimental ini dijamin akan membuat mereka mengerti seberapa penting pembatas waktu (throttle) di dalam perancangan produk hardware (IoT).
