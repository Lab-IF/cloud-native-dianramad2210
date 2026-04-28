# Next.js Docker App

Contoh ini dipakai sebagai bahan ajar untuk menjawab pertanyaan: "Apa yang sebenarnya dimasukkan ke Docker?" Isinya bukan halaman kosong, tetapi aplikasi Next.js kecil yang punya UI, API route, environment variable, health check, test, dan build production.

## Mulai dari materi Next.js dulu

Kalau besok ingin mengajar Next.js sebelum Docker, mulai dari:

```text
docs\nextjs-basics.md
```

Materi itu menjelaskan cara menjalankan project, struktur App Router, `layout.js`, `page.js`, CSS global, component, folder `lib`, API route, environment variable, public assets, dan latihan mahasiswa. Setelah mahasiswa paham isi aplikasinya, lanjutkan ke bagian Docker di README ini.

## Yang dipelajari

| Bagian | Fungsi | Komentar di kode |
|--------|--------|------------------|
| `app\layout.js` | Layout global, metadata, `{children}` | ✅ Layout, import CSS, App Router |
| `app\page.js` | UI utama untuk demo browser | ✅ Import, env var, JSX, className, `.map()` |
| `app\globals.css` | CSS global: variabel, flexbox, grid, responsive | ✅ CSS Variables, Flexbox vs Grid, `@media`, `::before` |
| `components\MetricCard.js` | Component React dengan props sederhana | ✅ Props, destructuring, export default |
| `components\DeploymentStep.js` | Component React dengan object props | ✅ Object props, `step.title` |
| `lib\health.mjs` | Helper function untuk health check | ✅ Pure function, default params, `process.env` |
| `lib\teachingData.js` | Data terpisah dari UI | ✅ Pemisahan data, array of objects |
| `app\api\health\route.js` | Health endpoint untuk Docker/Compose/CI | ✅ API route, `NextResponse`, `dynamic` |
| `app\api\deployments\route.js` | Data JSON untuk alur deployment | ✅ Multiple API endpoints |
| `next.config.mjs` | Konfigurasi Next.js | ✅ `standalone` output, turbopack, `reactStrictMode` |
| `Dockerfile` | Multi-stage build Next.js production | ✅ 4 stage: base, deps, builder, runner |
| `compose.yaml` | Menjalankan app dengan env var dan health check | — |
| `tests\health.test.mjs` | Test sederhana untuk pipeline CI | — |

> 💡 **Setiap file sudah dilengkapi komentar penjelasan berbahasa Indonesia.**
> Mahasiswa cukup buka file di VS Code → langsung baca penjelasan tiap baris tanpa perlu buka README terpisah.

## Jalankan lokal

```powershell
cd "cloud-native-practicum\examples\nextjs-docker-app"
npm install
npm run dev
```

Buka `http://localhost:3000`.

## Test dan build

```powershell
npm test
npm run build
```

## Build Docker image

```powershell
docker build -t nextjs-docker-app:kelas .
```

Dockerfile memakai 4 tahap (lihat komentar di dalam file untuk penjelasan lengkap):

| Stage | Isi |
|-------|-----|
| `base` | Image dasar Node.js 20 Alpine |
| `deps` | Install dependency dengan `npm ci` |
| `builder` | Menjalankan test dan `next build` |
| `runner` | Menjalankan output standalone sebagai user non-root |

## Run container

```powershell
docker run --rm -p 3000:3000 `
  -e APP_ENV=docker `
  -e APP_VERSION=0.1.0 `
  -e NEXT_PUBLIC_APP_MESSAGE="Halo dari Docker" `
  nextjs-docker-app:kelas
```

Cek hasilnya:

```powershell
curl http://localhost:3000
curl http://localhost:3000/api/health
```

## Run dengan Docker Compose

```powershell
docker compose up --build
```

Compose akan:

1. Build image dari `Dockerfile`.
2. Menjalankan container pada port `3000`.
3. Mengirim environment variable ke runtime Next.js.
4. Mengecek `/api/health` sebagai health check.

Untuk menghentikan:

```powershell
docker compose down
```

## Alur demo mengajar

1. Buka project di VS Code — mahasiswa baca komentar di setiap file.
2. Tunjukkan `app\layout.js` → jelaskan layout global dan `{children}`.
3. Tunjukkan `app\page.js` → jelaskan JSX, className, env var, `.map()`.
4. Tunjukkan `app\globals.css` → jelaskan CSS Variables, Flexbox, Grid, Responsive.
5. Tunjukkan `components\MetricCard.js` → jelaskan props dan component.
6. Tunjukkan `app\api\health\route.js` → jelaskan API route.
7. Jalankan `npm run dev` dan buka browser `http://localhost:3000`.
8. Jalankan `npm test` dan `npm run build` untuk membuktikan kode siap.
9. Baca `Dockerfile` dari atas ke bawah: base, deps, builder, runner.
10. Build image dengan `docker build`.
11. Run container dan buka browser.
12. Ubah `NEXT_PUBLIC_APP_MESSAGE` di `compose.yaml`, jalankan ulang, lalu lihat UI berubah.
13. Tunjukkan endpoint `/api/health` sebagai bahan smoke test di pipeline CI/CD.

## Hubungan dengan DevOps/CI-CD

Contoh pipeline untuk app ini ada di:

```text
devops-cicd-practicum\examples\nextjs-docker-pipeline
```
