# Deney Sonu Teslimatı

Sistem Programlama ve Veri Yapıları bakış açısıyla veri tabanlarındaki performansı öne çıkaran hususlar nelerdir?

Aşağıda kutucuk (checkbox) ile gösterilen maddelerden en az birini seçtiğiniz açık kaynak kodlu bir VT kaynak kodları üzerinde göstererek açıklayınız. Açıklama bölümüne kısaca metninizi yazıp, kod üzerinde gösterim videonuzun linkini en altta belirtilen kutucuğa yerleştiriniz.

- [X]  Seçtiğiniz konu/konuları bu şekilde işaretleyiniz. **!**
    
---

# 1. Sistem Perspektifi (Operating System, Disk, Input/Output)

### Disk Erişimi

- [X]  **Blok bazlı disk erişimi** → block_id + offset
- [ ]  Rastgele erişim

### VT için Page (Sayfa) Anlamı

- [X]  VT hangisini kullanır? **Satır/ Sayfa** okuması

---

### Buffer Pool

- [X]  Veritabanları, Sık kullanılan sayfaları bellekte (RAM) kopyalar mı (caching) ?

- [ ]  LRU / CLOCK gibi algoritmaları
- [ ]  Diske yapılan I/O nasıl minimize ederler?

# 2. Veri Yapıları Perspektifi

- [ ]  B+ Tree Veri Yapıları VT' lerde nasıl kullanılır?
- [X]  VT' lerde hangi veri yapıları hangi amaçlarla kullanılır?
- [ ]  Clustered vs Non-Clustered Index Kavramı
- [ ]  InnoDB satırı diskte nasıl durur?
- [ ]  LSM-tree (LevelDB, RocksDB) farkı
- [ ]  PostgreSQL heap + index ayrımı

DB diske yazarken:

- [X]  WAL (Write Ahead Log) İlkesi
- [ ]  Log disk (fsync vs write) sistem çağrıları farkı

---

# Özet Tablo

| Kavram      | Bellek          | Disk / DB      |
| ----------- | --------------- | -------------- |
| Adresleme   | Pointer         | Page + Offset  |
| Hız         | O(1)            | Page IO        |
| PK          | Yok             | Index anahtarı |
| Veri yapısı | Array / Pointer | B+Tree         |
| Cache       | CPU cache       | Buffer Pool    |

---

# Video [Linki](https://www.youtube.com/watch?v=Nw1OvCtKPII&t=2635s) 
Ekran kaydı. 2-3 dk. açık kaynak V.T. kodu üzerinde konunun gösterimi. Video kendini tanıtma ile başlamalıdır (Numara, İsim, Soyisim, Teknik İlgi Alanları). 

---

# Açıklama (Ort. 600 kelime)

1. Sistem Perspektifi: Disk, I/O ve Sayfa Yönetimi
  
   Veritabanı yönetim sistemleri (VTYS), işletim sisteminin dosya yönetimini basit bir veri saklama alanı olarak değil, optimize edilmesi gereken düşük seviyeli bir donanım etkileşimi olarak görür. Taslakta belirtilen Blok Bazlı Disk Erişimi, modern disklerin (özellikle HDD ve SSD) veriyi tekil baytlar yerine sabit boyutlu bloklar halinde okuması gerçeğine dayanır.

Sayfa (Page) Yapısı ve Mekansal Yerellik

İşletim sistemleri genellikle 4KB bloklar kullanırken, PostgreSQL gibi sistemlerin varsayılan 8KB, MySQL InnoDB’nin ise 16KB boyutunda sayfalar kullanması bir mühendislik tercihidir. Bir veritabanı tek bir satırı sorguladığında neden tüm sayfayı okur?. Bunun temel sebebi Spatial Locality (Mekansal Yerellik) ilkesidir. Eğer $n$ numaralı satıra ihtiyaç duyuluyorsa, muhtemelen $n+1$ numaralı satıra da kısa süre sonra ihtiyaç duyulacaktır. Sayfa okuması sayesinde, disk kafasının (HDD) veya kontrolcüsünün (SSD) fiziksel hareket maliyeti bir kez ödenir ve ilgili veri kümesi RAM'e taşınır. Adresleme aşamasında kullanılan block_id + offset (dosya içindeki sayfa numarası ve sayfa içindeki satır sırası) yapısı, karmaşık dosya sistemi arama algoritmalarını devre dışı bırakarak doğrudan donanım adresine yakın bir erişim sağlar.

Buffer Pool ve Bellek Yönetimi Stratejileri

Veritabanının "bellek içi çalışma alanı" olan Buffer Pool, diske yapılan I/O operasyonlarını minimize etmenin anahtarıdır. Bellek yönetimi sadece bir kopyalama (caching) işlemi değildir; aynı zamanda bir yönetim sanatıdır.
- LRU ve CLOCK Algoritmaları: Bellek dolduğunda hangi sayfanın atılacağına karar vermek kritiktir. Standart bir LRU yerine çoğu veritabanı, "sequential scan" (sıralı tarama) işlemlerinin cache'i kirletmesini önlemek için özelleşmiş LRU-K veya CLOCK algoritmalarını kullanır.
- Dirty Pages: RAM üzerinde güncellenen ancak henüz diske yazılmamış sayfalara "dirty page" denir. Buffer Pool, bu sayfaları toplu halde (batching) diske yazarak (Checkpointing) rastgele yazma maliyetini düşürür.

2. Veri Yapıları Perspektifi ve Kalıcılık
Veritabanlarında performans, verinin diskte nasıl dizildiği (Storage Layout) ile doğrudan bağlantılıdır.

B+ Tree ve İndeksleme Mekanizması
İlişkisel veritabanları (RDBMS), arama işlemlerini hızlandırmak için neden standart Binary Tree yerine B+ Tree kullanır?. Binary Tree yapılarında düğüm başına bir veri düşerken, B+ Tree sayfalarla (pages) tam uyumlu çalışır; bir düğüm yüzlerce anahtar barındırabilir. Bu, milyarlarca satırlık bir tabloda bile veriye ulaşmak için sadece 3-4 katman (level) inilmesini sağlar, bu da doğrudan 3-4 sayfa okuması (disk I/O) demektir.

Farklı Yaklaşımlar: PostgreSQL Heap vs. MySQL Clustered Index

Burada önemli bir mimari fark ortaya çıkar:
- PostgreSQL: Veriyi "Heap" dosyalarında sırasız tutar. İndeksler, verinin fiziksel adresini (TID - Tuple ID) işaret eder.
- MySQL (InnoDB): Verinin kendisini doğrudan anahtarın (Primary Key) yaprağında tutar (Clustered Index). Bu, PK üzerinden yapılan aramalarda ek bir disk erişimini ortadan kaldırır.

Write Ahead Log (WAL) ve Veri Bütünlüğü
Veritabanı diske yazarken "önce günlüğü tut, sonra veriyi işle" prensibini izler. WAL, rastgele I/O'yu sıralı I/O'ya (Sequential I/O) dönüştüren sihirli bir dokunuştur.
1. Hız: Veri dosyasının farklı yerlerine 10 farklı satır yazmak yerine, bu 10 değişikliği içeren tek bir log kaydı disk sonuna eklenir (append-only).
2. Güvenlik: Elektrik kesintisi gibi durumlarda, Buffer Pool'daki "dirty page"ler kaybolsa bile, diskteki WAL kayıtları okunarak veri tabanı tutarlı hale getirilir (Recovery).

### Özet ve Karşılaştırma Tablosu

| Özellik | Geleneksel Bellek Yönetimi | Veritabanı (VTYS) Yaklaşımı |
| :--- | :--- | :--- |
| **Birim** | Byte / Word | Page (8KB / 16KB) |
| **Kalıcılık** | Yok (Uçucu) | WAL ve Checkpoint ile Tam Kalıcılık |
| **Veri Yapısı** | Pointer tabanlı Listeler | B+ Tree / LSM Tree |
| **Arama Maliyeti** | $O(1)$ veya $O(\log n)$ CPU | Disk Seek / Page I/O Odaklı |
| **Adresleme** | Bellek Adresi (Pointer) | Page ID + Offset |
| **Önbellek** | L1/L2/L3 CPU Cache | Buffer Pool (RAM) |


## VT Üzerinde Gösterilen Kaynak Kodları

Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
... \
...
