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
**🔎 Bedah Kode UI:** 
* `connected ? "bg-emerald" : "bg-zinc"` adalah cara komputer berpikir: *Jika tersambung warnai Hijau, jika putus warnai Abu-abu.*

### 2. File `ScoreBoard.jsx` (Papan Skor)
```javascript
"use client";
import React from "react";

export default function ScoreBoard({ scoreA, scoreB }) {
  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-6 flex flex-col items-center">
      <h2 className="text-zinc-400 text-sm font-semibold uppercase tracking-[0.2em] mb-4">Match Score</h2>
      <div className="flex gap-8 w-full">
        <div className="flex flex-col items-center">
          <span className="text-blue-500 font-bold mb-2 text-xs uppercase">Team A</span>
          <div className="text-5xl font-black text-blue-100 bg-zinc-950 p-4 rounded-lg border-2 border-blue-600/50 font-mono">
            {scoreA.toString().padStart(2, "0")}
          </div>
        </div>
        <div className="text-3xl font-black text-zinc-600 mb-2">:</div>
        <div className="flex flex-col items-center">
          <span className="text-red-500 font-bold mb-2 text-xs uppercase">Team B</span>
          <div className="text-5xl font-black text-red-100 bg-zinc-950 p-4 rounded-lg border-2 border-red-600/50 font-mono">
            {scoreB.toString().padStart(2, "0")}
          </div>
        </div>
      </div>
    </div>
  );
}
```
**🔎 Bedah Kode Padding:** 
* `padStart(2, "0")` memaksa angka tunggal (seperti `1`) memiliki nol di depannya menjadi `01`. Sangat identik dengan papan skor digital asli.

### 3. File `ActionButtons.jsx` (Tombol Aksi Ilusi)
```javascript
"use client";
import React, { useEffect, useState } from "react";
import { Zap } from "lucide-react";

export default function ActionButtons({ onCommand }) {
  const [activeAction, setActiveAction] = useState(null);

  const handleAction = () => {
    setActiveAction("kick");
    onCommand(`ACTION_KICK`);
    setTimeout(() => { setActiveAction(null); }, 200); // Padamkan dalam 200 milidetik
  };

  useEffect(() => {
    const handleKeyDown = (e) => {
      if (e.repeat) return; // Anti-spam keyboard ditahan
      if (e.code === "Space") { e.preventDefault(); handleAction(); }
    };
    window.addEventListener("keydown", handleKeyDown);
    return () => window.removeEventListener("keydown", handleKeyDown);
  }, []);

  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-8 flex flex-col justify-center items-center h-full min-h-[300px]">
      <h2 className="absolute top-6 left-6 text-zinc-500 text-sm font-semibold uppercase tracking-[0.2em]">Action</h2>
      <button onClick={handleAction}
        className={`relative w-40 h-40 rounded-full border-[6px] flex flex-col justify-center items-center gap-2 transition-transform duration-100 ${activeAction === "kick" ? "bg-amber-600 border-amber-700 scale-95" : "bg-amber-500 border-amber-600"}`}>
        <Zap className="w-12 h-12 text-amber-200" />
        <span className="font-black text-lg text-amber-200">KICK</span>
      </button>
    </div>
  );
}
```
**🔎 Bedah Kode Ilusi:** 
* `setTimeout` digunakan untuk menyalakan tombol menjadi terang sebentar, lalu memadamkannya kembali ke warna normal dalam `200` milidetik, membuat ilusi layaknya menekan tombol fisik nyata.

### 4. File `Joystick.jsx` (Matematika & Anti-Spam Analog)
```javascript
"use client";
import React, { useState, useRef, useCallback } from "react";

export default function Joystick({ onCommand }) {
  const baseRef = useRef(null);
  const [position, setPosition] = useState({ x: 0, y: 0 }); 
  const [isDragging, setIsDragging] = useState(false);
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

  const updatePos = (e) => {
    if (!baseRef.current) return;
    const r = baseRef.current.getBoundingClientRect();
    handleMove(e.clientX - (r.left + r.width/2), e.clientY - (r.top + r.height/2));
  };

  return (
    <div className="bg-zinc-900 border border-zinc-800 rounded-xl p-8 flex flex-col items-center justify-center relative h-full min-h-[300px]">
      <h2 className="absolute top-6 left-6 text-zinc-500 text-sm font-semibold uppercase tracking-[0.2em]">Analog</h2>
      <div ref={baseRef} className="relative w-40 h-40 rounded-full bg-slate-900 border-[3px] border-slate-700 flex justify-center items-center mt-6 touch-none"
        onPointerDown={(e) => { setIsDragging(true); updatePos(e); }} 
        onPointerMove={(e) => { if (isDragging) updatePos(e); }} 
        onPointerUp={() => { setIsDragging(false); setPosition({x:0, y:0}); onCommand('STOP'); }} 
        onPointerLeave={() => { setIsDragging(false); setPosition({x:0, y:0}); onCommand('STOP'); }}>
        <div className="absolute w-16 h-16 rounded-full bg-blue-500 border-b-4 border-blue-600 shadow-lg" style={{ transform: `translate(${position.x}px, ${position.y}px)` }}></div>
      </div>
    </div>
  );
}
```
**🔎 Bedah Kode Matematika:** 
* **`Math.sqrt()`**: Rumus Phytagoras digunakan agar tuas analog tidak akan pernah keluar melompat dari batas radius lingkarannya sebesar `60` pixel sekuat apapun mouse Anda menariknya.
* **`lastLogTime > 150`**: Sistem pembatas sinyal (*Throttle*). Gerakan secepat kilat mouse kalian akan ditahan, dan perintah hanya dikirim maksimal **1 kali tiap 0.15 detik** agar koneksi Robot WiFi IoT tidak jebol (spam).

### 5. File `LogConsole.jsx` (Layar Terminal)
```javascript
"use client";
import React from "react";
export default function LogConsole({ logs }) {
  return (
    <div className="bg-zinc-950 border border-zinc-800 rounded-lg p-3 h-full flex flex-col font-mono text-[10px]">
      <div className="text-zinc-500 mb-2 border-b border-zinc-800 pb-1">SYSTEM_LOGS</div>
      <div className="flex-1 overflow-y-auto space-y-1 flex flex-col justify-end">
        {logs.map((log, i) => (
          <div key={i} className="text-zinc-400">
            <span className="text-zinc-600">[{log.timestamp}]</span> <span className="text-amber-500">{log.message}</span>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 🏗️ FASE 3: SATUKAN DI HALAMAN UTAMA (10 Menit)
Buka file **`app/page.js`**, **hapus semua isinya**, ganti dengan ini:

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

  const addLog = useCallback((message) => {
    const timestamp = new Date().toLocaleTimeString("id-ID", { hour12: false });
    setLogs((prev) => [...prev, { timestamp, message }].slice(-10)); 
  }, []);

  // 2. EFFECT (DETAK JANTUNG SISTEM)
  useEffect(() => {
    const interval = setInterval(() => {
      setTelemetry((prev) => ({
        ...prev,
        battery: Math.max(0, prev.battery - 1), // Baterai Berkurang Tiap 5 Detik
      }));
    }, 5000);
    return () => clearInterval(interval);
  }, []);

  return (
    <div className="relative w-screen h-screen bg-zinc-950 text-zinc-100 p-6 flex flex-col font-sans overflow-hidden">
      
      {/* 3. LAYOUT KEPALA TATA LETAK */}
      <div className="flex justify-between items-start w-full relative">
        <div className="w-[400px]"><TelemetryPanel telemetry={telemetry} /></div>
        <div className="absolute left-1/2 -translate-x-1/2 top-0"><ScoreBoard scoreA={2} scoreB={1} /></div>
        <div className="w-[300px] h-[150px]"><LogConsole logs={logs} /></div>
      </div>

      <div className="flex-1"></div>

      {/* 4. LAYOUT KONTROLER BAWAH */}
      <div className="flex justify-between items-end w-full pb-8 px-12">
        <Joystick onCommand={addLog} />
        <ActionButtons onCommand={addLog} />
      </div>

    </div>
  );
}
```
**🔎 Bedah Kode Utama:** 
* `useState` bertindak seperti sel memori. Jika kita ubah angkanya, UI otomatis membarui layarnya sendiri!
* `useEffect` bersama `setInterval` bekerja sebagai nyawa tak terlihat yang diam-diam mengurangi nilai baterai kita sebesar `-1` setiap `5000` ms (5 detik) untuk memberikan ilusi bahwa robot hidup secara *realtime*.

---

## 🏃‍♂️ FASE 4: JALANKAN & TINKERING (Demo!)
Buka Terminal dan jalankan:
```bash
npm run dev
```
Buka browser di **`http://localhost:3000`**.

🚨 **TANTANGAN SPEED SPRINT (TINKERING):**
Sekarang, rubah kode-kode tersebut secara liar!
1. Ubah warna Papan Skor Tim B dari `red` ke `purple`.
2. Ubah kecepatan detak baterai di `page.js` dari `5000` (5 detik) menjadi super cepat (misal `500` ms).
3. Presentasikan ke depan apa yang terjadi!
