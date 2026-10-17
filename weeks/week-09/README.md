# Hafta 9: Transaction: ACID, schedule ve serializability

Durum: Yakında

## Bu hafta ne işliyoruz

- Transaction kavramı ve durumları: active, partially committed, committed, failed, aborted
- ACID: atomicity, consistency, isolation, durability
- Schedule: seri ve serializable schedule
- Conflict serializability ve precedence graph
- Recoverable ve cascadeless schedule
- PostgreSQL'de `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT`

## Hafta sonunda yapabileceklerin

- ACID özelliklerini bir banka havalesi örneğiyle anlatabilirsin
- Bir schedule'ın serializable olup olmadığını precedence graph ile kontrol edebilirsin
- Recoverable ve recoverable olmayan schedule arasındaki farkı gösterebilirsin

## Anahtar terimler

| English | Türkçe |
|---|---|
| transaction | işlem |
| schedule | çizelge |
| serializability | serileştirilebilirlik |
| precedence graph | öncelik grafiği |
| recoverable schedule | kurtarılabilir çizelge |

## Laboratuvar

`BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` ile deneyler. Havale senaryosunda yarım kalan bir transaction'ı geri almak.

## Okuma

Transaction işleme kavramları ve teorisi bölümleri.

## Ek kaynaklar

- PostgreSQL Transactions: https://www.postgresql.org/docs/current/tutorial-transactions.html
