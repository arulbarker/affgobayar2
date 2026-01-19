# AI Photo Studio - Vercel Deployment

## Quick Deploy

1. Fork repository ini ke GitHub Anda
2. Buka [Vercel](https://vercel.com)
3. Klik "New Project" → Import from GitHub
4. Pilih repository ini
5. Klik "Deploy"

## Setup Pertama Kali

1. Buka aplikasi yang sudah di-deploy
2. Klik tab **Pengaturan**
3. Pilih provider API:
   - **Google AI Studio** - API resmi Google Gemini
   - **val.ai** - API aggregator kompatibel Gemini
   - **atlascloud** - API aggregator kompatibel Gemini
   - **kie.ai** - Task-based API dengan Gemini Flash 2.5 Image
4. Dapatkan API key dari provider pilihan Anda
5. Paste API key di form
6. Klik "Test Koneksi" untuk verifikasi
7. Klik "Simpan API Key"

## Supported API Providers

### Google AI Studio (Recommended)
- **Website**: https://aistudio.google.com/app/apikey
- **Format**: AIzaSy...
- **Kelebihan**: API resmi, dokumentasi lengkap, update terbaru
- **Gratis**: Ya (dengan quota)

### val.ai
- **Website**: https://val.ai
- **Format**: val_...
- **Kelebihan**: API aggregator, alternatif Google AI Studio
- **Kompatibilitas**: 100% kompatibel dengan Gemini API

### atlascloud
- **Website**: https://atlascloud.dev
- **Format**: atlas_...
- **Kelebihan**: API aggregator, pricing kompetitif
- **Kompatibilitas**: 100% kompatibel dengan Gemini API

### kie.ai
- **Website**: https://kie.ai
- **Format**: kie_...
- **Model**: google/nano-banana-edit (Gemini Flash 2.5 Image)
- **Kelebihan**: Task-based async API, reliable image generation
- **Kompatibilitas**: Gemini Flash 2.5 Image compatible

## Fitur

70 AI Tools untuk photo/video generation:
- Product Photography (5 tools)
- UGC Content (8 tools)
- Family & Lifestyle (10 tools)
- Creative Tools PRO (17 tools)
- Video & Audio PRO (4 tools)
- Security & Business (22 tools)

## Security

- API key disimpan di browser Anda (localStorage)
- Enkripsi XOR untuk storage
- Tidak dikirim ke server selain provider yang dipilih
- Auto-clear saat logout
- Support 4 providers: Google AI Studio, val.ai, atlascloud, kie.ai

## GitHub

Repository: https://github.com/arulbarker/affgobayar.git
