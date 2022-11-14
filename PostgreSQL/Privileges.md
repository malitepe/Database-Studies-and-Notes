## User:
In PostgreSqQL, user and role are exactly the same. Only their intended use is different.
`CREATE USER username;`  The following authorizations come by default.
`ALTER USER` for update, `DROP USER` for delete.
```
NOSUPERUSER
INHERIT
NOCREATEDB
NOCREATEROLE
NOREPLICATION
```

Important Tip:
Make a note of every change you make to the user and every code you write. Because the 'DROP USER' code may give an error due to the privileges given and the objects created with the user. The owner of these objects should be changed and their authorizations, and authorizations should be removed. You can face with `... cannot be dropped because some objects depend on it` error  .


## Role:
Bu konsept daha anlaşılabilir ve kolay yönetilebilir bir sistem oluşması için tasarlanmıştır. `CREATE ROLE rolename` şeklinde oluşturulur. User'a ek olarak `NOLOGIN` default yetkisiyle oluşturulur. Fakat bunu `WITH LOGIN` eklentisi ile user gibi kullanabiliriz.

Roller globaldir. Bir database'e göre oluşturulamazlar. Global oluşturup database erişimleri sağlanır.

Database sistemi ilk başlatıldığı zaman oluşturulan kullanıcı ismiyle(işletim sistemindeki kullanıcı) bir role oluşturulur. Çoğu zaman bu rol `postgres` rolüdür. Bu rol 'superuser' olarak tanımlanır. 

## Role Attributes:

`CREATE ROLE name LOGIN;`
`CREATE USER name;`
Komutları aynı nesneyi oluşturur.


```
CREATE ROLE app_user WITH
  NOLOGIN -- giriş yapamaz
  NOSUPERUSER --superuser değil
  INHERIT -- atanan rollerin yetkilerini alamaz. 
  NOCREATEDB -- db create edemez
  NOCREATEROLE -- role create edemez.
  NOREPLICATION; -- replica oluşturamaz.
```

Not: CREATEDB ve CREATEROLE yetkisiyle, süper kullanıcı olmayan bir rol oluşturmak ve bu rolü veritabanlarının ve rollerin tüm rutin yönetimi için kullanmak iyi bir uygulamadır.Gerçekten gerektirmeyen görevler için bir süper kullanıcı olarak çalışmanın tehlikelerini önler.

## Public:

Yeni bir database oluşturulduğunda public adında bir shcema oluşturulur. Her yeni user veya role oluşturulduğunda default olarak public yetkileri verilir ve public schema'da object create yetkisi olur.


Aşağıdaki yetkilerle devam edilmesi önerilir.
```
revoke create on schema public from public;
revoke all on database test1 from public;
```



## Postgres:
Postgres sistemin başlangıcında oluşturulan superuser'dır.


```
CREATE ROLE postgres WITH
  LOGIN
  SUPERUSER
  INHERIT
  CREATEDB
  CREATEROLE
  REPLICATION
  ENCRYPTED PASSWORD 'passwd';
```




##Priviliges:

SELECT
tablo, view, materialized view gibi objelerden herhangi bir kolona veya spesifik bir kolona SELECT sorgusu atabilirsiniz.

INSERT
yeni satır ekler .table, view, etc. 

UPDATE
DELETE (require SELECT privilege)
TRUNCATE
REFERENCES(Foreign key constraint oluşturmak için gereklidir.)
TRIGGER
CREATE
Database için, yeni schema oluşturmak, trusted extensions indirmek için kullanılır.

Schema için, yeni object oluşturmada kullanılır. object ismini değiştirmede kullanılır, ama bunun için ilgili schema da object owner olmak ve bu yetkiyi almak gerekir.

Tablespace için, table, index, ve temporary file oluşturmaya yarar. Kendi default tablespace'lerinde database create etmeyi sağlar.

Bu yetkiyi revoke etmek varolan object'lerin varlığını veya lokasyonlarını güncellemez.

CONNECT
database'e bağlantıyı sağlar. Yetki startup'ta kontrol edilir. (pg_hba.conf dosyasındaki sınırları da kontrol eder)

TEMPORARY (temp table)
EXECUTE (function or procedure,only priv for function and procedure)
USAGE
procedural language: İlgili dilde fonksiyon oluşturmaya izin verir. procedural language için tek yetki budur.

schemas: schema içerisine erişimi sağlar.

sequences: currval ve nextval function kullanımına izin verir.

foreign-data wrappers: foreign-data wrapper kullanılarak yeni server'lerin oluşturulmasına izin verir..

foreign servers: server kullanılarak foreign table oluşturulmasına izin verir. create, alter veya drop gibi yetkilerinin de ayrıca olması gerekir.

priviliges notes:

* Yetkilendirmeler additive'dir. Birden fazla role ataması yapıldığında roller birbiriyle çakışmaz. Rollerin yetkileri user'a eklenir.
* Bir user başka bir user'a role olarak atanabilir.
* Bir object'i owner veya superuser yetkilendirebilir.
* Eğer çok temel bir role varsa ve biz bu rolü her yerde kullandıysak, rolü NOINHERIT yapmak mantıklıdır. Çünkü bu role başka bir rol atandığında ilgili objeler değişir. NOINHERIT yetkilendirmesi ise grant edilen role'den yetkilendirme (Kalıtım) almaz.
* User ve rol oluştururken

### Senaryo 1:

```
CREATE ROLE application_base_user NOINHERIT;
CREATE ROLE reader INHERIT;
CREATE ROLE writer INHERIT;
CREATE ROLE administrator INHERIT;
GRANT application_base_user TO reader, writer, administrator;
```

Bu senaryoda application_base_user hemen hemen her kullanıcıya atanır. Büyük bir değişiklik yapmak gerektiğinde kullanıcılara yeni bir base user oluşturmak gerekir. Çünkü yetkilendirmeler additive'dir.


### Senaryo 2:

Bu senaryoda bir IT ekibinin çalışmaları resmedilecektir.
Database: studies_of_departments
schemas: dba, devops, systemadmin
roles: head_of_dba, head_of_devops, head_of_sysadmin
NOINHERIT roles: basic_dba_emp , head_base_role
tables: ahmet_works, mehmet_works, ali_works
users: ahmet, mehmet, ali
```
CREATE DATABASE studies_of_departments;
\c studies_of_departments; -- connect to db
```

Yeni oluşturulan Database içindeki public rolünün yetkileri sınırlandırılır. schema ve db üzerindeki yetkileri kaldırılır.

```
revoke create on schema public from public;
revoke all on database studies_of_departments from public;
```

Departmanların başındaki kişiler için roller oluşturulur. Bu yöneticiler için bir base role oluşturulup yetkilendirmeler bu rolün her birine atanmasıyla gerçekleştirilir. Login yetkisi ve connect yetkisi verilir.
```
CREATE ROLE head_of_dba, head_of_devops, head_of_sysadmin; 
CREATE ROLE head_base_role LOGIN NOINHERIT;
grant connect on database studies_of_departments to head_base_role;
GRANT head_base_role TO head_of_dba, head_of_devops, head_of_sysadmin ;
```
Her bir departman schema'lar ile birbirinden ayrılır.
```
CREATE SCHEMA dba AUTHORIZATION head_of_dba;
CREATE SCHEMA devops AUTHORIZATION head_of_devops;
CREATE SCHEMA sysadmin AUTHORIZATION head_of_sysadmin;
```
Yöneticilere kendi departmanlarını kullanabilme yetkisi verilir. Cheat sheet'e göz atabilirsiniz.

```
grant usage on schema dba to head_of_dba;
grant usage on schema devops to head_of_devops;
grant usage on schema sysadmin to head_of_sysadmin;
```

Schema içerisine girilip table oluşturulur.
```
set search_path to dba ;

CREATE TABLE ahmet_works (
    id        integer,
    work_name       varchar(40),
);

CREATE TABLE mehmet_works(
    id        integer,
    work_name       varchar(40),
);

CREATE TABLE ali_works (
    id        integer,
    work_name       varchar(40),
);
```

Tabloları kullanacak user'lar oluşturulur.
```
CREATE USER ali WITH PASSWORD '1';
CREATE USER mehmet WITH PASSWORD '1';
CREATE USER ahmet WITH PASSWORD '1';
```

Çalışanlar için base role oluşturulur. Connect , usage, create ve select yetkileri tanımlanır.
```
create role basic_dba_emp NOINHERIT;;
grant connect on database studies_of_departments to basic_dba_emp;
grant usage,create on schema dba to basic_dba_emp;
grant select on all tables in schema dba to basic_dba_emp;
```

Her çalışan, kendi çalışma alanına yetkilendirilir. Base rol dba departmanındaki herkese atanır.

```
GRANT ALL PRIVILEGES ON ali_works TO ali ;
GRANT ALL PRIVILEGES ON mehmet_works TO mehmet ;
GRANT ALL PRIVILEGES ON ahmet_works TO ahmet ;
GRANT basic_dba_emp TO ali, mehmet, ahmet ;
GRANT basic_dba_emp TO head_of_dba;
```







#Privilege Abbreviations 

| Privilege | Kısaltma | Uygulanabilir Object Tipi |
|--|--|--|
| SELECT | r (“read”) | LARGE OBJECT, SEQUENCE, TABLE (and table-like objects), table column |
| INSERT | a (“append”) | TABLE, table column |
| UPDATE | w (“write”) | LARGE OBJECT, SEQUENCE, TABLE, table column |
| DELETE | d | TABLE |
| TRUNCATE | D | TABLE |
| REFERENCES | x | TABLE, table column |
| TRIGGER | t | TABLE |
| CREATE | C | DATABASE, SCHEMA, TABLESPACE |
| CONNECT | c | DATABASE |
| TEMPORARY | T | DATABASE |
| EXECUTE | X | FUNCTION, PROCEDURE |
| USAGE | U | DOMAIN, FOREIGN DATA WRAPPER, FOREIGN SERVER, LANGUAGE, SCHEMA, SEQUENCE, TYPE |



#Summary of Access Privileges

| Object Type	 | All Privileges | Default PUBLIC Privileges | psql Command |
|--|--|--|--|
| DATABASE | CTc | Tc | \l |
| DOMAIN | U | U | \dD+ |
| FUNCTION or PROCEDURE | X | X | \df+ |
| FOREIGN DATA WRAPPER | U | none | \dew+ |
| FOREIGN SERVER | U | none | \des+ |
| LANGUAGE | U | U | \dL+ |
| LARGE OBJECT | rw | none |  |
| SCHEMA | UC | none | \dn+ |
| SEQUENCE | rwU | none | \dp |
| TABLE (and table-like objects) | arwdDxt | none | \dp |
| Table column | arwx | none | \dp |
| TABLESPACE | C | none | \db+ |
| TYPE | U | U | \dT+ |








readonly-writeonly-administrator roles example.
















