# 🚀 SPEED SPRINT: DASHBOARD ROBOT SOCCER

Buku panduan ini dirancang untuk format **Speed Sprint (1 Kali Pertemuan)**. Mengingat waktu yang sangat singkat, peserta akan melompati proses pembuatan kode dari nol, langsung masuk ke dalam proses *Setup*, *Copy-Paste Component*, dan *Tinkering* (bedah logika).

Fokus utama hari ini: **Setup Cepat, Rakit Komponen, Eksperimen Logika, lalu Demo!**

---

## ⏱️ FASE 1: SETUP KILAT (15 Menit)

Buka Terminal / Command Prompt di laptop kalian, lalu jalankan perintah ini secara berurutan:

**1. Membuat Proyek Next.js Baru**
```bash
npx create-next-app@latest robot-dashboard
```
*(Pilih **No** untuk TypeScript, **Yes** untuk ESLint & Tailwind, **No** untuk src/, **Yes** untuk App Router).*

**2. Masuk Folder & Install Ikon**
```bash
cd robot-dashboard
npm install lucide-react
```

**3. Buka di VS Code**
Buat folder baru bernama **`components`** di dalam folder `robot-dashboard` (sejajar dengan folder `app`).

**4. Izin Akses IP Jaringan (Untuk HP)**
Buat satu file baru bernama **`next.config.mjs`** di paling luar folder (sejajar dengan `package.json`), lalu isi dengan:
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Masukkan IP Address Laptop kalian di sini agar HP tidak diblokir saat test!
  allowedDevOrigins: ['*'], 
};
export default nextConfig;
```

---

## 🧩 FASE 2: RAKIT KOMPONEN (30 Menit)
Buat file-file di bawah ini di dalam folder `components/` lalu *copy-paste* kodenya!

### 1. File `TelemetryPanel.jsx` (Indikator Status Pintar)
```javascript
"use client";
import React from "react";
import { Battery, Activity, Wifi, Thermometer } from "lucide-react";

export default function TelemetryPanel({ telemetry }) {
  const { battery, ping, temperature, connected } = telemetry;
  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-4 w-full">
      <div className="flex items-center justify-between mb-3 border-b border-zinc-800 pb-2">
        <h2 className="text-zinc-500 text-xs font-bold uppercase tracking-[0.2em]">Telemetry</h2>
        <div className="flex items-center gap-2">
          {/* TERNARY OPERATOR: Ubah warna dinamis */}
          <div className={`w-2 h-2 rounded-full ${connected ? "bg-emerald-500" : "bg-zinc-500"}`}></div>
          <span className={`text-[10px] font-bold uppercase ${connected ? "text-emerald-500" : "text-zinc-500"}`}>
            {connected ? "Online" : "Offline"}
          </span>
        </div>
      </div>
      <div className="grid grid-cols-2 gap-3">
        <TelemetryCard icon={<Battery className={`w-4 h-4 ${battery > 20 ? "text-emerald-500" : "text-red-500"}`} />} label="BATT" value={`${battery}%`} />
        <TelemetryCard icon={<Wifi className="w-4 h-4 text-blue-500" />} label="PING" value={`${ping}ms`} />
        <TelemetryCard icon={<Thermometer className={`w-4 h-4 ${temperature > 70 ? "text-orange-500" : "text-amber-500"}`} />} label="TEMP" value={`${temperature}°C`} />
        <TelemetryCard icon={<Activity className="w-4 h-4 text-purple-500" />} label="LOAD" value={`${Math.max(10, Math.round(100 - battery / 2))}%`} />
      </div>
    </div>
  );
}

function TelemetryCard({ icon, label, value }) {
  return (
    <div className="flex flex-col p-3 bg-zinc-950 rounded-lg border border-zinc-800">
      <div className="flex items-center gap-2 mb-1">{icon} <span className="text-[10px] text-zinc-500 font-bold">{label}</span></div>
      <div className="text-lg font-bold font-mono text-zinc-300">{value}</div>
    </div>
  );
}
```

### 2. File `ScoreBoard.jsx` (Papan Skor)
```javascript
"use client";
import React from "react";

export default function ScoreBoard({ scoreA, scoreB }) {
  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-4 sm:p-6 flex flex-col items-center">
      <h2 className="text-zinc-400 text-xs sm:text-sm font-semibold uppercase tracking-[0.2em] mb-2 sm:mb-4">Match Score</h2>
      <div className="flex gap-4 sm:gap-8 w-full items-center">
        <div className="flex flex-col items-center">
          <span className="text-blue-500 font-bold mb-1 text-[10px] sm:text-xs uppercase">Team A</span>
          <div className="text-3xl sm:text-5xl font-black text-blue-100 bg-zinc-950 p-2 sm:p-4 rounded-lg border-2 border-blue-600/50 font-mono">
            {scoreA.toString().padStart(2, "0")}
          </div>
        </div>
        <div className="text-xl sm:text-3xl font-black text-zinc-600">:</div>
        <div className="flex flex-col items-center">
          <span className="text-red-500 font-bold mb-1 text-[10px] sm:text-xs uppercase">Team B</span>
          <div className="text-3xl sm:text-5xl font-black text-red-100 bg-zinc-950 p-2 sm:p-4 rounded-lg border-2 border-red-600/50 font-mono">
            {scoreB.toString().padStart(2, "0")}
          </div>
        </div>
      </div>
    </div>
  );
}
```

### 3. File `ActionButtons.jsx` (Tombol 0-Latency Mobile)
```javascript
"use client";
import React, { useState } from "react";
import { Zap } from "lucide-react";

export default function ActionButtons({ onCommand }) {
  const [activeAction, setActiveAction] = useState(null);

  const handleAction = () => {
    setActiveAction("kick");
    onCommand(`ACTION_KICK`);
    setTimeout(() => { setActiveAction(null); }, 200); // Padamkan dalam 200 milidetik
  };

  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-4 flex flex-col justify-center items-center relative h-full max-h-[250px] sm:max-h-[300px] w-full max-w-[200px]">
      <h2 className="absolute top-4 left-4 text-zinc-500 text-sm font-semibold uppercase tracking-[0.2em]">Action</h2>
      
      {/* Menggunakan onTouchStart agar merespon 0 milidetik di HP, bypass delay klik browser */}
      <button 
        onTouchStart={(e) => { e.preventDefault(); handleAction(); }}
        onMouseDown={(e) => { e.preventDefault(); handleAction(); }}
        className={`relative w-28 h-28 sm:w-40 sm:h-40 rounded-full border-[6px] flex flex-col items-center justify-center gap-2 transition-transform duration-100 ${activeAction === "kick" ? "bg-amber-600 border-amber-700 scale-95" : "bg-amber-500 border-amber-600 hover:bg-amber-400"}`}
        style={{ touchAction: 'none' }}
      >
        <Zap className={`w-10 h-10 pointer-events-none ${activeAction === "kick" ? "text-amber-200" : "text-amber-100"}`} />
        <span className={`font-black tracking-wider pointer-events-none text-base ${activeAction === "kick" ? "text-amber-200" : "text-amber-50"}`}>KICK</span>
      </button>
    </div>
  );
}
```

### 4. File `Joystick.jsx` (Sensor Jari Global & Anti-Spam)
```javascript
"use client";
import React, { useState, useRef, useCallback, useEffect } from "react";

export default function Joystick({ onCommand }) {
  const baseRef = useRef(null);
  const [position, setPosition] = useState({ x: 0, y: 0 }); 
  const [isDragging, setIsDragging] = useState(false);
  const draggingRef = useRef(false);
  const MAX_RADIUS = 60; const lastLogTime = useRef(0);

  const handleMove = useCallback((newX, newY) => {
    const distance = Math.sqrt(newX * newX + newY * newY); // PYTHAGORAS
    let finalX = newX; let finalY = newY;
    if (distance > MAX_RADIUS) {
      finalX = (newX / distance) * MAX_RADIUS; finalY = (newY / distance) * MAX_RADIUS;
    }
    setPosition({ x: finalX, y: finalY });

    const now = Date.now();
    if (now - lastLogTime.current > 150) { // ANTI-SPAM IOT (150ms)
      onCommand(`ANALOG [X: ${(finalX/MAX_RADIUS).toFixed(2)}, Y: ${-(finalY/MAX_RADIUS).toFixed(2)}]`);
      lastLogTime.current = now;
    }
  }, [onCommand]);

  const updatePosition = useCallback((clientX, clientY) => {
    if (!baseRef.current) return;
    const r = baseRef.current.getBoundingClientRect();
    handleMove(clientX - (r.left + r.width/2), clientY - (r.top + r.height/2));
  }, [handleMove]);

  // SENSOR GLOBAL - Mencegah "nyangkut" saat kursor/jari keluar layar
  useEffect(() => {
    const handleGlobalMove = (e) => {
      if (!draggingRef.current) return;
      if (e.touches && e.touches.length > 0) updatePosition(e.touches[0].clientX, e.touches[0].clientY);
      else updatePosition(e.clientX, e.clientY);
    };

    const handleGlobalEnd = () => {
      if (draggingRef.current) {
        draggingRef.current = false; setIsDragging(false);
        setPosition({ x: 0, y: 0 }); onCommand(`ANALOG_STOP`);
      }
    };

    window.addEventListener("mousemove", handleGlobalMove);
    window.addEventListener("mouseup", handleGlobalEnd);
    window.addEventListener("touchmove", handleGlobalMove, { passive: false });
    window.addEventListener("touchend", handleGlobalEnd);
    
    return () => {
      window.removeEventListener("mousemove", handleGlobalMove);
      window.removeEventListener("mouseup", handleGlobalEnd);
      window.removeEventListener("touchmove", handleGlobalMove);
      window.removeEventListener("touchend", handleGlobalEnd);
    };
  }, [updatePosition, onCommand]);

  const handleStart = (e) => {
    if (e.cancelable) e.preventDefault(); // Matikan scroll layar HP
    draggingRef.current = true; setIsDragging(true);
    if (e.touches && e.touches.length > 0) updatePosition(e.touches[0].clientX, e.touches[0].clientY);
    else updatePosition(e.clientX, e.clientY);
  };

  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-4 flex flex-col items-center justify-center relative h-full max-h-[250px] sm:max-h-[300px] w-full max-w-[200px]">
      <h2 className="absolute top-4 left-4 text-zinc-500 text-sm font-semibold uppercase tracking-[0.2em]">Analog</h2>
      <div ref={baseRef} className="relative w-28 h-28 sm:w-40 sm:h-40 rounded-full bg-slate-900 border-[3px] border-slate-700 flex items-center justify-center mt-6"
        style={{ touchAction: 'none' }} onTouchStart={handleStart} onMouseDown={handleStart}>
        <div className={`absolute w-12 h-12 rounded-full shadow-lg pointer-events-none ${isDragging ? 'bg-blue-400 border-blue-500 scale-95' : 'bg-blue-500 border-b-4 border-blue-600'}`} style={{ transform: `translate(${position.x}px, ${position.y}px)` }}></div>
      </div>
    </div>
  );
}
```

### 5. File `LogConsole.jsx` (Layar Terminal)
```javascript
"use client";
import React, { useEffect, useRef } from "react";
import { Terminal } from "lucide-react";

export default function LogConsole({ logs }) {
  const scrollRef = useRef(null);

  useEffect(() => {
    if (scrollRef.current) scrollRef.current.scrollTop = scrollRef.current.scrollHeight;
  }, [logs]);

  return (
    <div className="bg-zinc-950 border border-zinc-800 rounded-xl p-4 flex flex-col h-full font-mono relative overflow-hidden group">
      <div className="flex items-center gap-2 mb-4 border-b border-zinc-800 pb-3">
        <Terminal className="w-5 h-5 text-zinc-500" />
        <h2 className="text-zinc-400 text-sm font-semibold uppercase tracking-wider">System Logs</h2>
      </div>
      <div ref={scrollRef} className="flex-1 overflow-y-auto text-xs space-y-1.5 pr-2">
        {logs.map((log, idx) => (
          <div key={idx} className="flex gap-3">
            <span className="text-zinc-500 shrink-0">[{log.timestamp}]</span>
            <span className={log.type === "error" ? "text-red-400" : log.type === "action" ? "text-blue-400" : "text-emerald-400"}>
              {log.message}
            </span>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 🏗️ FASE 3: SATUKAN DI HALAMAN UTAMA (10 Menit)
Buka file **`app/page.js`**, **hapus semua isinya**, ganti dengan ini (Layout *Flexbox* Bebas Bug):

```javascript
"use client";
import React, { useState, useEffect, useCallback } from "react";
import Joystick from "@/components/Joystick";
import ActionButtons from "@/components/ActionButtons";
import TelemetryPanel from "@/components/TelemetryPanel";
import ScoreBoard from "@/components/ScoreBoard";
import LogConsole from "@/components/LogConsole";

export default function Dashboard() {
  const [logs, setLogs] = useState([]);
  
  // 1. STATE (MEMORI KOMPONEN)
  const [telemetry, setTelemetry] = useState({
    battery: 100, ping: 20, temperature: 45, connected: true,
  });

  const addLog = useCallback((message, type = "info") => {
    const timestamp = new Date().toLocaleTimeString("id-ID", { hour12: false });
    setLogs((prev) => [...prev, { timestamp, message, type }].slice(-10)); 
  }, []);

  // 2. EFFECT (DETAK JANTUNG SISTEM)
  useEffect(() => {
    addLog("Sistem inisialisasi... OK", "info");
    const interval = setInterval(() => {
      setTelemetry((prev) => ({
        ...prev,
        battery: Math.max(0, prev.battery - 1), // Baterai Berkurang Tiap 5 Detik
      }));
    }, 5000);
    return () => clearInterval(interval);
  }, [addLog]);

  return (
    // Memakai Flexbox untuk mengatasi Bug Koordinat Skala di Mobile Safari/Chrome
    <div className="w-screen h-screen bg-zinc-950 text-zinc-100 flex flex-col p-2 select-none overflow-hidden touch-none">
      
      {/* 3. LAYOUT HUD ATAS */}
      <div className="flex justify-between items-start w-full h-1/3">
        <div className="w-1/3 max-w-[300px]">
          <div className="w-full sm:w-[400px]"><TelemetryPanel telemetry={telemetry} /></div>
        </div>
        
        <div className="w-1/3 flex justify-center">
          <ScoreBoard scoreA={0} scoreB={0} />
        </div>
        
        {/* Log Console Tersembunyi di Layar HP yang sempit, Tinggi dikunci 180px */}
        <div className="w-1/3 max-w-[300px] hidden sm:block h-[180px]">
          <LogConsole logs={logs} />
        </div>
      </div>

      {/* 4. LAYOUT KONTROLER BAWAH */}
      <div className="flex justify-between items-end w-full h-2/3 pb-4 px-4 sm:px-12">
        <Joystick onCommand={(cmd) => addLog(`CMD: ${cmd}`, "action")} />
        <ActionButtons onCommand={(cmd) => addLog(`CMD: ${cmd}`, "action")} />
      </div>

    </div>
  );
}
```

---

## 🏃‍♂️ FASE 4: JALANKAN & TINKERING (Demo!)
Buka Terminal dan jalankan:
```bash
npm run dev
```
Buka browser di **`http://localhost:3000`** (atau akses dari IP Address Laptop di HP kalian).

🚨 **TANTANGAN SPEED SPRINT (TINKERING):**
Sekarang, rubah kode-kode tersebut secara liar!
1. Ubah warna Papan Skor Tim B dari `red` ke `purple`.
2. Ubah kecepatan detak baterai di `page.js` dari `5000` (5 detik) menjadi super cepat (misal `500` ms).
3. Presentasikan ke depan apa yang terjadi!


# 👨‍🏫 BEDAH KODE: PENJELASAN KHUSUS MOBILE

Dokumen ini berisi rangkuman "rahasia" di balik baris-baris kode dasbor Robot Soccer yang kebal terhadap semua masalah *touch-screen*. Ajak peserta membedah rahasia logika tersebut satu per satu:

---

### 1. Rahasia `app/page.js` (Otak & Layout Anti-Bug)
* **Hilangnya CSS Scale (Flexbox)**: Jika Anda melihat tutorial lama, seringkali ukuran dikecilkan menggunakan `transform: scale()`. Kenyataannya, browser HP (WebKit) sering kali **rusak / korup** saat menghitung koordinat matematika *Touch* (getBoundingClientRect) di atas wadah yang di-scale. Menggunakan layout Flexbox Murni (`flex flex-col`) memastikan posisi x dan y selalu sempurna di layar berukuran apapun.
* **`touch-none`**: Di baris teratas, kelas Tailwind ini secara ajaib memblokir semua interaksi geser bawaan browser (seperti memuat ulang halaman saat ditarik ke bawah atau *zoom in*).

---

### 2. Rahasia `Joystick.jsx` (Sensor Jari Global & Anti-Spam)
Ini adalah komponen paling jenius. Ada 3 batasan fisika yang terjadi di sini:
* **Rumus Pythagoras (`Math.sqrt`)**: Kenapa saat kalian menarik tongkat analog sekuat tenaga, tongkatnya tidak copot keluar dari lingkaran analog?
Jawabannya karena kita menggunakan rumus Pythagoras ($a^2 + b^2 = c^2$). Kode akan menghitung seberapa jauh tarikan kursor Anda dari titik tengah. Jika jaraknya melebihi radius lingkaran, sistem menahannya secara paksa di garis batas.
* **Sensor Jendela Global (`window.addEventListener`)**: Biasanya, jika jari kita keluar sedikit saja dari kotak komponen, Joystick akan *hang* atau macet karena sensornya kehilangan jejak jari kita. Dengan menempelkan mata-mata (`addEventListener`) ke **`window`** (seluruh tubuh layar), bahkan saat jempol Anda lari hingga ke pinggir kamera HP, Joystick akan tetap patuh mengikuti arah jempol tanpa terlepas!
* **Anti-Spam IoT (`Date.now() - lastLogTime > 150`)**: Sensor kursor itu super sensitif. Gerakan 1 milimeter saja merekam ribuan angka koordinat. Jika semua angka itu dikirim via WiFi ke mikrokontroler robot, mesin robot akan *crash*. Maka ada pembatas (*Throttle*) yang hanya mengizinkan pengiriman **1 kali per 0.15 detik (150ms)**.

---

### 3. Rahasia `ActionButtons.jsx` (0-ms Latency/Instan)
* **Mengalahkan Browser Delay (`onTouchStart`)**: Jika kita menggunakan `onClick` biasa, browser web di HP sengaja menahan perintah tersebut selama **300 milidetik** untuk mengecek apakah user sedang berniat melakukan "Klik Ganda untuk Zoom". Tentu 300 milidetik sangat fatal untuk bermain bola! Dengan menembak langsung melalui `onTouchStart`, tombol di HP Anda akan merespon **Tepat di milidetik yang sama** saat jari menyentuh kaca!

---

### 4. Rahasia `next.config.mjs` (Membobol Blokir Layar)
* **IP Whitelist**: Saat kita membangun aplikasi pengontrol, kita pasti menggunakan HP (IP luar) untuk meremote Laptop. Namun fitur keamanan Live-Reload Next.js akan mendeteksi HP sebagai penyusup dari jaringan asing dan diam-diam melemparkan "Layar Peringatan Transparan" (*Error Overlay*) ke HP kita, yang menyebabkan semua tombol macet (karena tertutup lapisan transparan). Baris ajaib `allowedDevOrigins: ['*']` menonaktifkan portal perlindungan itu dan membiarkan HP kita memiliki akses penuh 100%.

---

**💡 Tinkering Challenge Tambahan:** 
 
*"Coba ganti angka Throttle `150` di Joystick jadi `2000` (2 detik), lalu coba tarik tongkat analognya! Pasti pergerakan angkanya di layar log terminal menjadi patah-patah (hanya update tiap 2 detik)!"* 

Hal eksperimental ini dijamin akan membuat mereka mengerti seberapa penting pembatas waktu dalam perancangan produk hardware IoT!
