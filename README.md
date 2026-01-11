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

1. Sistem Perspektifi: Disk ve Bellek Yönetimi

Blok Bazlı Disk Erişimi ve Sayfa (Page) Kavramı

Geleneksel dosya sistemlerinin aksine veritabanları, diski rastgele bir veri yığını olarak değil, belirli büyüklükteki Sayfalar (Pages) bütünü olarak görür. İşletim sistemi seviyesinde disk erişimi genellikle 4KB'lık bloklar halinde yapılırken, veritabanları (örneğin PostgreSQL) varsayılan olarak 8KB'lık sayfalar kullanır.
  
- Neden Sayfa Okuması? Veritabanı tek bir satırı okumak istediğinde bile, o satırın bulunduğu tüm sayfayı belleğe getirir. Bu, "Spatial Locality" (Mekansal Yerellik) prensibinden faydalanarak, komşu satırlara erişimi hızlandırır.
- Adresleme: Disk üzerindeki bir veriye erişmek için block_id + offset yapısı kullanılır. Bu, veritabanı motorunun dosya sistemi içinde doğrudan ilgili koordinata gitmesini sağlayarak CPU döngülerini korur.

Buffer Pool Mekanizması

Veritabanlarının performans kalbi Buffer Pool'dur. Disk erişimi, RAM erişimine göre binlerce kat daha yavaştır. Bu farkı kapatmak için veritabanı, diskin bir kopyasını RAM üzerinde tutar.
- Caching ve I/O Minimizasyonu: Bir sorgu geldiğinde motor önce Buffer Pool'a bakar. Eğer sayfa RAM'deyse (Buffer Hit), disk erişimine gerek kalmaz.
- Veri Yapısı: Buffer Pool genellikle bir Hash Table ve bir LRU (Least Recently Used) listesi ile yönetilir. Sık kullanılan sayfalar "hot" olarak işaretlenir ve bellekte tutulur, nadir kullanılanlar ise diske geri yazılarak (eviction) yer açılır.

2. Veri Yapıları ve Kalıcılık

Veritabanlarında Veri Yapıları

Performansın yazılım tarafındaki anahtarı doğru veri yapısı seçimidir. İlişkisel veritabanlarında arama maliyetini $O(\log n)$ seviyesinde tutmak için B+ Tree yapıları standarttır. B+ Tree'lerin, standart Binary Tree'lere göre avantajı, düğümlerin çok sayıda çocuk barındırabilmesidir; bu da ağacın boyunu kısaltarak disk üzerindeki "seek" (arama) operasyonunu azaltır.

Write Ahead Log (WAL) İlkesi

Bir veritabanı için en maliyetli ve riskli işlem "yazma" işlemidir. Bir veri güncellendiğinde, verinin asıl dosyasının (data file) rastgele bir konumuna gidip yazmak çok yavaştır. Bunun yerine WAL mekanizması devreye girer.
- Sıralı Yazma: Yapılan değişiklik önce "Append-only" (sadece sona eklenen) bir log dosyasına yazılır. Diskler sıralı yazmada (Sequential Write), rastgele yazmaya göre çok daha hızlıdır.
- Atomisite ve Kurtarma: Eğer sistem o an çökerse, RAM'deki veriler silinse bile, WAL kayıtları sayesinde veritabanı yeniden başlatıldığında işlemleri tekrar uygulayarak (Redo) tutarlılığı sağlar.

## VT Üzerinde Gösterilen Kaynak Kodları

Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
... \
...
