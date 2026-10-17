# Hafta 8: Vize savunmaları, gelişmiş SQL ve indeks

Durum: Yakında

## Bu hafta ne işliyoruz

- Vize savunmaları (bireysel, 7 dakika)
- İlişkili alt sorgu (correlated subquery), `EXISTS`, outer join
- View ve materialized view
- Trigger ve stored function
- `CHECK` ile yazılamayan kurallar: assertion fikri ve PostgreSQL'de trigger ile çözümü
- Şema değişikliği: `ALTER TABLE`
- İndekse giriş: B-tree indeks, `EXPLAIN ANALYZE` ile planı okumak

## Hafta sonunda yapabileceklerin

- Kendi tasarım kararlarını gerekçeleriyle savunabilirsin
- View ile hem sorguyu sadeleştirip hem yetkiyi daraltabilirsin
- Birden çok tabloya yayılan bir kuralı trigger ile yazabilirsin
- Bir indeksin sorgu planını nasıl değiştirdiğini ölçebilirsin

## Anahtar terimler

| English | Türkçe |
|---|---|
| view / materialized view | görünüm / gerçekleştirilmiş görünüm |
| trigger | tetikleyici |
| assertion | genel bütünlük kısıtı |
| query plan | sorgu planı |
| B-tree index | B-ağacı indeksi |

## Laboratuvar

Savunmalar laboratuvar saatinde yapılır. Sırası gelmeyenler view, trigger ve `EXPLAIN ANALYZE` uygulaması üzerinde çalışır.

## Okuma

Gelişmiş SQL bölümü (trigger, view, şema değişikliği) ve indeksleme bölümünün girişi.

## Ek kaynaklar

- Use The Index, Luke: https://use-the-index-luke.com
- PostgreSQL Triggers: https://www.postgresql.org/docs/current/triggers.html

## Ödev

Ödev 4: Capstone önerisi (GitHub Issue). Yönergesi bu klasöre eklenecek.
