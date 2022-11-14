Oracle sunucularına ve veritabanlarına erişim kısaca anlatılacaktır.

* Linux OS-info: Linux versiyon ve dağıtım bilgisini verir.
`cat /etc/release`

* version: Database versionunu görüntüler.
`sqlplus -v`
`SELECT * FROM v$version;`

* Database service info: Database'in ayakta olup olmadığını gösterir.
`svcs -a`

* Terminal commands:
`id` " kullanıcı bilgisi verir"
`env` "environment parmetrelerini gösterir"
`hostname` "sunucunun ismini gösterir"
`history` "daha önceden kullanılan komutları gösterir"
`history | grep ora` "ora sözcüğüne göre filtrelenmiş halini verir."
`sudo su - oraqc` "database kullanıcısına geçişin uygun kullanımı"
`top` "kaynak kullanımını gösterir"
`ps -ef | grep ora` "çalışan oracle process'lerini gösterir."


**Database**

Database'e adım adım bağlantı nasıl yapılacağı aşağıdadır.

`sudo su - oraqc`
* `history | grep sqlplus`

`sqlplus / as sysdba`


`SELECT NAME FROM v$database;` "databaseleri listeler"
`CONNECT username/password` "kullanıcıya bağlantı yapar"
`conn db_name` "database'e bağlanır"

`desc dictionary` "dictionary tablosunun kolonlarının ne olduğunu gösterir"
`desc dba_users` "tablonun kolon bilgisini verir"
`desc dba_tables`

`lsnrctl status` "listener durumunu gösterir. Bağlantı yapabilmek için listener ayakta olmalı."
