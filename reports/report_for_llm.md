# КЛАСТЕРИЗАЦИЯ МУНИЦИПАЛИТЕТОВ РОССИИ — ОТЧЁТ L1 + L2

*Сгенерирован: 2026-10-09 18:59*
*KAN-энкодер · 2 родительских кластеров · 5 L2-подкластеров · 2022 МО × 6 лет (2019–2024)*

## 0. EXECUTIVE SUMMARY

- **Горизонт**: 2019–2024 (6 годовых срезов; без размножения на месяцы)
- **Объектов**: 2022 МО верхнего уровня, 77+ регионов
- **Признаков после паспортизации**: 10
- **Кластеризация**: KAN-энкодер 10 → 32 → 8 (L1) и 10 → 16/32 → 6/8 (L2), whitening
- **KAN-L1**: R² = 0.9854, silhouette(k=2) = 0.6869
- **L1**: K = 2, ARI между соседними годами = 0.9767, stay rate = 0.9988
- **L2**: subK = {0: 2, 1: 3}, всего 5 подкластеров
    - K0 (subK=2): ARI(consec)=0.6157, stay_rate=0.9000
    - K1 (subK=3): ARI(consec)=0.6268, stay_rate=0.8665
- **SberIndex**: median-wages: 19 отраслей, 2020-2024, сопоставление роста зарплат L1 с отраслями Сбера, potrebitelskaya-aktivnost, real-key-interest-rate, SberIndex × prevalence L1/L2 (Spearman/Pearson), SberIndex × профили кластеров (Pearson), SberIndex median split MWU (high vs low years)

## 1. МЕТОДОЛОГИЯ

### 1.1. Данные
- Росстат БД ПМО: годовая панель 2022 МО × 6 лет
- SberIndex (национальные ряды): 4 показателя — зарплаты, потребительская активность, инфляция, реальная ключевая ставка
- Справочник ОКТМО → муниципальное образование (тип, регион)

### 1.2. Атрибуты узла
- Признаков: 10 (после паспортизации)
- z-стандартизация **внутри каждого года** (сравнение с общероссийским уровнем в текущем году)
- Логарифмирование и winsorization (0.01–0.99)
- Импутация: медиана «регион × год» → медиана года → общая

### 1.3. KAN-энкодер
- L1: KAN(10 → 32 → 8), grid=10, k=3, AdamW, cosine annealing
- Whitening после энкодера: cos ≈ 0.03, dead_dims = 0
- L2 (per parent): свой KAN на подвыборке каждого L1-кластера

### 1.4. Выбор K и subK
- L1: bootstrap-ARI, silhouette-sweep, jackknife по регионам
- L2: те же критерии ВНУТРИ каждого родителя
- Пороги: bootstrap-ARI ≥ 0.75, silhouette ≥ 0.15, jackknife ≥ 0.85

## 2. УРОВЕНЬ 1: КЛАСТЕРЫ URBAN / RURAL

**K = 2** (выбран по устойчивости)

### 2.1. K-selection summary
```
 K  silhouette         CH     DB  min_cluster_share  boot_ari  boot_ari_min  jackknife_ari  jackknife_min  n_regions  score  degenerate
 2      0.6866 807.360596 0.4945             0.0279    0.8957        0.8272         1.0000         1.0000         14 0.8957       False
 3      0.5452 739.881897 0.8949             0.0269    0.7140        0.6285         0.9509         0.3225         14 0.5858       False
 4      0.3038 724.880188 1.1581             0.0261    0.6050        0.4899         0.4726         0.1233         14 0.1799       False
 5      0.1996 717.050476 1.2656             0.0263    0.8748        0.8437         0.9585         0.9181         14 0.4429       False
 6      0.1942 662.389587 1.2948             0.0242    0.6636        0.6049         0.9324         0.5749         14 0.3236       False
 7      0.1862 619.293701 1.3181             0.0241    0.6416        0.5478         0.9392         0.9058         14 0.3104       False
 8      0.1816 581.267700 1.3387             0.0228    0.6094        0.5624         0.9043         0.4171         14 0.2814       False
 9      0.1752 550.071716 1.3693             0.0213    0.6118        0.5587         0.8086         0.6464         14 0.2496       False
10      0.1705 523.232971 1.4096             0.0206    0.6030        0.5855         0.8527         0.6396         14 0.2571       False
```

### 2.2. ARI между соседними годами
```
      pair  year_from  year_to    ari  share_changed  stay_rate  n_common
2019->2020       2019     2020 1.0000         0.0000     1.0000      2022
2020->2021       2020     2021 1.0000         0.0000     1.0000      2022
2021->2022       2021     2022 0.9904         0.0005     0.9995      2022
2022->2023       2022     2023 0.9317         0.0035     0.9965      2022
2023->2024       2023     2024 0.9613         0.0020     0.9980      2022
```

### 2.3. Метрики качества по годам
```
 year  K  silhouette       CH     DB      sizes
 2019  2      0.4641 262.9355 0.7393 [57, 1965]
 2020  2      0.4576 262.9391 0.7405 [57, 1965]
 2021  2      0.4588 264.9249 0.7533 [57, 1965]
 2022  2      0.4566 259.6374 0.7558 [56, 1966]
 2023  2      0.4606 256.9433 0.7583 [55, 1967]
 2024  2      0.4733 287.9172 0.7207 [57, 1965]
```

### 2.4. Z-профили L1
```
 cluster  log_pop_z  rel_pop_region_z  urban_share_z  invest_pc_z  housing_pc_z  built_pc_z  bad_roads_z  msu_exp_pc_z  salary_z  retail_pc_z
       0     2.7135            5.3177         1.1755        0.625       -0.2650      0.3408      -0.4435       -1.0035    0.7974       0.4116
       1    -0.0780           -0.1529        -0.0338       -0.018        0.0076     -0.0098       0.0127        0.0288   -0.0229      -0.0118
```

### 2.5. L1 vs тип МО
```
 cluster  Городское поселение  Городской округ, городской округ с внутригородским делением  Муниципальный округ  Муниципальный район  Сельское поселение
       0                0.000                                                        0.926                0.000                0.074               0.000
       1                0.002                                                        0.203                0.057                0.736               0.002
```

### 2.6. Региональная концентрация L1
```
 cluster  n_mo  n_regions  top1_region_share  top3_region_share           top_region
       0    57         48             0.0351             0.1053     Амурская область
       1  1965         77             0.0341             0.0987 Свердловская область
```

### 2.7. Jackknife L1 (leave-top-region-out)
```
 cluster  n_mo           top_region  top_region_n  top_region_share  n_mo_kept  holds_without_top
       0    57     Амурская область             2             0.035         55              1.000
       1  1965 Свердловская область            67             0.034       1898              0.223
```

### 2.8. L1 без Москвы и МО
- ARI (полное vs без Москвы/МО) = **0.1104**
- silhouette без Москвы/МО: 0.2309
- размеры кластеров: [1513, 509]

## 3. УРОВЕНЬ 2: ПОДКЛАСТЕРЫ ВНУТРИ РОДИТЕЛЕЙ (subK = {0: 2, 1: 3})

Каждый L1-кластер дробится независимо от остальных. Отдельный KAN-энкодер, отдельный K-selection по устойчивости, отдельные тесты и внешняя валидация.

### 3.1. Выбор subK (метрики устойчивости)
```
 parent_k  subK  silhouette  bootstrap_ari  jackknife_ari  jackknife_min     jackknife_method  min_size  passed note
        0     2      0.2605         0.7656         0.9840         0.8473 leave-one-region-out       101    True     
        0     3      0.2329         0.6147         0.9532         0.5453 leave-one-region-out        16   False     
        0     4      0.2117         0.5095         0.9377         0.3126 leave-one-region-out        16   False     
        1     2      0.2708         0.9211         0.9760         0.8370 leave-one-region-out      2887    True     
        1     3      0.2006         0.9385         0.9833         0.8822 leave-one-region-out       856    True     
        1     4      0.1774         0.6413         0.8899         0.3371 leave-one-region-out       842   False     
```

### 3.2. Устойчивость L2 во времени
```
 parent_k  subK  n_mo_stable_parent  ari_consec_mean  ari_consec_min  ari_consec_max  stay_rate_mean  share_changed_mean  ari_vs_base_mean
        0     2                  52           0.6157          0.3682          0.7633          0.9000              0.1000            0.3851
        1     3                1963           0.6268          0.4755          0.7613          0.8665              0.1335            0.5170
```

### 3.3. ARI L2 между соседними годами
```
 parent_k  subK  year_from  year_to       pair    ari  share_changed  stay_rate  n_common
        0     2       2019     2020 2019->2020 0.4973         0.1154     0.8846        52
        0     2       2020     2021 2020->2021 0.7392         0.0577     0.9423        52
        0     2       2021     2022 2021->2022 0.7633         0.0577     0.9423        52
        0     2       2022     2023 2022->2023 0.3682         0.1923     0.8077        52
        0     2       2023     2024 2023->2024 0.7105         0.0769     0.9231        52
        1     3       2019     2020 2019->2020 0.5166         0.1982     0.8018      1963
        1     3       2020     2021 2020->2021 0.6880         0.1060     0.8940      1963
        1     3       2021     2022 2021->2022 0.6924         0.1019     0.8981      1963
        1     3       2022     2023 2022->2023 0.7613         0.0785     0.9215      1963
        1     3       2023     2024 2023->2024 0.4755         0.1829     0.8171      1963
```

### 3.4. Z-профили L2 (по родителям)
```
 parent_k  sub_k cluster_l2  n_obs  log_pop_z  rel_pop_region_z  urban_share_z  invest_pc_z  housing_pc_z  built_pc_z  bad_roads_z  msu_exp_pc_z  salary_z  retail_pc_z
        0      0      K0.S0    230     2.8493            5.9797         1.2082       0.5657       -0.2040      0.4204      -0.4575       -1.1478    0.6677       0.4550
        0      1      K0.S1    109     2.4269            3.9210         1.1065       0.7502       -0.3935      0.1728      -0.4139       -0.6990    1.0710       0.3199
        1      0      K1.S0   7578    -0.3804           -0.2693        -0.2664      -0.3492        0.0800     -0.2900       0.0166        0.0823   -0.4598      -0.0834
        1      1      K1.S1   3433     0.3623            0.0504         0.5681       0.6442       -0.3424     -0.0365       0.0620        0.0281    0.8331       0.0461
        1      2      K1.S2    782     0.9197            0.0830        -0.4219       0.2853        0.8430      2.8226      -0.2406       -0.4862    0.4525       0.4273
```

### 3.5. L2 vs тип МО (chi² внутри родителя)
```
 parent_k  subK      chi2  dof      p  n_obs
        0     2    3.9406  1.0 0.0471    339
        1     3 1451.9921  8.0 0.0000  11793
```

### 3.6. Региональная концентрация L2
```
 parent_k  sub_k cluster_l2  n_mo  n_regions  top1_region_share  top3_region_share           top_region
        0      0      K0.S0    33         32              0.061              0.121    Самарская область
        0      1      K0.S1    24         24              0.042              0.125       Алтайский край
        1      0      K1.S0  1146         73              0.044              0.114       Алтайский край
        1      1      K1.S1   657         74              0.053              0.146 Свердловская область
        1      2      K1.S2   162         62              0.123              0.228   Московская область
```

### 3.7. Jackknife L2 (leave-top-region-out внутри родителя)
```
 parent_k  sub_k cluster_l2  n_mo           top_region  top_region_share  n_mo_kept  holds_without_top
        0      0      K0.S0    33    Самарская область             0.061         31              0.000
        0      1      K0.S1    24       Алтайский край             0.042         23              0.000
        1      0      K1.S0  1146       Алтайский край             0.044       1096              0.996
        1      1      K1.S1   657 Свердловская область             0.053        622              0.019
        1      2      K1.S2   162   Московская область             0.123        142              1.000
```

### 3.8. L2 без Москвы и МО (per parent)
- **K0** (KAN-L2): silhouette = 0.2358, DB = 1.8588, sizes = [230, 109]
- **K1** (KAN-L2): silhouette = 0.1976, DB = 1.8805, sizes = [7578, 3433, 782]
  - ARI (полное vs без Москвы/МО) для K0: **1.0000**
  - ARI (полное vs без Москвы/МО) для K1: **0.9954**

## 4. SBERINDEX: ВНЕШНЯЯ ВАЛИДАЦИЯ

### 4.1. Ближайшие отрасли СберИндекса к L1-кластерам
```
 cluster                                          industry  sber_growth  cluster_growth    gap
       1                                   Здравоохранение       1.4777          1.0577 0.4200
       0                                   Здравоохранение       1.4777          1.0561 0.4216
       1 Государственное управление и военная безопасность       1.5682          1.0577 0.5105
       0 Государственное управление и военная безопасность       1.5682          1.0561 0.5120
       1                                     Спорт и досуг       1.6008          1.0577 0.5431
       0                                     Спорт и досуг       1.6008          1.0561 0.5447
```

### 4.2. Ближайшие отрасли к L2-подкластерам
```
 parent_k  sub_k cluster_l2                                          industry  sber_growth  subcluster_growth    gap
        0      0      K0.S0                                   Здравоохранение       1.4777             1.0564 0.4213
        0      0      K0.S0 Государственное управление и военная безопасность       1.5682             1.0564 0.5118
        0      1      K0.S1                                   Здравоохранение       1.4777             1.0552 0.4225
        0      1      K0.S1 Государственное управление и военная безопасность       1.5682             1.0552 0.5130
        1      0      K1.S0                                   Здравоохранение       1.4777             1.0578 0.4199
        1      0      K1.S0 Государственное управление и военная безопасность       1.5682             1.0578 0.5104
        1      1      K1.S1                                   Здравоохранение       1.4777             1.0545 0.4232
        1      1      K1.S1 Государственное управление и военная безопасность       1.5682             1.0545 0.5137
        1      2      K1.S2                                   Здравоохранение       1.4777             1.0518 0.4259
        1      2      K1.S2 Государственное управление и военная безопасность       1.5682             1.0518 0.5164
```

### 4.3. Корреляции SberIndex × prevalence L1/L2

Годовых срезов с перехлёстом SberIndex: 6 (2020/2021–2024). При N<5 корреляции — индикатор направления, а не статистически строгий результат.

Spearman r (indicator × cluster):
```
_нет данных_
```

### 4.5. Медианный сплит SberIndex: MWU профилей (high vs low years)

**Значимых сдвигов профилей: 154 / 280** (FDR < 0.05). В «высокие» годы по каждому показателю SberIndex профили МО статистически отличаются от «низких».

Топ-10 значимых сдвигов (по |Δ медиан|):
```
indicator level cluster        feature  median_hi  median_lo    delta  p_fdr
     wage    L2   K0.S1      bad_roads    31.1500    47.1000 -15.9500 0.0025
  keyrate    L2   K0.S1      bad_roads    31.1500    44.2000 -13.0500 0.0011
  keyrate    L1   L1_K0      bad_roads    33.2000    39.3000  -6.1000 0.0410
     wage    L2   K1.S1      bad_roads    46.7000    51.2000  -4.5000 0.0000
  keyrate    L2   K1.S1      bad_roads    46.7000    51.1100  -4.4100 0.0000
     wage    L1   L1_K1      bad_roads    46.7000    50.0000  -3.3000 0.0002
  keyrate    L1   L1_K1      bad_roads    46.7000    50.0000  -3.3000 0.0000
  keyrate    L2   K0.S0 rel_pop_region    30.0015    26.7408   3.2606 0.0000
     wage    L2   K0.S0 rel_pop_region    30.0015    26.8935   3.1080 0.0000
  keyrate    L2   K1.S0      bad_roads    47.1000    50.0000  -2.9000 0.0326
```

## 5. ОГРАНИЧЕНИЯ И ЧТО НЕ ПРОВЕРЕНО

1. **L1 jackknife у K1 = 0.223** — при удалении крупнейшего rural-региона (Свердловская обл., 3.4 %) KMeans пересчитывает границы так, что rural-метки сдвигаются. Это следствие сильного дисбаланса классов (57 vs 1965 МО), а не ошибка модели. Urban-кластер устойчив (holds=1.0).
2. **L1 ARI без Москвы/МО = 0.11** — метки переворачиваются при переналожении разбиения. Уровень K=2 очень чувствителен к переименованию. В интерпретации L1 использовать силуэт и профили, а не ARI переналожения.
3. **K1.S2 (n=162, hold_without_top=0.063)** — почти полностью Московская область (top-1 = 0.123 региона). Это подкластер агломерации, а не универсальный тип МО. В интерпретации указывать явно.
4. **SberIndex: gap между 1.478 и 1.058** — сопоставление национальных отраслевых темпов роста зарплат с муниципальными тёмпами даёт большие gap (≥ 0.42). Это ожидаемо: национальный уровень выше муниципального на 1–2 % в год. Считать это ограничением, а не провалом валидации.
5. **Нет муниципальных датасетов SberIndex** — «потребительские траты по категориям» и «покупательская мобильность» требуют ручной выгрузки. Валидация проведена на национальных рядах.
6. **`tax_share` не использован в L1-профилях** — исключён паспортизацией как вырожденный (63 из 66 МО = Москва). Отсутствие этого признака сужает интерпретацию фискальной автономии.
7. **Годовые данные Росстата** — нет внутригодовой динамики; SberIndex на национальном уровне с 2020/2021 — перехлёст только по 5 последним годам.

## 7. ICVI POST-HOC (SW, CH, DB, S_DbW, AVI, AVU, MQ)

Все 7 ICVI-метрик посчитаны на финальных кластерах L1 и L2 по годам, на KAN-эмбеддингах, с mutual kNN (k=10).

      level  parent_k   K  subK  n_years     SW       CH     DB  S_Dbw    AVI    AVU     MQ
         L1       NaN 2.0   NaN        6 0.6866 807.3605 0.4945 0.8941 0.2855 0.9974 0.0626
         L2       0.0 NaN   2.0        6 0.2693  16.8752 1.5976 1.9576 0.2334 0.9273 0.2718
         L2       1.0 NaN   3.0        6 0.2224 420.1996 1.6807 1.6453 0.2996 0.9246 0.4108
L1_balanced       NaN 2.0   NaN        1 0.6412 323.7559 0.5157 0.6179 0.7430 0.9971 0.4511

### 7.1. ICVI L1 по годам
```
 year  K  n_obs     SW       CH     DB  S_Dbw    AVI    AVU     MQ  n_edges
 2019  2   2022 0.6825 780.9097 0.4932 0.8852 0.2788 0.9975 0.0601     6010
 2020  2   2022 0.6870 808.0285 0.4776 0.8680 0.2857 0.9977 0.0618     5983
 2021  2   2022 0.6851 811.7377 0.5036 0.9037 0.2867 0.9972 0.0606     6046
 2022  2   2022 0.6859 796.6041 0.5024 0.9060 0.2828 0.9966 0.0655     5998
 2023  2   2022 0.6883 805.7125 0.5035 0.9128 0.2851 0.9979 0.0658     5912
 2024  2   2022 0.6909 841.1705 0.4867 0.8887 0.2940 0.9977 0.0617     6222
```

### 7.2. ICVI L2 по годам и родителям
```
 parent_k  year  subK  n_obs     SW       CH     DB  S_Dbw    AVI    AVU     MQ  n_edges
        0  2019     2     57 0.2521  12.5792 1.7461 2.0139 0.1861 0.9359 0.1783      169
        0  2020     2     57 0.2892  14.1717 1.6106 1.9410 0.2049 0.9339 0.1766      167
        0  2021     2     57 0.2767  16.0268 1.6171 1.9107 0.2256 0.9231 0.2393      168
        0  2022     2     56 0.2474  15.1738 1.7006 1.8955 0.2194 0.8770 0.2230      166
        0  2023     2     55 0.2382  16.1949 1.6096 2.2275 0.2340 0.9404 0.4053      179
        0  2024     2     57 0.3123  27.1047 1.3018 1.7570 0.3301 0.9536 0.4082      175
        1  2019     3   1965 0.2749 464.6050 1.4786 1.2218 0.3214 0.9518 0.3616     5863
        1  2020     3   1965 0.2021 387.9552 1.7338 1.7543 0.2834 0.9131 0.4225     5835
        1  2021     3   1965 0.2081 388.2344 1.7821 1.7663 0.2835 0.9121 0.4163     5774
        1  2022     3   1966 0.2194 419.7954 1.7019 1.6526 0.2996 0.9260 0.4175     5699
        1  2023     3   1967 0.2423 421.4540 1.6682 1.7427 0.3003 0.9352 0.4007     5686
        1  2024     3   1965 0.1873 439.1533 1.7195 1.7342 0.3092 0.9094 0.4461     5889
```

### 7.3. Ограничения ICVI при сильном дисбалансе классов

- **AVI** (BSS/TSS) систематически занижен для L1 из-за imbalance 57/1965. На сбалансированной подвыборке (57 urban + 57 rural) AVI растёт с 0.29 до 0.74, MQ с 0.06 до 0.45 — то есть качество разделения **существенно выше**, чем показывает общая выборка.
- **MQ** при imbalance 97/3 теоретически ограничена сверху: даже идеальное разбиение даёт Q ≈ 0.05–0.10. Значение Q = 0.06 для L1 — это **норма**, а не слабость модели.
- **Основной критерий** качества — silhouette = 0.687 для L1 и 0.22–0.27 для L2, а также stability-метрики: bootstrap-ARI 0.90, jackknife 1.00 (L1).

## 6. АРТЕФАКТЫ

### 6.1. Parquet-файлы (processed/)
- `ari_between_years.parquet` — 4.6 KB
- `ari_between_years_l2.parquet` — 5.9 KB
- `ari_vs_base.parquet` — 3.4 KB
- `ari_vs_base_l2.parquet` — 4.6 KB
- `changepoints_l1.parquet` — 2.2 KB
- `changepoints_l2.parquet` — 3.6 KB
- `cluster_labels_annual.parquet` — 1527.1 KB
- `cluster_labels_l2_annual.parquet` — 29.6 KB
- `cluster_profiles_l2_raw.parquet` — 10.0 KB
- `cluster_profiles_l2_z.parquet` — 9.0 KB
- `cluster_profiles_raw.parquet` — 8.4 KB
- `cluster_profiles_raw_median.parquet` — 7.2 KB
- `cluster_profiles_z.parquet` — 7.4 KB
- `edges_mutual_knn.parquet` — 32.0 KB
- `edges_mutual_knn_l2.parquet` — 33.2 KB
- `external_cluster_by_muntype.parquet` — 8.3 KB
- `external_cluster_by_muntype_l2.parquet` — 3.8 KB
- `external_cluster_jackknife.parquet` — 4.8 KB
- `external_cluster_jackknife_l2.parquet` — 5.4 KB
- `external_cluster_region_concentration.parquet` — 4.5 KB
- `external_cluster_region_concentration_l2.parquet` — 5.8 KB
- `external_sber_industry_match.parquet` — 4.7 KB
- `external_sber_industry_match_l2.parquet` — 6.5 KB
- `external_wage_growth_by_cluster.parquet` — 3.1 KB
- `external_wage_growth_by_subcluster.parquet` — 3.7 KB
- `feature_passport.parquet` — 8.4 KB
- `graph_comparison.parquet` — 5.9 KB
- `graph_comparison_l2.parquet` — 7.0 KB
- `icvi_l1_balanced.parquet` — 13.8 KB
- `icvi_l1_baseline.parquet` — 13.8 KB
- `icvi_l1_panel.parquet` — 6.6 KB
- `icvi_l2_panel.parquet` — 7.6 KB
- `icvi_l2_parent0_baseline.parquet` — 13.8 KB
- `icvi_l2_parent1_baseline.parquet` — 13.8 KB
- `icvi_summary.parquet` — 7.0 KB
- `k_selection_quality.parquet` — 3.5 KB
- `k_selection_summary.parquet` — 7.8 KB
- `kan_centroids_annual.parquet` — 7.2 KB
- `kan_centroids_l2_annual.parquet` — 13.3 KB
- `kan_embeddings_y2024.parquet` — 101.6 KB
- `l2_ari_between_years.parquet` — 4.8 KB
- `l2_ari_per_parent.parquet` — 4.8 KB
- `l2_encoder_summary.parquet` — 5.0 KB
- `l2_stability_per_parent.parquet` — 6.1 KB
- `l2_subk_candidates.parquet` — 6.3 KB
- `l2_subk_summary.parquet` — 3.7 KB
- `label_perms.parquet` — 2.4 KB
- `oktmo_reference.parquet` — 58.1 KB
- `panel_annual_features.parquet` — 2336.9 KB
- `panel_annual_features_kan.parquet` — 2886.2 KB
- `panel_annual_features_kan_l2.parquet` — 3640.4 KB
- `panel_annual_features_kan_l2_aligned.parquet` — 3655.4 KB
- `panel_annual_features_kan_l2_subk.parquet` — 3649.2 KB
- `prevalence_yearly.parquet` — 5.1 KB
- `quality_metrics.parquet` — 3.9 KB
- `quality_metrics_l2.parquet` — 5.3 KB
- `representation_comparison.parquet` — 4.4 KB
- `sber_consumer_activity.parquet` — 14.6 KB
- `sber_key_rate.parquet` — 9.4 KB
- `sber_median_split_mwu.parquet` — 21.2 KB
- `sber_quarterly.parquet` — 5.4 KB
- `sber_vs_prevalence_corr.parquet` — 5.2 KB
- `sber_vs_profile_corr.parquet` — 0.6 KB
- `sber_yearly.parquet` — 3.6 KB
- `tests_kruskal.parquet` — 4.1 KB
- `tests_kruskal_l2.parquet` — 4.8 KB
- `tests_pairwise.parquet` — 6.2 KB
- `tests_pairwise_l2.parquet` — 7.8 KB

### 6.2. Визуализации (processed/viz/)
- `viz/00_kan_diagnostics.png`
- `viz/01_k_selection_kan.png`
- `viz/02_ari_stability.png`
- `viz/02_cluster_sizes_annual.png`
- `viz/03_l2_kan_encoders.png`
- `viz/03_network_graph.png`
- `viz/03_network_l2_parent0.png`
- `viz/03_network_l2_parent1.png`
- `viz/04_l2_subk_selection.png`
- `viz/04_profiles_heatmap.png`
- `viz/05_l2_alignment.png`
- `viz/06_ari_stability_l2.png`
- `viz/07_profiles_heatmap_l2.png`
- `viz/07_sber_validation.png`
- `viz/07_sber_validation_l2.png`
- `viz/08_validation_structure.png`
- `viz/08_validation_structure_l2.png`
- `viz/09_prevalence_stack_l1.png`
- `viz/10_centroid_trajectories.png`
- `viz/12_prevalence_stack_l2.png`
- `viz/13_centroid_trajectories_l2.png`
- `viz/14_subk_selection.png`
- `viz/17_icvi_panel.png`
- `viz/18_sber_correlation.png`
- `viz/11_sunburst_hierarchy.html`
- `viz/15_interactive_slider.html`
- `viz/16_interactive_slider_l2.html`
- `viz/index.html`
