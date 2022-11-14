MongoDB sunucularına ve veritabanlarına erişim kısaca anlatılacaktır.

* Linux OS-info:  Linux versiyon ve dağıtım bilgisini verir.
`cat /etc/os-release` 
`hostnamectl`

* Database service info: Database'in ayakta olup olmadığını gösterir.
`systemctl status mongod.service`
`systemctl status mongodb.service`

* version: Database versionunu görüntüler.
`mongo --version`

* Terminal commands:
`id` " kullanıcı bilgisi verir"
`env` "environment parmetrelerini gösterir"
`hostname` "sunucunun ismini gösterir"
`sudo su - mongod` "database kullanıcısına geçişin uygun kullanımı"
`history` "daha önceden kullanılan komutları gösterir"
`history | grep mongo` "mongo sözcüğüne göre filtrelenmiş halini verir."
`mongo` "database'e bağlanan araç"
`lscpu` "cpu bilgisini verir."
`top` "kaynak kullanımını gösterir"
`ps -ef | grep mongo` "çalışan mongo process'lerini gösterir."



* Lists which database you have:
`systemctl list-units | grep 'mongo\|oracle\|postgres\|sql'`



**Database**


Database'e adım adım bağlantı nasıl yapılacağı aşağıdadır.

`sudo su - mongod`
* `history | grep mongo` çıktısından güncel bağlantı satırını alabilirsiniz.
`mongo --host "10.14.4.150:27011" -u madmin -p "madm900"`

`db` "bağlanılan database'i gösterir"
`show dbs` "tüm database'leri gösterir"
`use db_name` "database'e bağlantı yapar. örnekte db_name bağlantısı yapılmış"
`show collections` "tüm collection'ları (table) listeler."
`db.stats()` "database status bilgilerini listeler" 
`rs.status()` "sistemdeki node'ların durumunu gösterir"
