# Simulasi Perhitungan Manual: TF-IDF & Naive Bayes
**Oleh: Almunajat aka Aas**

Dokumen ini berisi langkah-langkah perhitungan algoritma White-Box untuk klasifikasi teks "layanan mahal".

---

## 📚 Data Latih (Dataset)
Kita memiliki 3 dokumen ($N = 3$) dengan total kosakata unik ($|V| = 5$).

| ID | Teks Dokumen | Label |
|----|--------------|-------|
| D1 | "layanan bagus" | Positif |
| D2 | "harga mahal" | Negatif |
| D3 | "layanan lambat" | Negatif |

**Data Uji:** "layanan mahal"

---

## 🧮 FASE 1: PERHITUNGAN TF-IDF

### Langkah 1: Hitung IDF (Inverse Document Frequency)
Rumus yang digunakan (Scikit-Learn Standard):
$$IDF = \ln\left(\frac{N+1}{DF+1}\right) + 1$$

* **Kata "layanan"** ($DF = 2$):
    $$IDF = \ln\left(\frac{3+1}{2+1}\right) + 1 = \ln(1.333) + 1 = 1.287$$
* **Kata "bagus", "harga", "mahal", "lambat"** ($DF = 1$):
    $$IDF = \ln\left(\frac{3+1}{1+1}\right) + 1 = \ln(2) + 1 = 1.693$$

### Langkah 2: Bobot TF-IDF Akhir ($TF \times IDF$)
| Dokumen | Kata | TF | IDF | TF-IDF |
|---------|------|----|-----|--------|
| **D1** | layanan | 1 | 1.287 | **1.287** |
| | bagus | 1 | 1.693 | **1.693** |
| **D2** | harga | 1 | 1.693 | **1.693** |
| | mahal | 1 | 1.693 | **1.693** |
| **D3** | layanan | 1 | 1.287 | **1.287** |
| | lambat | 1 | 1.693 | **1.693** |

---

## 🧠 FASE 2: PERHITUNGAN NAIVE BAYES

### Langkah 3: Probabilitas Prior ($P(C)$)
* $P(\text{Positif}) = 1/3 = \mathbf{0.333}$
* $P(\text{Negatif}) = 2/3 = \mathbf{0.667}$

### Langkah 4: Total Bobot per Kelas
* Total Bobot Positif = $1.287 + 1.693 = \mathbf{2.980}$
* Total Bobot Negatif = $1.693 + 1.693 + 1.287 + 1.693 = \mathbf{6.366}$

### Langkah 5: Likelihood dengan Laplace Smoothing ($\alpha=1$)
Rumus:
$$P(\text{kata}|\text{Kelas}) = \frac{\text{Bobot TF-IDF kata di Kelas} + 1}{\text{Total Bobot di Kelas} + |V|}$$

**Jika Kelas = POSITIF:**
* $P(\text{layanan}|\text{Positif}) = \frac{1.287 + 1}{2.980 + 5} = \mathbf{0.286}$
* $P(\text{mahal}|\text{Positif}) = \frac{0 + 1}{2.980 + 5} = \mathbf{0.125}$

**Jika Kelas = NEGATIF:**
* $P(\text{layanan}|\text{Negatif}) = \frac{1.287 + 1}{6.366 + 5} = \mathbf{0.201}$
* $P(\text{mahal}|\text{Negatif}) = \frac{1.693 + 1}{6.366 + 5} = \mathbf{0.237}$

---

## 🏆 KESIMPULAN (Skor Posterior)
$$Skor = P(\text{Kelas}) \times P(\text{layanan}|\text{Kelas}) \times P(\text{mahal}|\text{Kelas})$$

* **Skor POSITIF:** $0.333 \times 0.286 \times 0.125 = \mathbf{0.0119}$
* **Skor NEGATIF:** $0.667 \times 0.201 \times 0.237 = \mathbf{0.0317}$

> **Hasil:** Karena Skor Negatif > Skor Positif, maka kalimat **"layanan mahal"** diklasifikasikan sebagai **NEGATIF**.