# 🧬 ZINC Veritabanı Batch Docking Pipeline

Google Colab için ZINC veritabanı entegrasyonlu otomatik moleküler docking sistemi.

## 📋 İçindekiler

- [Özellikler](#özellikler)
- [Hızlı Başlangıç](#hızlı-başlangıç)
- [ZINC Kullanım Yöntemleri](#zinc-kullanım-yöntemleri)
- [Adım Adım Kullanım](#adım-adım-kullanım)
- [Önemli Notlar](#önemli-notlar)
- [Sorun Giderme](#sorun-giderme)

## ✨ Özellikler

### ZINC Entegrasyonu
- ✅ ZINC veritabanından otomatik ligand indirme
- ✅ ZINC subset desteği (FDA, world, investigational, vb.)
- ✅ ZINC ID listesinden toplu indirme
- ✅ Manuel ZINC ID girişi
- ✅ Otomatik SDF → PDBQT dönüştürme

### Docking Özellikleri
- ✅ AutoDock Vina 1.2.5 ile batch docking
- ✅ Google Drive entegrasyonu (kalıcı depolama)
- ✅ Kesintiden kaldığı yerden devam etme
- ✅ Otomatik grid box hesaplama
- ✅ Ligand validasyonu
- ✅ Kapsamlı log sistemi
- ✅ Bellek-optimize işleme

### Analiz
- ✅ Otomatik sonuç analizi ve görselleştirme
- ✅ Excel/CSV çıktıları
- ✅ Kalite sınıflandırması
- ✅ Top adayları listeleme

## 🚀 Hızlı Başlangıç

### 1. Notebook'u Google Colab'a Yükleyin

```
1. Google Drive'a girin
2. ZINC_Batch_Docking_Colab.ipynb dosyasını Drive'a yükleyin
3. Dosyaya çift tıklayıp "Google Colaboratory ile Aç" seçin
```

### 2. Runtime Tipi Seçin

```
Runtime > Change runtime type > Hardware accelerator: None (CPU yeterli)
```

### 3. Bölümleri Sırayla Çalıştırın

```
Bölüm 1: Drive bağlantısı ve kurulum (5-10 dakika)
Bölüm 2-5: Fonksiyon yüklemeleri (< 1 dakika)
Bölüm 6: Hedef protein hazırlama (2-5 dakika)
Bölüm 8: ZINC indirme ve ligand hazırlama (değişken)
Bölüm 9: Docking (değişken - ligand sayısına bağlı)
Bölüm 10-11: Analiz ve export (< 5 dakika)
```

## 🔬 ZINC Kullanım Yöntemleri

### Yöntem 1: ZINC Subset Kullanımı (ÖNERİLEN)

**En kolay yöntem - FDA onaylı ilaçlar gibi hazır setler**

```python
# Bölüm 8'de bu ayarları yapın:
USE_ZINC_SUBSET = True
ZINC_SUBSET_NAME = "fda"  # FDA onaylı ilaçlar
MAX_MOLECULES_FROM_SUBSET = 100  # İndirilecek maksimum molekül
```

**Popüler Subsetler:**
- `fda`: FDA onaylı ilaçlar (~3,000 molekül)
- `world`: Dünya çapında onaylı ilaçlar (~10,000)
- `investigational`: Araştırma aşamasındaki ilaçlar
- `lead-like`: Lead-like özellikler (Lipinski kurallarına uygun)
- `fragment`: Fragment library

### Yöntem 2: ZINC ID Dosyasından

**Özel ZINC ID listeniz varsa**

```python
# 1. zinc_ids.txt dosyası oluşturun:
ZINC000003830413
ZINC000003831231
ZINC000003831236

# 2. Bölüm 8'de:
USE_ZINC_SUBSET = False
ZINC_ID_FILE = PROJECT_ROOT / "zinc_ids.txt"
```

### Yöntem 3: Manuel ZINC ID Listesi

**Birkaç molekül test etmek için**

```python
# Bölüm 8'de:
USE_ZINC_SUBSET = False
ZINC_ID_FILE = None
MANUAL_ZINC_IDS = [
    'ZINC000003830413',
    'ZINC000003831231',
    'ZINC000003831236'
]
```

## 📖 Adım Adım Kullanım

### Adım 1: Kurulum (İlk Çalıştırma)

```python
# Bölüm 1: Drive bağlantısı
✓ Google Drive'a bağlanır
✓ Proje dizini oluşturur
✓ Alan kontrolü yapar

# Bölüm 1 (2. hücre): Yazılım kurulumu
✓ OpenBabel kurulur
✓ AutoDock Vina kurulur
✓ Python paketleri kurulur (RDKit, BioPython, vb.)
```

**Beklenen Süre:** 5-10 dakika (ilk kurulum)

### Adım 2: Hedef Protein Hazırlama

```python
# Bölüm 6'da PDB kodunuzu girin:
TARGET_PDB = "7EU8"  # Örnek: SARS-CoV-2 Main Protease

# Seçenek A: Tüm proteini kullan (otomatik)
target_path, grid_params = prepare_target_protein(TARGET_PDB)

# Seçenek B: Belirli bağlanma bölgesi
binding_site = [('A', 41), ('A', 49), ('A', 54)]  # chain:residue
target_path, grid_params = prepare_target_protein(TARGET_PDB, binding_site)
```

**Çıktı:**
- `target/7EU8.pdbqt` - Docking için hazır protein
- Grid parametreleri otomatik hesaplanır

### Adım 3: ZINC Molekül İndirme

```python
# Bölüm 8'i çalıştırın
# Seçtiğiniz yönteme göre ZINC'den indirilir:

✓ FDA subset → ~100 onaylı ilaç indirilir
✓ Rate limiting ile güvenli indirme
✓ SDF formatında kaydedilir
```

**Beklenen Süre:**
- 100 molekül: ~5-10 dakika
- 500 molekül: ~30-45 dakika

### Adım 4: Ligand Hazırlama (SDF → PDBQT)

```python
# Bölüm 8'in devamında otomatik çalışır:

✓ Her SDF dosyası için:
  - 3D koordinat kontrolü/üretimi
  - pH 7.4'te protonation
  - Gasteiger yükleri
  - PDBQT formatına çevirme
```

**Beklenen Süre:** Molekül başına ~2-5 saniye

### Adım 5: Batch Docking

```python
# Bölüm 9'da ayarları kontrol edin:

DOCKING_PARAMS = {
    'exhaustiveness': 16,  # 8=hızlı, 16=dengeli, 32=yüksek kalite
    'cpu': auto,  # Otomatik CPU tespiti
    'timeout': 180  # Molekül başına max 3 dakika
}
```

**Çalıştırın ve bekleyin:**
```
✓ Her ligand için Vina docking çalışır
✓ Sonuçlar her 10 ligandda bir kaydedilir
✓ Bellek her 100 ligandda bir temizlenir
✓ İlerleme çubuğu ile takip
```

**Beklenen Süre (exhaustiveness=16):**
- 100 ligand: ~1-2 saat
- 500 ligand: ~5-8 saat
- 1000 ligand: ~10-15 saat

**ÖNEMLI:** Colab bağlantısı kesilirse:
1. Bölüm 1'i tekrar çalıştırın
2. Bölüm 6'yı tekrar çalıştırın
3. Bölüm 9'u çalıştırın → Otomatik kaldığı yerden devam eder!

### Adım 6: Sonuç Analizi

```python
# Bölüm 10: Görselleştirme
✓ Affinity dağılım histogramı
✓ Top 20 ligand grafiği
✓ Kalite pie chart
✓ RMSD scatter plot

# Bölüm 11: Export
✓ Excel dosyası (tüm sonuçlar + top 100)
✓ CSV dosyaları
✓ Top 50 PDBQT listesi
```

## 📊 Çıktı Dosyaları

Tüm dosyalar `Google Drive/ZINC_Docking_Project/` altında:

```
ZINC_Docking_Project/
├── DOCKING_RESULTS.csv          # Tüm sonuçlar (ana dosya)
├── DOCKING_RESULTS.xlsx         # Excel versiyonu
├── TOP_50_CANDIDATES.csv        # En iyi 50 ligand
├── TOP_50_PDBQT_FILES.txt       # En iyi 50'nin PDBQT listesi
├── FAILED_LIGANDS.csv           # Başarısız dockinglar
├── INVALID_LIGANDS.csv          # Geçersiz ligandlar
├── docking_analysis.png         # Analiz grafikleri
├── QC_REPORT.txt                # Kalite kontrol raporu
│
├── ligands/
│   ├── zinc_raw/                # ZINC'den indirilen SDF'ler
│   └── pdbqt_final/             # Hazır PDBQT ligandları
│
├── target/
│   ├── 7EU8.pdbqt              # Hazır protein
│   └── 7EU8_clean.pdb          # Temizlenmiş PDB
│
├── results/
│   └── *_docked.pdbqt          # Her ligand için docked pozlar
│
└── logs/
    └── docking_*.log           # Detaylı log dosyası
```

## 🎯 Sonuçları Yorumlama

### Affinity Değerleri

```
< -10 kcal/mol  →  Mükemmel bağlanma (çok güçlü)
-10 to -7       →  İyi bağlanma (umut verici)
-7 to -5        →  Orta bağlanma
> -5            →  Zayıf bağlanma
```

### DOCKING_RESULTS.csv Sütunları

| Sütun | Açıklama |
|-------|----------|
| `Ligand_Name` | ZINC ID |
| `Affinity_kcal_mol` | Bağlanma afinitesi (ne kadar negatif o kadar iyi) |
| `Num_Poses` | Üretilen poz sayısı (9'a kadar) |
| `RMSD_ub` | Pozlar arası RMSD (>3Å ise farklı binding modları) |
| `Status` | Başarılı/Başarısız |
| `Quality` | Mükemmel/İyi/Zayıf |
| `Output_File` | Docked poz dosyası |

### En İyi Adayları Seçme

```python
# Excel dosyasını açın:
# "En İyi 100" sheet'inde en umut verici moleküller

# PyMOL/Chimera ile görselleştirme için:
# TOP_50_PDBQT_FILES.txt'deki dosyaları kullanın
```

## ⚙️ Performans Optimizasyonu

### Hızlı Tarama (Exhaustiveness 8)
```python
DOCKING_PARAMS = {
    'exhaustiveness': 8,
    'timeout': 90
}
# ~15-20 saniye/ligand
# Binlerce molekül için ön tarama
```

### Dengeli Mod (Exhaustiveness 16 - ÖNERİLEN)
```python
DOCKING_PARAMS = {
    'exhaustiveness': 16,
    'timeout': 180
}
# ~30-45 saniye/ligand
# Çoğu çalışma için ideal
```

### Yüksek Doğruluk (Exhaustiveness 32)
```python
DOCKING_PARAMS = {
    'exhaustiveness': 32,
    'timeout': 300
}
# ~90-120 saniye/ligand
# En iyi 50-100 aday için yeniden docking
```

## 🔧 Önemli Notlar

### ZINC API Rate Limiting

```python
# ZINCDownloader otomatik olarak:
✓ Her indirmede 0.5-1 saniye bekler
✓ Hataları yakalar ve loglar
✓ Başarısız indirmeleri atlar
```

**Manuel kontrol gerekirse:**
```python
# download_zinc_simple() fonksiyonunda:
time.sleep(1)  # Gerekirse artırın
```

### Grid Box Ayarlama

```python
# Varsayılan: Tüm protein kullanılır
# Özel bağlanma bölgesi için:

# 1. PyMOL'de proteini açın
# 2. Bağlanma bölgesi residue'lerini belirleyin
# 3. Notebook'ta:
binding_site = [
    ('A', 41),   # Chain A, residue 41
    ('A', 49),   # Chain A, residue 49
    ('A', 54)    # Chain A, residue 54
]
target_path, grid_params = prepare_target_protein(TARGET_PDB, binding_site)
```

### Bellek Yönetimi

```python
# Google Colab RAM: ~12GB
# Binlerce ligand için:

SAVE_INTERVAL = 10   # Sık kaydet
BATCH_SIZE = 50      # Daha sık bellek temizle
```

## 🐛 Sorun Giderme

### 1. ZINC İndirme Hataları

**Sorun:** "ZINC ID indirilemedi" hatası

**Çözüm:**
```python
# Rate limiting'i artırın:
time.sleep(2)  # 1 yerine 2 saniye bekle

# Veya manuel indirme:
# https://zinc15.docking.org/substances/ZINCID.sdf
```

### 2. SDF → PDBQT Dönüşüm Hatası

**Sorun:** "PDBQT dönüşümü başarısız"

**Çözüm:**
```python
# RDKit 3D koordinat üretiminde sorun olabilir
# INVALID_LIGANDS.csv dosyasını kontrol edin
# Geçersiz moleküller otomatik atlanır
```

### 3. Colab Bağlantı Kesildi

**Sorun:** "Runtime disconnected"

**Çözüm:**
```python
# 1. Bölüm 1'i yeniden çalıştırın (Drive bağla)
# 2. Bölüm 6'yı yeniden çalıştırın (hedef hazırla)
# 3. Bölüm 9'a geçin
# ✓ Otomatik olarak CSV'den kaldığı yerden devam eder
```

### 4. Çok Fazla Başarısız Docking

**Sorun:** Success rate < %50

**Çözüm:**
```python
# Grid box'ı kontrol edin:
# - Çok küçük mü? → padding artırın
# - Çok büyük mü? → Belirli residue'ler seçin

# Ligand validasyonunu kontrol edin:
# - INVALID_LIGANDS.csv'yi inceleyin
# - 3D koordinat problemleri olabilir
```

### 5. Timeout Hataları

**Sorun:** Çok fazla timeout

**Çözüm:**
```python
DOCKING_PARAMS['timeout'] = 300  # 3 dk → 5 dk
# veya
DOCKING_PARAMS['exhaustiveness'] = 8  # 16 → 8 (hızlı mod)
```

## 📚 İleri Seviye Kullanım

### Kendi SDF/MOL2 Dosyalarınızı Kullanma

```python
# Eğer zaten SDF dosyalarınız varsa:
# 1. zinc_download_dir'e kopyalayın
# 2. Bölüm 8'in son hücresini çalıştırın:
prepared_ligands = batch_prepare_ligands(zinc_download_dir, pdbqt_dir)
```

### Farklı Proteinlerle Paralel Docking

```python
# Her protein için ayrı PROJECT_ROOT kullanın:
PROJECT_ROOT_7EU8 = Path("/content/drive/MyDrive/ZINC_7EU8")
PROJECT_ROOT_6LU7 = Path("/content/drive/MyDrive/ZINC_6LU7")
```

### Sonuçları Filtreleme

```python
import pandas as pd

# Sonuçları yükle
df = pd.read_csv(PROJECT_ROOT / "DOCKING_RESULTS.csv")

# Mükemmel bağlananları filtrele
excellent = df[df['Affinity_kcal_mol'] <= -9.0]

# Lipinski Rule of 5 kontrolü için:
# (Bu bilgi ZINC'den çekilmeli - gelişmiş özellik)
```

## 🎓 Önerilen İş Akışı

### Yeni Bir Proje İçin:

1. **Ön Tarama (Geniş Kütüphane)**
   ```
   - ZINC subset: lead-like veya fragment
   - Exhaustiveness: 8
   - ~5,000-10,000 molekül
   - Süre: ~1-2 gün
   ```

2. **İkinci Tur (Top 500)**
   ```
   - Top 500 molekülü seç
   - Exhaustiveness: 16
   - Süre: ~6-8 saat
   ```

3. **Son Validasyon (Top 50-100)**
   ```
   - Top 50-100 molekülü seç
   - Exhaustiveness: 32
   - Görsel kontrol (PyMOL)
   - MD simülasyonları
   ```

## 📖 Referanslar

### AutoDock Vina
```
Eberhardt, J., Santos-Martins, D., Tillack, A.F., Forli, S. (2021).
AutoDock Vina 1.2.0: New Docking Methods, Expanded Force Field,
and Python Bindings. Journal of Chemical Information and Modeling.
```

### ZINC Database
```
Irwin, J.J., Tang, K.G., Young, J., et al. (2020).
ZINC20 - A Free Ultralarge-Scale Chemical Database for Ligand Discovery.
Journal of Chemical Information and Modeling.
```

### RDKit
```
RDKit: Open-source cheminformatics.
http://www.rdkit.org
```

## 💡 İpuçları

1. **İlk test için küçük başlayın** (10-20 molekül)
2. **Grid box'ı görselleştirin** (PyMOL ile kontrol)
3. **Log dosyalarını inceleyin** (hatalar için)
4. **Sonuçları düzenli kaydedin** (SAVE_INTERVAL)
5. **En iyi adayları manuel kontrol edin** (PyMOL/Chimera)

## 🆘 Yardım

**Sorunuz mu var?**
- Issues: GitHub repository
- Email: ersin.gundeger@istun.edu.tr

**Katkıda bulunmak ister misiniz?**
- Pull requests memnuniyetle karşılanır!

---

**Son Güncelleme:** Aralık 2024
**Versiyon:** 1.0
**Lisans:** MIT

---

✨ **Başarılı docking'ler dileriz!** ✨
