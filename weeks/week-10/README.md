# Hafta 10: Eşzamanlılık denetimi

Durum: Yakında

## Bu hafta ne işliyoruz

- Aynı anda çalışan transaction'ların sorunları: lost update, dirty read, non-repeatable read, phantom
- Kilit tabanlı protokoller: shared ve exclusive lock, two-phase locking (2PL)
- Deadlock: tespit ve önleme, wait-for graph
- Timestamp ordering
- MVCC ve optimistic yaklaşım
- Isolation level'lar ve hangi anomaliyi önledikleri

## Hafta sonunda yapabileceklerin

- İki `psql` oturumuyla bir anomali ve bir deadlock üretebilirsin
- Hangi isolation level'ın hangi anomaliyi önlediğini açıklayabilirsin
- 2PL ile timestamp ordering'i karşılaştırabilirsin

## Anahtar terimler

| English | Türkçe |
|---|---|
| concurrency control | eşzamanlılık denetimi |
| two-phase locking (2PL) | iki aşamalı kilitleme |
| deadlock | kilitlenme |
| timestamp ordering | zaman damgası sıralaması |
| isolation level | izolasyon seviyesi |

## Laboratuvar

İki `psql` oturumuyla anomali ve deadlock deneyi.

## Okuma

Eşzamanlılık denetimi protokolleri bölümü.

## Ek kaynaklar

- PostgreSQL Concurrency Control: https://www.postgresql.org/docs/current/mvcc.html

## Ödev

Ödev 5: Capstone ara kontrol (Pull Request). Yönergesi bu klasöre eklenecek.
