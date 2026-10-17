# Hafta 5: İlişkisel model, constraint'ler ve ilişkisel cebir

Durum: Yayında

## Bu hafta ne işliyoruz

- İlişkisel model ve bütünlük kısıtları (integrity constraints)
- `NOT NULL`, `UNIQUE`, `CHECK`, `FOREIGN KEY` ve `ON DELETE` davranışları
- `INSERT`, `UPDATE`, `DELETE` bir kuralı bozduğunda ne olur: işlem reddedilir, ya da `CASCADE` / `SET NULL` devreye girer
- İlişkisel cebir: selection, projection, join, union, difference, rename, division
- İlişkisel hesap (tuple relational calculus)
- Küme ve çanta farkı: cebir tekrarı atar, SQL atmaz

## Hafta sonunda yapabileceklerin

- İş kurallarını veritabanının kendisine denetletebilirsin
- Bir constraint'i bilerek bozup PostgreSQL'in hata kodunu okuyabilirsin
- Bir silme işleminin bağlı satırlara ne yapacağına gerekçeyle karar verebilirsin
- Bir cebir ifadesini SQL'e, bir SQL sorgusunu cebire çevirebilirsin

## Anahtar terimler

| English | Türkçe |
|---|---|
| integrity constraint | bütünlük kısıtı |
| check constraint | kontrol kısıtı |
| referential integrity | referans bütünlüğü |
| relational algebra / calculus | ilişkisel cebir / hesap |
| selection / projection | seçim / izdüşüm |
| division | bölme |

## Laboratuvar

Kendi şemana constraint ekleme ve her birini bilerek bozma. Cebir ifadelerini SQL'e çevirme.

## Ödevler

Son teslim ikisi için de **25 Ekim 2026 Pazar, 23:59**. İkisi tek Pull Request ile gönderilir.

- [Ödev 1: Kendi senaryon için ER diyagramı ve şema](odev-1.md)
- [Ödev 2: Constraint'ler, ihlal testleri ve ilişkisel cebir](odev-2.md)

Önce Ödev 1'i bitir; Ödev 2 onun üzerine kuruluyor.

## Okuma

Elmasri & Navathe veya Silberschatz: ilişkisel model, constraint'ler ve ilişkisel cebir bölümleri.

## Ek kaynaklar

- PostgreSQL Constraints: https://www.postgresql.org/docs/current/ddl-constraints.html
- PostgreSQL Error Codes: https://www.postgresql.org/docs/current/errcodes-appendix.html
