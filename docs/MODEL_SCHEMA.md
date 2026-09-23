# Retail Gadget — Semantic Model Schema (Dummy Data)

Dataset dummy untuk uji coba **Power BI Report Authoring Skill** via Claude Code.
Star schema: **1 fact + 5 dimensi**. Mata uang **IDR**. Rentang **2023-01-01 s/d 2025-12-31**.

> Taruh file ini di dalam folder proyek PBIP agar agent punya konteks lengkap
> tentang struktur model (nama tabel, kolom, tipe, relasi, dan measure).

---

## Tabel

### fact_sales (tabel fakta — 12.000 baris)
| Kolom | Tipe | Keterangan |
|---|---|---|
| SalesOrderID | Text | ID order (PK teknis) |
| DateKey | Whole Number | FK → dim_date[DateKey] (format yyyymmdd) |
| ProductKey | Whole Number | FK → dim_product[ProductKey] |
| CustomerKey | Whole Number | FK → dim_customer[CustomerKey] |
| RegionKey | Whole Number | FK → dim_region[RegionKey] (region penjualan = region customer) |
| SalespersonKey | Whole Number | FK → dim_salesperson[SalespersonKey] |
| Quantity | Whole Number | Jumlah unit |
| UnitPrice | Whole Number | Harga satuan (IDR) |
| DiscountPct | Decimal | Diskon (0–0.15), simpan sebagai pecahan |
| SalesAmount | Whole Number | Penjualan bersih setelah diskon (IDR) |
| COGS | Whole Number | Harga pokok penjualan (IDR) |
| Profit | Whole Number | SalesAmount − COGS (IDR) |

### dim_date (1.096 baris)
DateKey (PK, yyyymmdd) · Date · Year · Quarter · MonthNumber · MonthName · MonthYear · Day · DayName · WeekOfYear · IsWeekend
> Di Power BI, **Mark as Date Table** pakai kolom `Date`.

### dim_product (20 baris)
ProductKey (PK) · ProductName · Category · SubCategory · UnitCost · UnitPrice
Kategori: Smartphone, Tablet, Wearable, Audio, Accessory, Networking, Smart Home.

### dim_customer (300 baris)
CustomerKey (PK) · CustomerName · Segment · RegionKey · JoinDate
Segment: Consumer, SME/UMKM, Corporate.

### dim_region (20 baris)
RegionKey (PK) · City · Province · Island · Latitude · Longitude
> `Latitude`/`Longitude` untuk visual **Map**; set Data Category = Latitude/Longitude, City = City.

### dim_salesperson (12 baris)
SalespersonKey (PK) · SalespersonName · Team
Team: Jawa & Bali, Sumatera, Indonesia Timur.

---

## Relasi (semua single-direction, satu-ke-banyak, dari dim → fact)

```
dim_date[DateKey]            1 --- * fact_sales[DateKey]
dim_product[ProductKey]      1 --- * fact_sales[ProductKey]
dim_customer[CustomerKey]    1 --- * fact_sales[CustomerKey]
dim_region[RegionKey]        1 --- * fact_sales[RegionKey]
dim_salesperson[SalespersonKey] 1 --- * fact_sales[SalespersonKey]
```
Catatan: dim_customer juga punya RegionKey. Untuk analisis by-region, gunakan
`fact_sales[RegionKey] → dim_region` (relasi aktif). Jangan buat relasi ganda
customer→region yang bikin ambiguitas; cukup lewat fact.

---

## Measure DAX yang disarankan (buat tabel _Measures)

```dax
Total Sales      = SUM ( fact_sales[SalesAmount] )
Total Profit     = SUM ( fact_sales[Profit] )
Total COGS       = SUM ( fact_sales[COGS] )
Total Orders     = DISTINCTCOUNT ( fact_sales[SalesOrderID] )
Total Quantity   = SUM ( fact_sales[Quantity] )
Profit Margin %  = DIVIDE ( [Total Profit], [Total Sales] )
Avg Order Value  = DIVIDE ( [Total Sales], [Total Orders] )

Sales YTD        = TOTALYTD ( [Total Sales], dim_date[Date] )
Sales PY         = CALCULATE ( [Total Sales], SAMEPERIODLASTYEAR ( dim_date[Date] ) )
Sales YoY %      = DIVIDE ( [Total Sales] - [Sales PY], [Sales PY] )
```

---

## Ide layout executive dashboard (buat prompt ke agent)

1. **KPI cards** baris atas: Total Sales, Total Profit, Profit Margin %, Total Orders (+ YoY%).
2. **Line chart**: Total Sales per MonthYear (tren + musiman terlihat jelas).
3. **Bar chart**: Total Sales per Category (atau SubCategory).
4. **Map**: SalesAmount per City (pakai Latitude/Longitude).
5. **Donut/bar**: kontribusi per Segment.
6. **Table/bar**: Top salesperson atau Top product by Profit.
7. **Slicer**: Year, Quarter, Island, Segment.
