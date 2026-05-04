# ⚡ KICAU MANIA DETECTOR
Aplikasi web real-time yang mendeteksi pose "punggung tangan menutup mulut" langsung di browser — tanpa backend, tanpa install apapun selain menjalankannya lewat local server.
Saat pose terdeteksi, aplikasi masuk ke **Kicau Mania Mode**: audio otomatis play, animasi muncul di tepi layar, dan banner glitch "⚡KICAU MANIA!⚡" menyala di tengah layar. Semua mati otomatis begitu tangan menjauh dari mulut.

## Cara Kerja
Kamera menangkap feed video secara langsung. Dua model machine learning dari MediaPipe berjalan bersamaan di browser — **MediaPipe Hands** untuk melacak 21 landmark tangan, dan **MediaPipe Face Mesh** untuk melacak 468 titik wajah.
Setiap frame, sistem mengambil dua titik spesifik:
- **Face Mesh landmark #13** — titik bibir atas
- **Hand landmark #9** — punggung tangan (dorsum)
Lalu menghitung jarak antara keduanya menggunakan rumus **Euclidean 3D**:

```
jarak = √( (x₁-x₂)² + (y₁-y₂)² + (z₁-z₂)² )
```

Koordinat yang dipakai sudah normalized (0 sampai 1) oleh MediaPipe, jadi nilainya konsisten terlepas dari resolusi kamera. Kalau hasil jarak di bawah threshold **0.12**, sistem menganggap punggung tangan sedang menutup mulut dan Kicau Mania Mode aktif.

Untuk mencegah audio spam, sistem memakai state `sedangMainAudio` — `audio.play()` hanya dipanggil sekali saat mode pertama kali aktif, dan `audio.pause()` dipanggil saat pose hilang.
