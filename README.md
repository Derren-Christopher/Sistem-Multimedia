# Sistem-Multimedia
# Watermarking Citra DCT dengan Evaluasi Kompresi JPEG

Tugas implementasi watermarking citra digital pada foto wajah menggunakan domain **DCT (Discrete Cosine Transform)** dengan skema **QIM (Quantization Index Modulation)**, lalu dievaluasi ketahanannya terhadap kompresi **JPEG** pada berbagai nilai Quality Factor (QF).

Seluruh operasi inti diimplementasikan **manual** tanpa memakai fungsi bawaan library (tidak menggunakan `cv2.dct`, `scipy.fft.dct`, encoder JPEG bawaan, dll).

---

## Fitur

- DCT-2D 8×8 dan IDCT-2D 8×8 ditulis manual dari rumus DCT-II.
- Kompresi JPEG manual: pembagian blok 8×8 → DCT → kuantisasi (tabel JPEG Annex K) → dekuantisasi → IDCT, dengan scaling Quality Factor mengikuti rumus libjpeg.
- Penyisipan watermark biner via QIM pada koefisien DCT mid-frequency.
- Ekstraksi watermark dari citra yang sudah dikompresi JPEG.
- Evaluasi otomatis: BER (Bit Error Rate), NC (Normalized Correlation), dan PSNR untuk berbagai QF.
- Visualisasi pada setiap tahap (foto asli, watermark, citra ter-watermark, citra terkompresi, watermark hasil ekstraksi, grafik BER & NC vs QF).

---

## Cara Menjalankan (Google Colab)

1. Buka [Google Colab](https://colab.research.google.com/).
2. Klik **File → Upload notebook**, lalu pilih file `watermarking_dct.ipynb`.
3. Jalankan sel **Setup** terlebih dahulu.
4. Pada sel **Upload Foto**, akan muncul dialog upload — unggah foto wajah kamu (JPG/PNG, bebas ukuran).
5. Jalankan sel berikutnya secara berurutan dari atas ke bawah.

### Menjalankan secara lokal (opsional)

```bash
pip install numpy pillow matplotlib
jupyter notebook watermarking_dct.ipynb
```

Pada sel upload, ganti bagian `try/except` ke path lokal foto kamu:

```python
fname = 'face.jpg'
```

---

## Struktur Notebook

| Tahap | Deskripsi |
|-------|-----------|
| 0 | Setup library (numpy, PIL, matplotlib) |
| 1 | Upload & pra-proses foto wajah (grayscale, resize 256×256) |
| 2 | Generate watermark biner 32×32 (1024 bit) |
| 3 | Implementasi manual DCT-2D & IDCT-2D 8×8 |
| 4 | Penyisipan watermark dengan QIM pada koefisien DCT (4,3) |
| 5 | Kompresi JPEG manual (DCT + kuantisasi Annex K + IDCT) |
| 6 | Ekstraksi watermark dari citra |
| 7 | Evaluasi pada QF = {95, 90, 80, 70, 60, 50, 40, 30, 20, 10} |
| 8 | Kesimpulan & ide eksperimen lanjutan |

---

## Parameter yang Bisa Diubah

| Variabel | Default | Keterangan |
|----------|---------|------------|
| `IMG_SIZE` | 256 | Ukuran citra host (harus kelipatan 8) |
| `WM_SIZE` | 32 | Ukuran sisi watermark biner |
| `DELTA` | 40.0 | Step size QIM. Besar → lebih robust tapi PSNR turun |
| `WM_POS` | (4, 3) | Posisi koefisien DCT untuk menyisipkan bit |
| `QFs` | [95..10] | Daftar Quality Factor yang dievaluasi |

---

## Metrik Evaluasi

- **PSNR (dB)** — kualitas visual citra ter-watermark vs citra asli (semakin tinggi semakin baik, > 35 dB umumnya tidak terlihat mata).
- **BER (%)** — persentase bit watermark yang salah saat diekstrak. BER < 5% berarti watermark masih dapat dikenali; BER ≈ 50% berarti gagal total.
- **NC (Normalized Correlation)** — korelasi watermark asli vs hasil ekstraksi. NC mendekati 1 berarti sempurna; mendekati 0 berarti acak.

---

## Hasil yang Diharapkan

- Pada QF tinggi (≥ 70) watermark masih dapat diekstrak dengan BER rendah.
- Saat QF diturunkan, kuantisasi JPEG semakin agresif sehingga koefisien DCT pembawa bit watermark rusak → BER meningkat.
- Notebook secara otomatis melaporkan **QF threshold** di mana watermark mulai rusak (BER > 5%) dan di mana watermark tidak dapat diekstrak (BER > 25%).

---

## Dependensi

- Python ≥ 3.8
- numpy
- pillow (PIL)
- matplotlib

Tidak ada library DCT/JPEG eksternal yang digunakan untuk operasi inti — semua manual sesuai persyaratan tugas.

---

## Catatan

- Foto host diproses sebagai grayscale untuk menyederhanakan implementasi. Skema yang sama bisa diperluas ke kanal Y pada YCbCr untuk citra berwarna.
- Watermark default adalah pola acak biner; bisa diganti pola geometris (lingkaran) atau citra logo dengan memodifikasi sel ke-2.
