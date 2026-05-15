# GML Ödev 4 — Katz Index & ParWalk ile GraphRAG Retrieval

## Genel Bakış

Bu proje, HippoRAG2 ve LinearRAG sistemlerindeki **Personalized PageRank (PPR)** retrieval algoritmasının iki alternatif yöntemle değiştirilmesini içermektedir.

- **Soru 1 (80 pnt):** PPR yerine Katz Index (K=10)
- **Soru 2 (20 pnt):** PPR yerine ParWalk

---

## Algoritmalar

### Katz Index (Soru 1)

Katz Index, iki düğüm arasındaki tüm yolları uzunluklarına göre üstel olarak azalan ağırlıklarla toplar.

**Formül (Denklem 2, K=10):**

```
s_q = sum_{k=1}^{K} beta^k * A^k * e_q
```

- `A`: Adjacency matrisi
- `beta`: Damping parametresi (`0.85 / lambda_max`)
- `K = 10`: Truncation derinliği (ödev şartı)
- `e_q`: Seed düğümlerinin indikatör vektörü

Hesaplama yöntemi: sparse power iteration (K adımda, matris tersi almadan).

---

### ParWalk (Soru 2)

ParWalk, Laplacian regularizasyonuna dayanan bir retrieval yöntemidir.

**Formül:**

```
s_q = (L + alpha * I)^{-1} * e_q
```

- `L = D - A`: Graf Laplacian'ı
- `alpha`: Regularizasyon parametresi (varsayılan: 0.1)

Hesaplama yöntemi: `scipy.sparse.linalg.spsolve` ile sparse lineer sistem çözümü (matris tersi alınmaz, doğrudan çözülür).

---

## Dosya Yapısı

```
GML_Odev4_Katz_ParWalk.ipynb   # Ana notebook
README.md                       # Bu dosya
```

---

## Notebook İçeriği

| Bölüm | Konu |
|-------|------|
| 0 | Kurulum (bağımlılıklar, repo klonlama) |
| 1 | Referans PPR implementasyonu |
| 2 | Katz Index implementasyonu (Soru 1, 80 pnt) |
| 3 | ParWalk implementasyonu (Soru 2, 20 pnt) |
| 4 | HippoRAGRetriever wrapper sınıfı |
| 5 | Demo ve karşılaştırma deneyi |
| 6 | Ölçeklenebilirlik testi (N = 500 / 1000 / 2000) |
| 7 | LinearRAG pipeline entegrasyonu |
| 8 | Özet ve sonuç tablosu |

---

## Kurulum ve Çalıştırma

### Gereksinimler

```
numpy==1.26.4
scipy
networkx
```

### Google Colab

1. Notebook'u Colab'a yükle: `File > Upload notebook`
2. Runtime türünün **GPU** olduğunu kontrol et
3. `Runtime > Run All` ile tüm hücreler çalıştırılır

---

## Sonuçlar

### Algoritma Karşılaştırması (N=200 düğüm, Barabási-Albert graf)

| Algoritma | Formül | Yöntem | Karmaşıklık |
|-----------|--------|--------|-------------|
| PPR (Referans) | π = α * A^T * π + (1-α) * e_q | Power iteration | O(K * \|E\|) |
| Katz Index (K=10) | s_q = Σ β^k A^k e_q | Sparse power iteration | O(K * \|E\|) |
| ParWalk | s_q = (L + αI)^{-1} e_q | Sparse lineer çözüm | O(\|V\|^1.5) sparse |

### Ölçeklenebilirlik

Tüm üç yöntem N=2000 düğüme kadar milisaniye mertebesinde çalışır.

---

## Referanslar

- HippoRAG2: https://github.com/GraphRAG-Bench/GraphRAG-Benchmark
- LinearRAG: https://github.com/DEEP-PolyU/LinearRAG
- Katz Index formülasyonu: https://arxiv.org/pdf/1912.06525 (Denklem 2)
- ParWalk: Li et al., NIPS 2012 — https://www.ee.columbia.edu/~zgli/papers/NIPS2012_PARW.pdf
