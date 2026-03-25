# Simulasi Perhitungan Manual: TF-IDF & Naive Bayes
**Oleh: Almunajat aka Aas**

Ide yang sangat cemerlang! Membawa contoh perhitungan manual langkah demi langkah ke dalam presentasi adalah cara terbaik untuk membuat dosen penguji terpukau. Ini membuktikan bahwa kamu tidak hanya "menempel" kodingan, tapi benar-benar menguasai ilmu di baliknya (**Algoritma White-Box**).

Mari kita buat skenario simulasi yang sederhana, terstruktur, dan sangat mudah diikuti untuk slide presentasimu.

---

## 📚 Data Latih Sederhana (Contoh Kasus)
Katakanlah kita memiliki 3 kalimat ulasan (Total Dokumen $N = 3$) dengan 2 label (Positif/Negatif).

| ID | Teks Dokumen | Label |
|----|--------------|-------|
| D1 | "layanan bagus" | Positif |
| D2 | "harga mahal" | Negatif |
| D3 | "layanan lambat" | Negatif |

* **Total Kosakata Unik ($|V|$):** 5 kata [layanan, bagus, harga, mahal, lambat].
* **Kalimat Prediksi (Data Uji):** "layanan mahal"

---

## 🧮 FASE 1: PERHITUNGAN TF-IDF
Kita harus mengubah teks menjadi bobot angka terlebih dahulu.

### Langkah 1: Hitung TF (Term Frequency)
Berapa kali kata muncul di setiap kalimat?
* **D1:** layanan (1), bagus (1)
* **D2:** harga (1), mahal (1)
* **D3:** layanan (1), lambat (1)

### Langkah 2: Hitung DF (Document Frequency) & IDF
Berapa banyak dokumen yang mengandung kata tersebut? Rumus IDF Scikit-Learn yang kita gunakan di kodingan adalah:
$$IDF = \ln\left(\frac{N+1}{DF+1}\right) + 1$$
*(Catatan: $\ln$ adalah logaritma natural)*

* **Kata "layanan"** (Muncul di D1 dan D3, maka $DF = 2$):
    $$IDF = \ln\left(\frac{3+1}{2+1}\right) + 1 = \ln(1.333) + 1 = 0.287 + 1 = 1.287$$
* **Kata "bagus", "harga", "mahal", "lambat"** (Masing-masing hanya muncul di 1 dokumen, maka $DF = 1$):
    $$IDF = \ln\left(\frac{3+1}{1+1}\right) + 1 = \ln(2) + 1 = 0.693 + 1 = 1.693$$

### Langkah 3: Bobot TF-IDF Akhir ($TF \times IDF$)
Tabel nilai TF-IDF untuk data latih kita:

| Dokumen | Kata | TF | IDF | TF-IDF |
|---------|------|----|-----|--------|
| **D1 (Positif)** | layanan | 1 | 1.287 | **1.287** |
| | bagus | 1 | 1.693 | **1.693** |
| **D2 (Negatif)** | harga | 1 | 1.693 | **1.693** |
| | mahal | 1 | 1.693 | **1.693** |
| **D3 (Negatif)** | layanan | 1 | 1.287 | **1.287** |
| | lambat | 1 | 1.693 | **1.693** |

---

## 🧠 FASE 2: PERHITUNGAN NAIVE BAYES
Sekarang, model Naive Bayes akan menggunakan bobot TF-IDF di atas untuk menebak kalimat baru: **"layanan mahal"**.

### Langkah 4: Hitung Prior (Peluang Kelas)
Dari 3 dokumen latih, ada 1 Positif dan 2 Negatif.
* $P(\text{Positif}) = 1/3 = \mathbf{0.333}$
* $P(\text{Negatif}) = 2/3 = \mathbf{0.667}$

### Langkah 5: Persiapan Total Bobot per Kelas
Untuk menghitung Likelihood, kita butuh total jumlah bobot TF-IDF di masing-masing kelas:
* **Total Bobot Positif** = $1.287 + 1.693 = \mathbf{2.980}$
* **Total Bobot Negatif** = $1.693 + 1.693 + 1.287 + 1.693 = \mathbf{6.366}$

### Langkah 6: Hitung Likelihood (Peluang Kata per Kelas)
Gunakan rumus Laplace Smoothing ($\alpha = 1$):
$$P(\text{kata}|\text{Kelas}) = \frac{\text{Bobot TF-IDF kata di Kelas} + 1}{\text{Total Bobot di Kelas} + |V|}$$

**Peluang jika Positif (Penyebut = $2.980 + 5 = 7.980$):**
* $P(\text{layanan}|\text{Positif}) = \frac{1.287 + 1}{7.980} = \mathbf{0.286}$
* $P(\text{mahal}|\text{Positif}) = \frac{0 + 1}{7.980} = \mathbf{0.125}$ *(Kata mahal tidak ada di D1)*

**Peluang jika Negatif (Penyebut = $6.366 + 5 = 11.366$):**
* $P(\text{layanan}|\text{Negatif}) = \frac{1.287 + 1}{11.366} = \mathbf{0.201}$
* $P(\text{mahal}|\text{Negatif}) = \frac{1.693 + 1}{11.366} = \mathbf{0.237}$

### Langkah 7: Hitung Skor Posterior (Penentuan Pemenang)
$$Skor = P(\text{Kelas}) \times P(\text{layanan}|\text{Kelas}) \times P(\text{mahal}|\text{Kelas})$$

* **Skor POSITIF:** $0.333 \times 0.286 \times 0.125 = \mathbf{0.0119}$
* **Skor NEGATIF:** $0.667 \times 0.201 \times 0.237 = \mathbf{0.0317}$

---

## 🏆 KESIMPULAN PRESENTASI
Karena **0.0317 > 0.0119**, maka model Naive Bayes berhasil memprediksi secara matematis bahwa kalimat **"layanan mahal"** masuk ke dalam kategori **NEGATIF**.

Penjelasan di atas sangat terstruktur dan dijamin membuat audiens paham bagaimana algoritma mengubah kata menjadi angka, lalu angka menjadi probabilitas!