**PostgreSQL sunucularına ve veritabanlarına erişim kısaca anlatılacaktır.**

* Linux OS-info: Linux versiyon ve dağıtım bilgisini verir.
`cat /etc/os-release` 
`hostnamectl`


* Database service info: Database'in ayakta olup olmadığını gösterir.
`systemctl status postgresql.service`

* version: Database versionunu görüntüler.
`psql --version`

* Terminal commands:
`id` " kullanıcı bilgisi verir"
`env` "environment parmetrelerini gösterir"
`hostname` "sunucunun ismini gösterir"
`sudo su - postgres` "database kullanıcısına geçişin uygun kullanımı"
`history` "daha önceden kullanılan komutları gösterir"
`history | grep psql` "psql sözcüğüne göre filtrelenmiş halini verir."
`psql` "database'e bağlanan araç"
`lscpu` "cpu bilgisini verir."
`top` "kaynak kullanımını gösterir"
`ps -ef | grep postgres` "çalışan postgres process'lerini gösterir."

* Lists which database you have:
`systemctl list-units | grep 'mongo\|oracle\|postgres\|sql'`

**Database :** 

Database'e adım adım bağlantı nasıl yapılacağı aşağıdadır.

`sudo su - postgres`

```
psql
psql testdb -U testUser
```

Default schema görüntüleme ve değiştirme adımları:

```
show search_path;
set search_path to schema1;
```

Parametreler bu şekilde gösterilebilir.
`show default_tablespace;`

\l : show databases
\db : list of tablespaces.
\\? : list all commands.
\h : help command

* pg_config : kurulum bilgilerini veren komut satırı  arcıdır.
