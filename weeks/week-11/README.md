# Hafta 11: Veritabanı kurtarma

Durum: Yakında

## Bu hafta ne işliyoruz

- Hata türleri: transaction hatası, sistem çökmesi, disk hatası
- Log tabanlı kurtarma: undo, redo, checkpoint
- Write-ahead logging (WAL)
- ARIES algoritmasına giriş
- Shadow paging
- Backup ve restore: `pg_dump`, `pg_restore`

## Hafta sonunda yapabileceklerin

- Bir çökmeden sonra log'a bakıp hangi transaction'ın undo, hangisinin redo edileceğini çıkarabilirsin
- Veritabanını yedekten geri yükleyebilirsin
- WAL'ın ve checkpoint'in rolünü anlatabilirsin

## Anahtar terimler

| English | Türkçe |
|---|---|
| write-ahead log (WAL) | önceden yazma günlüğü |
| checkpoint | kontrol noktası |
| undo / redo | geri alma / yeniden yapma |
| shadow paging | gölge sayfalama |
| backup / restore | yedekleme / geri yükleme |

## Laboratuvar

`pg_dump` / `pg_restore` tatbikatı: veritabanını yedekle, boz, geri yükle; adımları ve süreyi not et.

## Okuma

Veritabanı kurtarma bölümü.

## Ek kaynaklar

- PostgreSQL Backup and Restore: https://www.postgresql.org/docs/current/backup.html
- PostgreSQL WAL: https://www.postgresql.org/docs/current/wal-intro.html

## Ödev

Ödev 6: Backup ve restore tatbikatı. Yönergesi bu klasöre eklenecek.
