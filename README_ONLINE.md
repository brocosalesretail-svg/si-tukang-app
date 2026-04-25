# Instruksi Setup Online (GitHub, Vercel, Supabase)

Untuk menjalankan aplikasi **Si Tukang** secara online dengan database terpusat, ikuti langkah-langkah berikut:

## 1. Setup Supabase (Database)
1. Buat akun di [Supabase](https://supabase.com/).
2. Buat Project baru (misal: `si-tukang-report`).
3. Masuk ke menu **SQL Editor** dan jalankan query berikut untuk membuat tabel:

```sql
-- Tabel Laporan
create table reports (
  id bigint primary key,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null,
  date date,
  tech text,
  area text,
  vehicle text,
  s1Project text,
  s1Addr text,
  s1Dev text,
  s1Mandor text,
  s1Phone text,
  s1TukangEst text,
  s1TukangName text,
  s1TukangPhone text,
  s1Unit text,
  s1Result text,
  s1Notes text,
  photos text[], -- Array untuk URL foto
  h1Name text,
  h1Type text,
  h1Mat text,
  h1Mandor text,
  h1Phone text,
  h1Result text,
  h2Name text,
  h2Type text,
  h2Mat text,
  h2Mandor text,
  h2Phone text,
  obsMat text,
  obsBrand text,
  obsPrice text,
  dataMandor text,
  dataToko text,
  dataWorkers jsonb, -- Untuk menyimpan list tukang
  sumVisit text,
  sumContact text,
  sumDemo text,
  techNote text,
  fu1 text,
  fu2 text,
  fu3 text,
  lat float8,
  lng float8
);

-- Tabel Log Aktivitas (Sudah diperbaiki)
create table activity_logs (
  id uuid default gen_random_uuid() primary key,
  user_name text, -- Nama kolom diganti agar tidak error
  action text,
  timestamp timestamp with time zone default now()
);

-- PENTING: Jalankan ini agar aplikasi bisa baca/tulis data tanpa login email (Anonymous)
-- Izinkan Akses Publik (RLS Policies)
ALTER TABLE reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE activity_logs ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow anonymous select" ON reports FOR SELECT USING (true);
CREATE POLICY "Allow anonymous insert" ON reports FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow anonymous update" ON reports FOR UPDATE USING (true);

CREATE POLICY "Allow anonymous log insert" ON activity_logs FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow anonymous log select" ON activity_logs FOR SELECT USING (true);
```

4. Buka **Project Settings > API** dan ambil `Project URL` serta `anon public key`.
5. Buka file `ts_report.html` dan ganti nilai berikut di bagian atas script:
   - `YOUR_SUPABASE_URL`
   - `YOUR_SUPABASE_ANON_KEY`

## 2. Setup GitHub
1. Buat repository baru di GitHub (misal: `si-tukang-app`).
2. Upload file `ts_report.html` dan beri nama `index.html` agar otomatis terbaca sebagai halaman utama.
3. Commit dan Push.

## 3. Setup Vercel (Hosting)
1. Buat akun di [Vercel](https://vercel.com/).
2. Klik **Add New > Project**.
3. Import repository GitHub yang baru dibuat.
4. Klik **Deploy**.
5. Aplikasi Anda sekarang online di URL yang diberikan Vercel (misal: `si-tukang-app.vercel.app`).

### **SANGAT PENTING: Jika Sync Error (Failed to Fetch)**
Pesan `TypeError: Failed to fetch` berarti Browser Anda memblokir koneksi ke Supabase.
1. **Daftarkan Vercel di Supabase**: 
   - Dashboard Supabase > **Authentication** > **URL Configuration**.
   - **Site URL**: `https://si-tukang-app.vercel.app`
   - **Redirect URLs**: `https://si-tukang-app.vercel.app/**`
2. **DNS/Firewall**: Jika di Laptop tetap error, coba gunakan **Hotspot HP** (jangan WiFi kantor/rumah) untuk mengetes apakah jaringan Anda memblokir `*.supabase.co`.
3. **Debug F12**: Sekarang Anda bisa menekan **F12** > Pilih tab **Console**. Jika ada tulisan merah "CORS", berarti langkah nomor 1 belum dilakukan atau belum tersimpan.
