# Ödev 2: Constraint'ler, ihlal testleri ve ilişkisel cebir

**Son teslim:** 25 Ekim 2026 Pazar, 23:59
**Teslim:** Ödev 1 ile birlikte, tek Pull Request (`week-05` → `main`)
**Ağırlık:** Altı ödevden biri. Ödevler toplam notun %20'si; en düşük not atılır.
**Tahmini süre:** 3-4 saat

## Neden bu ödev

Ödev 1'de tablolarını kurdun. Ama bir tablonun var olması, içine saçma veri girmesini engellemez. Şu an şemana bitiş tarihi başlangıçtan önce olan bir etkinlik, eksi fiyatlı bir ürün ya da var olmayan bir üyeye ait bir kayıt yazılabiliyor olabilir.

İyi bir veritabanı, yanlış veriyi kapıda geri çevirir. Bu ödevde iki şey yapacaksın. Önce iş kurallarını constraint olarak şemana yazacak, sonra her birini bilerek bozmaya çalışacaksın. Ardından aynı şema üzerinde ilişkisel cebirle sorular sorup her birini SQL'e çevireceksin. Cebir, SQL'in arkasındaki matematik; bir sorgunun neden doğru ya da yanlış sonuç verdiğini bu dille görürsün.

## Klasör

Ödev 1'in üzerine, aynı `week-05` branch'inde şu klasörü ekle:

```
week-05/
├── README.md               çalıştırma sırası ve kısa notların
├── constraints.sql         Bölüm A
├── violations.sql          Bölüm B
├── violations_output.txt   Bölüm B'nin psql çıktısı
├── on_delete.md            Bölüm C
├── seed.sql                Bölüm D için küçük veri seti
├── algebra.md              Bölüm D: cebir, hesap ve sonuçlar
└── algebra.sql             Bölüm D: SQL karşılıkları
```

`AI_LOG.md` repo kökünde kalır; içine `## Ödev 2` başlığı açarsın.

---

## Bölüm A: Constraint'ler (`constraints.sql`)

`schema.sql`'i baştan yazmıyorsun. Bu dosya `schema.sql`'den sonra çalışır ve kuralları `ALTER TABLE ... ADD CONSTRAINT` ile ekler.

**En az şunlar olsun:**

- Boş kalmaması gereken her sütunda `NOT NULL`.
- En az iki `UNIQUE`. Biri birden çok sütunlu olsun. M:N ara tablon iyi bir aday: bir üye aynı etkinliğe iki kez kaydolamamalı.
- En az üç `CHECK`:
  - biri tek sütunda aralık: `capacity > 0`, `rating BETWEEN 1 AND 5`
  - biri izin verilen değerler: `status IN ('active', 'suspended', 'closed')`
  - biri **iki sütunu karşılaştıran** kural: `end_date >= start_date`, `min_age <= max_age`
- Bütün foreign key'lerin bir adı olsun. Ödev 1'de isimsiz yazdıysan drop edip isimli olarak yeniden ekle.

**İsim ver.** PostgreSQL hata verdiğinde mesajda constraint'in adı görünür. İyi bir ad, hatanın neden çıktığını okuyana hemen söyler.

| Tür | Kalıp | Örnek |
|---|---|---|
| CHECK | `chk_<tablo>_<kural>` | `chk_event_date_order` |
| UNIQUE | `uq_<tablo>_<sütunlar>` | `uq_registration_member_event` |
| FOREIGN KEY | `fk_<çocuk>_<ebeveyn>` | `fk_registration_member` |

**Kuralı Türkçe yaz.** Her constraint'in üstüne, koruduğu kuralı bir cümleyle yorum olarak ekle. SQL bilmeyen biri de ne korunduğunu anlayabilmeli.

```sql
-- Bir etkinlik başladığı günden önce bitemez.
ALTER TABLE event
  ADD CONSTRAINT chk_event_date_order
  CHECK (end_date >= start_date);
```

---

## Bölüm B: İhlal testleri (`violations.sql`, `violations_output.txt`)

Yazdığın her kuralı bilerek bozmaya çalış. Test edilmemiş bir constraint'in gerçekten çalıştığından emin olamazsın.

**En az 10 test:**

- Bölüm A'daki her constraint için en az bir ihlal.
- Foreign key'i iki yönden dene:
  1. olmayan bir üyeye kayıt eklemek (`INSERT`)
  2. kaydı olan bir üyeyi silmek ya da id'sini değiştirmek (`DELETE` veya `UPDATE`)
- En az iki **sınır testi**: kurala tam sınırda uyan ve kabul edilmesi gereken değer. `capacity > 0` için `capacity = 1`; tarih kuralı için aynı gün başlayıp biten etkinlik.
- Bir **NULL testi**: `CHECK`'i olan ama `NOT NULL` olmayan bir sütuna `NULL` ekle. Ne olduğunu `README.md`'de bir iki cümleyle açıkla. Sonuç seni şaşırtabilir.

**Her test aynı biçimde.** Üstünde test numarası, hedef constraint ve beklenen sonuç olsun. Beklenen sonuç ya PostgreSQL hata kodu (SQLSTATE) ya da `OK`.

```sql
-- T03 | chk_event_date_order | Beklenen: 23514
INSERT INTO event (title, start_date, end_date)
VALUES ('Spring Cup', '2026-04-10', '2026-04-08');

-- T04 | chk_event_date_order | Beklenen: OK (sınır: aynı gün)
INSERT INTO event (title, start_date, end_date)
VALUES ('One Day Workshop', '2026-04-10', '2026-04-10');
```

Karşılaşacağın kodlar:

| SQLSTATE | Adı | Ne zaman |
|---|---|---|
| `23502` | not_null_violation | `NOT NULL` sütuna `NULL` |
| `23503` | foreign_key_violation | foreign key'in iki yönü |
| `23505` | unique_violation | tekrar eden değer |
| `23514` | check_violation | `CHECK` kuralı `false` döndüğünde |

**Dosya her çalıştırmada aynı sonucu versin.** En üste şu satırı koy:

```sql
\set VERBOSITY verbose
```

Bu ayar her hata mesajına SQLSTATE kodunu ekler, böylece beklenenle gerçekleşeni yan yana görürsün. Hemen altına küçük bir hazırlık bloğu ekle: ilgili tabloları `TRUNCATE ... RESTART IDENTITY CASCADE` ile boşalt, testlerin ihtiyaç duyduğu birkaç satırı ekle.

**Output'u al:**

```bash
psql -d come381 -f week-05/violations.sql > week-05/violations_output.txt 2>&1
```

`violations_output.txt`'yi elle düzenleme. Beklenen ile gerçekleşen farklıysa constraint'ini ya da testini düzelt ve yeniden çalıştır. Çözemediysen farkı `README.md`'de anlat. Açıkça anlatılmış bir sorun, sessizce silinmiş bir testten daha çok puan alır.

---

## Bölüm C: Silme politikası (`on_delete.md`)

Bir üye silindiğinde onun kayıtlarına ne olacağına sen karar veriyorsun. Teknik bir ayar gibi görünür ama aslında bir iş kararıdır.

Şemandaki **her foreign key** için şu tabloyu doldur:

| Foreign key | Ebeveyn → çocuk | Seçimin | Neden | Diğer seçenek neden olmadı |
|---|---|---|---|---|
| `fk_registration_member` | member → event_registration | `CASCADE` | Üye silinince kayıtlarının anlamı kalmıyor | `RESTRICT` üyeyi silmeyi imkânsız kılardı |

- En az **iki farklı politika** kullan: `CASCADE`, `RESTRICT` / `NO ACTION`, `SET NULL`, `SET DEFAULT`.
- `SET NULL` seçtiysen o sütunun `NOT NULL` olmadığından emin ol, yoksa silme yine hata verir.
- `CASCADE` seçtiğin bir foreign key için kanıt göster: silmeden önce ve sonra iki tablodaki satır sayısı (`SELECT count(*) ...`). Komutları ve sonucu bu dosyaya yapıştır.
- Bir cümleyle cevapla: `RESTRICT` ile `NO ACTION` arasındaki fark ne? PostgreSQL dokümantasyonundaki "Foreign Keys" bölümünde bulabilirsin.

---

## Bölüm D: İlişkisel cebir ve hesap (`algebra.md`, `algebra.sql`, `seed.sql`)

Kendi senaryonda altı soru yaz. Her soru için dört şey ver:

1. Sorunun Türkçesi
2. İlişkisel cebir ifadesi
3. SQL karşılığı (`algebra.sql`'de, soru numarasıyla)
4. Çalıştırınca dönen sonuç (en fazla 10 satır)

| # | İşlem | Örnek soru |
|---|---|---|
| D1 | σ selection ve π projection | "Kapasitesi 50'den büyük etkinliklerin adları" |
| D2 | ⋈ join (natural veya koşullu) | "Her kaydın üye adı ve etkinlik adı" |
| D3 | ∪ union veya ∩ intersection | "Ya turnuvaya ya atölyeye katılmış üyeler" |
| D4 | − difference | "Hiçbir etkinliğe kaydolmamış üyeler" |
| D5 | ρ rename ile tablonun kendisiyle join'i | "Aynı şehirde yaşayan farklı üye çiftleri" |
| D6 | ÷ division | "Spor kategorisindeki **tüm** etkinliklere katılmış üyeler" |

**D4'ü iki yoldan yaz:** `EXCEPT` ile ve `NOT EXISTS` ile. İkisi aynı sonucu vermeli.

**D6 için:** SQL'de division operatörü yok. Ya iç içe iki `NOT EXISTS` ile ya da `GROUP BY ... HAVING COUNT(...) = (...)` ile yaz. Hangisini neden seçtiğini bir cümleyle söyle.

**İlişkisel hesap:** D1 ya da D4'ü tuple relational calculus ile de yaz:

```
{ t.title | event(t) ∧ t.capacity > 50 }
```

**Küme mi, çanta mı?** İlişkisel cebir kümelerle çalışır; sonuçta aynı satır iki kez olmaz. SQL ise tekrara izin verir. Her soru için bir cümle yaz: bu sorguda `DISTINCT` gerekli mi, değilse neden?

**Gösterim.** Sembolleri (σ π ⋈ ∪ ∩ − ρ ÷) doğrudan yazabilir ya da GitHub'ın desteklediği `$...$` matematik gösterimini kullanabilirsin. Okunur olsun yeter:

```
π title ( σ capacity>50 (event) )
```

**Veri (`seed.sql`).** Her tabloya 5-15 satır ekle ve veriyi sorulara göre kur: D4 ve D6 ne boş dönmeli ne de bütün tabloyu döndürmeli. D6 için en az bir "neredeyse" aday koy: spor etkinliklerinin hepsine değil, birine eksik katılmış bir üye. Doğru sorgu onu elemeli.

---

## Bölüm E: Yapay zeka (`AI_LOG.md`, `## Ödev 2`)

1. Kullandığın bir yapay zeka aracından D6 (division) sorusunun SQL'ini iste. Prompt'u aynen yaz.
2. Gelen sorguyu kendi verinde çalıştır, kendi sonucunla karşılaştır.
3. Yanlışsa hatanın nerede olduğunu göster.
4. Doğruysa onu yanıltacak bir veri durumu ara ve `seed.sql`'e ekle. İpucu: `COUNT` ile yazılmış division sorguları, bir satır iki kez sayıldığında ya da spor dışı kayıtlar da sayıma girdiğinde şaşırabilir.
5. Ödevin başka bir yerinde yapay zeka kullandıysan onu da aynı düzende ekle.

---

## `week-05/README.md`

Kısa tut, üç şey yeter:

1. Çalıştırma sırası:

```bash
psql -d come381 -f sql/schema.sql
psql -d come381 -f week-05/constraints.sql
psql -d come381 -f week-05/violations.sql
psql -d come381 -f week-05/seed.sql
psql -d come381 -f week-05/algebra.sql
```

   Sıra önemli: `violations.sql` tabloları boşaltarak başladığı için `seed.sql` ondan sonra gelir. Windows'ta komutlara `-U postgres` ekle.

2. Bölüm B'deki NULL testinin açıklaması.
3. Beklediğinden farklı çıkan bir şey ve ondan ne öğrendiğin (iki üç cümle).

---

## Teslim

Ödev 1 ve Ödev 2 tek Pull Request:

```bash
git push -u origin week-05
gh pr create --base main --head week-05 --title "Ödev 1 + Ödev 2"
```

PR açıklamasına şu listeyi yapıştır ve işaretle:

```markdown
## Ödev 1
- [ ] docs/er.md GitHub'da çizim olarak görünüyor
- [ ] sql/schema.sql temiz bir veritabanında hatasız çalışıyor
- [ ] README'de senaryo ve bir tasarım kararı var

## Ödev 2
- [ ] constraints.sql hatasız çalışıyor, her constraint'in adı ve yorumu var
- [ ] violations.sql en az 10 test, 2 sınır testi, 1 NULL testi içeriyor
- [ ] violations_output.txt psql'den alındı, elle düzenlenmedi
- [ ] on_delete.md her foreign key'i kapsıyor, en az iki farklı politika var
- [ ] algebra.md'de altı soru, bir hesap ifadesi ve DISTINCT notları var
- [ ] AI_LOG.md'de "Ödev 2" başlığı ve D6 denemesi var
```

`gizemturker`'ı reviewer olarak ekle. PR'ı merge etme; yorumlarımı gördükten sonra kendin merge edeceksin.

Teslim zamanı, PR'daki son commit'in zamanıdır. Her şeyi tek commit'te göndermek yerine işi parça parça commit'le; dönem boyunca commit geçmişine bakılır.

---

## Değerlendirme (100 puan)

| Bölüm | Ölçüt | Puan |
|---|---|---|
| A | Kapsam: `NOT NULL`, çok sütunlu `UNIQUE`, üç tür `CHECK`, isimli foreign key'ler | 8 |
| A | Constraint'ler gerçekten iş kuralını koruyor | 8 |
| A | İsimler ve kural yorumları | 4 |
| B | Her constraint için ihlal, foreign key'in iki yönü, doğru SQLSTATE | 12 |
| B | Sınır testleri | 4 |
| B | NULL testi ve açıklaması | 4 |
| C | Her foreign key için gerekçeli seçim, en az iki farklı politika | 10 |
| C | CASCADE kanıtı ve `RESTRICT` / `NO ACTION` farkı | 5 |
| D | Altı soru: doğru cebir, doğru SQL, sonuç (her biri 4) | 24 |
| D | İlişkisel hesap ifadesi | 3 |
| D | DISTINCT notları ve D4'ün iki yolu | 3 |
| E | D6 denemesi ve belgelenmiş yapay zeka hatası | 10 |
| | README, PR düzeni, commit geçmişi | 5 |
| | **Toplam** | **100** |

README'deki komutlarla çalışmayan bir dosya, kendi bölümünün en fazla yarısını alır.

## Sık sorulanlar

**Senaryomda tarih yok. İki sütunlu CHECK'i nasıl yazacağım?**
Tarih tek örnek değil: `min_capacity <= max_capacity`, `discount_price <= list_price`, `delivered_qty <= ordered_qty`.

**Division sorusu senaryoma uymuyor.**
"Tümü" diye kurulabilen her soru division sorusudur: tüm modülleri bitirmiş kullanıcılar, tüm bedenlerde stoğu olan ürünler, tüm haftalarda antrenmana gelmiş sporcular. Yine de takılırsan erken haber ver: repoda Issue aç ya da danışma saatine gel.

**Birden çok tabloya yayılan bir kuralım var, CHECK ile yazamıyorum.**
Doğru tespit. `CHECK` yalnızca kendi satırına bakar. SQL standardındaki `CREATE ASSERTION` bunun için var ama PostgreSQL desteklemiyor. Bu tür kuralları Hafta 8'de trigger ile yazacağız. Şimdilik kuralı `README.md`'ye not et; fark etmiş olman puan alır.

**Dörtten fazla tablom var. Hepsine constraint mı yazacağım?**
`NOT NULL` ve foreign key'ler için evet. `UNIQUE` ve `CHECK`'te en az sayıları tutturman yeterli ama şemanın gerçekten ihtiyaç duyduğu bir kuralı atlama.

**Yapay zeka sorguyu ilk seferde doğru yazdı. Hata bulamazsam ne olacak?**
Bölüm E'nin 4. adımı bunun için var. Doğru görünen bir sorguyu yanıltacak veriyi bulmak, yanlış bir sorguyu fark etmekten daha değerli bir beceri.

## Okuma

- Elmasri & Navathe, *Fundamentals of Database Systems*: ilişkisel model ve constraint'ler; ilişkisel cebir ve hesap bölümleri
- Silberschatz, Korth & Sudarshan, *Database System Concepts*: ilişkisel model ve ilişkisel cebir bölümleri
- PostgreSQL dokümantasyonu, Constraints: https://www.postgresql.org/docs/current/ddl-constraints.html
- PostgreSQL Error Codes: https://www.postgresql.org/docs/current/errcodes-appendix.html

## Yardım

Danışma saatleri: Salı 13:00-15:00, Cuma 12:00-14:00 (Dudullu Kampüsü)
E-posta: gturker@dogus.edu.tr (konu satırına "COME 381" ve GitHub kullanıcı adını yaz)
