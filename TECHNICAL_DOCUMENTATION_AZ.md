# RFM MÜŞTƏRİ SEQMENTASİYASI ANALİZİ
## TEXNİKİ DOKUMENTASİYA

---

## 1. VERİLƏNLƏR LÜĞƏTİ (Data Dictionary)

### 1.1 Əsas məlumat strukturu

```sql
Cədvəl: Müştərilər
├── CustomerID (varchar) - Müştərinin unikal identifikatoru
├── LastPurchaseDate (date) - Son alış tarixi
├── PurchaseCount (integer) - Ümumi alış sayı
├── TotalSpent (decimal) - Ümumi xərclənən məbləğ
├── RFM_Score (decimal) - Hesablanmış RFM balı
└── Segment (varchar) - Təyin edilmiş seqment
```

### 1.2 Əsas dəyişənlər

#### **Recency (R) — son alışdan keçən müddət**

| Parametr | Təsvir |
| --- | --- |
| **Formula** | Recency = bugünkü tarix − son alış tarixi |
| **Vahid** | Gün |
| **Bal diapazonu** | 1–5 (5 = ən yüksək) |
| **Şərh** | Aşağı dəyərlər daha yüksək aktivliyi göstərir |

**Ballandırma cədvəli:**

```
Gün diapazonu    → R balı
0–30 gün         → 5
31–60 gün        → 4
61–90 gün        → 3
91–180 gün       → 2
180+ gün         → 1
```

#### **Frequency (F) — alış tezliyi**

| Parametr | Təsvir |
| --- | --- |
| **Formula** | Frequency = müəyyən dövr ərzində alışların sayı |
| **Vahid** | Ədəd (əməliyyat sayı) |
| **Bal diapazonu** | 1–5 (5 = ən yüksək) |
| **Şərh** | Daha çox alış daha yüksək sadiqlik deməkdir |

**Ballandırma cədvəli (12 aylıq dövr):**

```
Alış sayı        → F balı
25+ alış         → 5
15–24 alış       → 4
8–14 alış        → 3
4–7 alış         → 2
1–3 alış         → 1
```

#### **Monetary (M) — pul xərci**

| Parametr | Təsvir |
| --- | --- |
| **Formula** | Monetary = ümumi xərclənən məbləğ |
| **Vahid** | Pul vahidi (AZN, USD və s.) |
| **Bal diapazonu** | 1–5 (5 = ən yüksək) |
| **Şərh** | Müştərinin ümumi ömürboyu dəyərini göstərir |

**Ballandırma cədvəli:**

```
Xərc diapazonu   → M balı
50,000+ AZN      → 5
30,000–49,999    → 4
15,000–29,999    → 3
5,000–14,999     → 2
0–4,999          → 1
```

---

## 2. SEQMENTASİYA MODELİ

### 2.1 Seqmentlərin təsviri

#### **1. CHAMPION (Çempion)**

```
Xüsusiyyətlər:   R ≥ 4 VƏ F ≥ 4 VƏ M ≥ 4
Say:             2–3 müştəri
Faiz:            ~5%
Dəyər səviyyəsi: Ən yüksək (VIP)
```

**Profil:**

- Son 30 gün ərzində alış edir
- Yüksək alış tezliyi
- Ən yüksək monetar dəyər
- Brendə bağlılıq və sadiqlik

**Məqsəd:**

- Uzunmüddətli sadiqliyin saxlanılması
- Tövsiyə (referral) proqramlarına dəvət
- Premium xidmətin təmin edilməsi

---

#### **2. LOYAL MÜŞTƏRİLƏR (Loyal Customers)**

```
Xüsusiyyətlər:   (F ≥ 3 VƏ M ≥ 3) VƏ (R ≥ 2)
Say:             3–4 müştəri
Faiz:            ~8%
Dəyər səviyyəsi: Yüksək
```

**Profil:**

- Müntəzəm alışlar
- Yüksək xərcləmə
- Brendə sadiqlik
- Diqqətə və fərdi yanaşmaya həssaslıq

**Məqsəd:**

- Daha yüksək sifariş dəyəri
- Çarpaz satış və əlavə satış (cross-selling/up-selling) fəaliyyətləri
- Loyallıq proqramının təkmilləşdirilməsi

---

#### **3. POTENSİAL BÖYÜMƏ (Potential High Value)**

```
Xüsusiyyətlər:   (R ≤ 3 VƏ F ≥ 3) VƏ YA (M ≥ 4 VƏ F = 2)
Say:             2–3 müştəri
Faiz:            ~6%
Dəyər səviyyəsi: Orta–yüksək
```

**Profil:**

- Son dövrdə aktivlik göstərib
- Böyümə potensialı mövcuddur
- Aktivliyin azalması riski var

**Məqsəd:**

- Aktivliyin saxlanılması
- Təkrar alışa təşviq
- Qarşılıqlı əlaqənin artırılması

---

#### **4. RİSK ALTINDA OLANLAR (At Risk)**

```
Xüsusiyyətlər:   R ≤ 2 VƏ F ≥ 2 VƏ M ≥ 2
Say:             5–7 müştəri
Faiz:            ~15%
Dəyər səviyyəsi: Orta
```

**Profil:**

- Keçmiş dəyər: orta–yüksək
- Hazırkı vəziyyət: azalan aktivlik
- Uzun müddətdir fəaliyyətsizdir
- Müştəri itkisi riski yüksəkdir

**Məqsəd:**

- Təcili kommunikasiya
- Xüsusi təkliflər və endirimlər
- Səbəbləri anlamaq üçün rəy (feedback) toplamaq

---

#### **5. YENİ MÜŞTƏRİLƏR (New Customers)**

```
Xüsusiyyətlər:   R ≥ 4 VƏ F ≤ 2 VƏ M ≤ 3
Say:             2–3 müştəri
Faiz:            ~6%
Dəyər səviyyəsi: Aşağı (böyümə potensialı ilə)
```

**Profil:**

- Yeni müştəridir (ilk 30–60 gün)
- Sınaq mərhələsindədir
- Müştəri itkisi riski mövcuddur
- Müsbət ilkin təcrübəyə ehtiyacı var

**Məqsəd:**

- Yüksək keyfiyyətli ilkin istifadə təcrübəsi
- Təkrar alışa təşviq
- Uzunmüddətli münasibətin qurulması

---

#### **6. İTİRİLMİŞ MÜŞTƏRİLƏR (Lost Customers)**

```
Xüsusiyyətlər:   R ≤ 1 VƏ F ≤ 2 VƏ M ≤ 2
Say:             8–12 müştəri
Faiz:            ~25%
Dəyər səviyyəsi: Aşağı (bərpa potensialı ilə)
```

**Profil:**

- 6–12 ay və ya daha uzun müddətdir fəaliyyətsizdir
- Keçmiş dəyər: aşağı–orta
- Müştəri sadiqliyinin və ya marağının itirilməsi
- Rəqiblərə keçmiş ola bilər

**Məqsəd:**

- Geri qazanma (win-back) kampaniyaları
- Məhdudmüddətli xüsusi təkliflər
- Əlaqə məlumatlarını doğrulamaq

---

#### **7. DİQQƏT TƏLƏB EDƏN (Needs Attention)**

```
Xüsusiyyətlər:   R ≤ 2 VƏ F ≤ 2 VƏ M ≤ 2
Say:             15–20 müştəri
Faiz:            ~40%
Dəyər səviyyəsi: Çox aşağı
```

**Profil:**

- Çox uzun müddətdir fəaliyyətsizdir
- Aşağı tarixi dəyər
- Məlumatların keyfiyyəti ilə bağlı risk
- Kritik vəziyyət

**Məqsəd:**

- Aktivliyin artırılması üçün kampaniya
- Əlaqə məlumatlarının təsdiqlənməsi
- Bazanın təmizlənməsi məsələsi

---

## 3. HESABLAMA METODOLOGİYASI

### 3.1 RFM balının hesablanması

#### **Addım 1: Ayrı-ayrı balların hesablanması**

```excel
=IF(Recency_Days<=30, 5, IF(Recency_Days<=60, 4, IF(Recency_Days<=90, 3, IF(Recency_Days<=180, 2, 1))))
=IF(Frequency>=25, 5, IF(Frequency>=15, 4, IF(Frequency>=8, 3, IF(Frequency>=4, 2, 1))))
=IF(Monetary>=50000, 5, IF(Monetary>=30000, 4, IF(Monetary>=15000, 3, IF(Monetary>=5000, 2, 1))))
```

#### **Addım 2: RFM ballarının kombinasiyası**

```
RFM_Composite_Score = (R_Score × 100) + (F_Score × 10) + M_Score

Diapazon: 111–555
```

#### **Addım 3: Seqmentin təyin edilməsi**

```
IF(RFM_Composite_Score >= 444, "Champion",
   IF(RFM_Composite_Score >= 434, "Loyal",
      IF(RFM_Composite_Score >= 333, "Potential",
         IF(RFM_Composite_Score >= 324, "At_Risk",
            IF(RFM_Composite_Score >= 444 AND R_Score = 5, "New",
               IF(RFM_Composite_Score <= 222, "Lost",
                  "Needs_Attention"))))))
```

### 3.2 Verilənlərin yenilənməsi

**Yenilənmə tezliyi:** Aylıq  
**Zaman nöqtəsi:** Hər ayın son həftəsi  
**Proses:**

```
1. Əvvəlki dövr üzrə bütün əməliyyat məlumatlarının əldə edilməsi
2. Recency, Frequency və Monetary dəyərlərinin yenilənməsi
3. Yeni R, F və M ballarının hesablanması
4. Müştərilərin seqmentlərə yenidən təyin edilməsi
5. Dəyişikliklərin sənədləşdirilməsi (dəyişikliklərin təhlili)
6. Maraqlı tərəflərə hesabatın göndərilməsi
```

---

## 4. KEYFİYYƏTƏ NƏZARƏT VƏ VALİDASİYA

### 4.1 Məlumatların doğrulanması

```
✓ Null/boş dəyərlərin yoxlanılması
✓ Tarix diapazonunun məntiqə uyğunluğu (son alış ≤ bu gün)
✓ Müsbət dəyərlərin doğrulanması (alış sayı, məbləğ)
✓ Kovariant xəbərdarlığının təhlili
✓ Kənar dəyərlərin aşkarlanması (outlier detection)
```

### 4.2 Seqment balansının yoxlanılması

```
Normal bölgü:
- Champion:          3–5%
- Loyal:             8–10%
- Potential:         6–10%
- At Risk:          15–20%
- New:               5–8%
- Lost:             20–30%
- Needs Attention:  30–40%

Kənarlaşma həddi: ±5% (araşdırma tələb olunur)
```

---

## 5. TƏHLİL VƏ İSTİFADƏ

### 5.1 Dashboard metrikləri

```
ƏSAS KPI-LƏR:
├── Seqment bölgüsü (Pie Chart)
├── Seqmentlərin zaman üzrə dəyişməsi (Trend)
├── Orta RFM balı (Heatmap)
└── Seqment keçid matrisi

İKİNCİ DƏRƏCƏLİ KPI-LƏR:
├── Seqment üzrə orta müştəri dəyəri (CLV)
├── Aktivləşdirmə dərəcəsi (Recency)
├── Təkrar alış dərəcəsi (Frequency)
└── İdeal xərcləmə dərəcəsi (Monetary)
```

### 5.2 Müdaxilə hədəflərinin müəyyən edilməsi

```
Seqment           Hədəf metrika                  Hədəf dəyər  İnterval
─────────────────────────────────────────────────────────────────────
Champion          CLV-nin saxlanılması           +5%          Aylıq
Loyal             Çarpaz satış dərəcəsi          +20%         Rüblük
Potential         Aktivliyin saxlanılması        +25%         İki həftədən bir
At Risk           Yenidən aktivləşmə dərəcəsi    +30%         Həftəlik
New               Təkrar alış dərəcəsi           +40%         Rüblük
Lost              Geri qazanma dərəcəsi          +15%         Aylıq
Needs Attention   Yenidən cəlbetmə dərəcəsi      +20%         Aylıq
```

---

## 6. İSTİFADƏ SƏNARİLƏRİ VƏ KONFİQURASİYA

### 6.1 RFM bal parametrləri (əlavə tənzimləmə)

Aşağıdakı parametrlər biznes tələblərinə uyğun tənzimlənə bilər:

```yaml
Recency:
  Latest: 30 gün
  Recent: 31–60 gün
  Moderate: 61–90 gün

Frequency:
  VeryHigh: 25+ alış
  High: 15–24 alış
  Medium: 8–14 alış

Monetary:
  Premium: 50,000+ AZN
  HighValue: 30,000–49,999 AZN
```

### 6.2 Dinamik seqmentasiya

Düzəliş tələb olunan hallar:

1. Bazanın ölçüsü kəskin şəkildə dəyişərsə
2. Davranış meyarları markaya görə fərqlənərsə
3. Seqmentdaxili meyarlar fərdiləşdirmə tələb edərsə

---

## 7. QAYDALAR VƏ TƏHLÜKƏSİZLİK

### 7.1 Məlumatların məxfiliyi (Data Privacy)

- ✓ GDPR və yerli məlumatların qorunması qanunlarına uyğunluq
- ✓ Müştəri məlumatlarının şifrələnməsi
- ✓ Giriş hüquqlarının məhdudlaşdırılması (əsasən analitika komandası üçün)
- ✓ Müxtəlif audit qeydləri

### 7.2 Məlumatların saxlanılması siyasəti

- **Aktiv məlumatlar:** 24 ay
- **Arxiv:** 3 il
- **Silmə:** Müştərinin silinmə tələbindən sonra 30 gün ərzində

---

## 8. KANALLAR VƏ İNTEQRASİYA

### 8.1 Məlumat mənbələri

```
├── CRM sistemi (əsas)
│   └── CustomerID, alış tarixçəsi
├── Elektron ticarət platforması
│   └── Qiymət məlumatları, sifariş tarixçəsi
├── E-poçt platforması
│   └── Kampaniyalarla qarşılıqlı əlaqə
└── Analitika aləti
    └── Veb-sayt aktivliyi
```

### 8.2 Əlaqə tezliyi

```
Seqment           E-poçt             SMS       Push      In-App    Telefon
─────────────────────────────────────────────────────────────────────────
Champion          Həftəlik           Aylıq     Aylıq     Aylıq     Rüblük
Loyal             İki həftədən bir   Aylıq     Aylıq     Aylıq     Rüblük
At Risk           Həftəlik           Həftəlik  Həftəlik  Həftəlik  Rüblük
Lost              Aylıq              Aylıq     —         —         Rüblük
Needs Attention   Aylıq              Aylıq     —         —         Rüblük
```

---

## 9. MƏNBƏLƏR VƏ İSTİNADLAR

- RFM Analysis Best Practices, Gartner Group
- Customer Segmentation Models, HBR
- Cohort Analysis Framework, Eric Seufert
- CLV Calculation Methods, Bain & Company

---

**Hazırlayan:** Verilənlərin analitikası şöbəsi  
**Son yenilənmə:** Sentyabr 2024  
**Versiya:** 1.0  
**Status:** ✅ Aktiv

---

*Hər hansı sual üçün Verilənlərin analitikası şöbəsi ilə əlaqə saxlayın.*
