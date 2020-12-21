# Veritabanı Oluşturmak
*Veritabanı Oluşturmak — nasıl yeni bir veritabanı oluşturulur*

## Özet

```
CREATE DATABASE name
    [ [ WITH ] [ OWNER [=] user_name ]
           [ TEMPLATE [=] template ]
           [ ENCODING [=] encoding ]
           [ LOCALE [=] locale ]
           [ LC_COLLATE [=] lc_collate ]
           [ LC_CTYPE [=] lc_ctype ]
           [ TABLESPACE [=] tablespace_name ]
           [ ALLOW_CONNECTIONS [=] allowconn ]
           [ CONNECTION LIMIT [=] connlimit ]
           [ IS_TEMPLATE [=] istemplate ] ]
```

## Tanımlama

`CREATE DATABASE` yeni bir PostgreSQL veritabanı oluşturur.

Bir veritabanı oluşturmak için, süper kullanıcı(superuser) olmanız veya özel `CREATEDB` ayrıcalığına sahip olmanız gerekir. Bkz. [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html).

Varsayılan olarak, yeni veritabanı standart sistem veritabanı `template1` klonlanarak oluşturulacaktır. `TEMPLATE` **name** yazarak farklı bir şablon belirtilebilir. Özellikle, `TEMPLATE` `template0` yazarak, yalnızca PostgreSQL sürümünüz tarafından önceden tanımlanmış standart nesneleri içeren bozulmamış bir veritabanı (kullanıcı tanımlı nesnelerin olmadığı ve sistem nesnelerinin değiştirilmediği bir veritabanı) oluşturabilirsiniz. Bu, `template1`'e eklenmiş olabilecek herhangi bir yerel kurulum nesnesini kopyalamak istemiyorsanız kullanışlıdır.

## Parametreler

* **name**

Oluşturulacak veritabanının adı.

* **user_name**

Yeni veritabanına sahip olacak kullanıcının rol adı veya varsayılanı kullanmak için `DEFAULT` (yani, komutu yürüten kullanıcı). Başka bir role ait bir veritabanı oluşturmak için, o rolün doğrudan veya dolaylı bir üyesi olmanız veya bir süper kullanıcı(superuser) olmanız gerekir.

* **template**

Yeni veritabanının oluşturulacağı şablonun adı veya varsayılan şablonu (`template1`) kullanmak için `DEFAULT`.

* **encoding**

Yeni veritabanında kullanılacak karakter seti kodlaması. Varsayılan kodlamayı (yani, şablon veritabanının kodlamasını) kullanmak için bir dize sabiti (ör. `'SQL_ASCII'`) veya bir tam sayı kodlama numarası veya `DEFAULT` belirtiniz. PostgreSQL sunucusu tarafından desteklenen karakter kümeleri [Section 23.3.1](https://www.postgresql.org/docs/current/multibyte.html#MULTIBYTE-CHARSET-SUPPORTED)'de açıklanmıştır. Ek kısıtlamalar için aşağıya bakın.

* **locale**

Bu, `LC_COLLATE` ve `LC_CTYPE`'i aynı anda ayarlamak için bir kısayoldur. Bunu belirtirseniz, bu parametrelerden hiçbirini belirleyemezsiniz.


* **İpucu**

Diğer yerel ayarlar [lc_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MESSAGES), [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY), [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) ve [lc_time](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-TIME) veritabanı başına sabitlenmiş değildir ve bu komutla ayarlanmamıştır. Bunları belirli bir veritabanı için varsayılan yapmak istiyorsanız, ALTER DATABASE ... SET'i kullanabilirsiniz.

* **lc_collate**

Yeni veritabanında kullanılacak harmanlama sırası `(LC_COLLATE)`. Bu, dizelere uygulanan sıralama düzenini, örneğin `ORDER BY` ile sorgularda ve ayrıca metin sütunlarındaki dizinlerde kullanılan sırayı etkiler. Varsayılan, şablon veritabanının harmanlama sırasını kullanmaktır. Ek kısıtlamalar için aşağıya bakın.

* **lc_ctype**

Yeni veritabanında kullanılacak karakter sınıflandırması `(LC_CTYPE)`. Bu, karakterlerin kategorize edilmesini etkiler, örn. `lower`, `upper` ve `digit`. Varsayılan, şablon veritabanının karakter sınıflandırmasını kullanmaktır. Ek kısıtlamalar için aşağıya bakın.

* **tablespace_name**

Yeni veritabanıyla ilişkilendirilecek tablo alanının adı veya şablon veritabanının tablo alanını kullanmak için `DEFAULT`. Bu tablo alanı, bu veritabanında oluşturulan nesneler için kullanılan varsayılan tablo alanı olacaktır. Daha fazla bilgi için bkz. [CREATE TABLESPACE](https://www.postgresql.org/docs/current/sql-createtablespace.html).

* **allowconn**

Yanlışsa, bu veritabanına hiç kimse bağlanamaz. Varsayılan değer doğrudur ve bağlantılara izin verir (`GRANT/REVOKE CONNECT` gibi diğer mekanizmalar tarafından kısıtlanması dışında).

* **connlimit**

Bu veritabanına kaç eşzamanlı bağlantı yapılabilir. -1 (varsayılan) sınır yok demektir.

* **istemplate**

Doğruysa, bu veritabanı `CREATEDB` ayrıcalıklarına sahip herhangi bir kullanıcı tarafından klonlanabilir; false ise (varsayılan), o zaman veritabanının yalnızca süper kullanıcıları veya sahibi onu klonlayabilir.

İsteğe bağlı parametreler, yalnızca yukarıda gösterilen sırayla değil, herhangi bir sırada yazılabilir.

## Notlar

`CREATE DATABASE` bir işlem bloğu içinde yürütülemez.

"Veritabanı dizini ilklendirilemedi" satırındaki hatalar büyük olasılıkla veri dizinindeki yetersiz izinlerle, tam diskle veya diğer dosya sistemi sorunlarıyla ilgilidir.

Bir veritabanını kaldırmak için [DROP DATABASE](https://www.postgresql.org/docs/current/sql-dropdatabase.html) kullanın.

Program [createdb](https://www.postgresql.org/docs/current/app-createdb.html), kolaylık sağlamak için sağlanan bu komut etrafında bir sarmalayıcı programdır.

Veritabanı seviyesinde yapılandırma parametreleri ([ALTER DATABASE](https://www.postgresql.org/docs/current/sql-alterdatabase.html) aracılığıyla ayarlanır) ve veritabanı düzeyinde izinler ([GRANT](https://www.postgresql.org/docs/current/sql-grant.html) aracılığıyla ayarlanır) şablon veritabanından kopyalanmaz.

`template1` dışındaki bir veritabanını şablon olarak adını belirterek kopyalamak mümkün olsa da, bu (henüz) genel amaçlı bir `“COPY DATABASE”` tesisi olarak tasarlanmamıştır. Temel sınırlama, kopyalanırken şablon veritabanına başka hiçbir oturumun bağlanamamasıdır. `CREATE DATABASE`, başladığında başka bir bağlantı varsa başarısız olur; aksi takdirde, şablon veritabanına yeni bağlantılar `CREATE DATABASE` tamamlanana kadar kilitlenir. Daha fazla bilgi için bkz. [Section 22.3](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) for more information.

Yeni veritabanı için belirtilen karakter seti kodlaması, seçilen yerel ayarlarla (`LC_COLLATE` ve `LC_CTYPE`) uyumlu olmalıdır. Yerel C (veya eşdeğer olarak `POSIX`) ise, tüm kodlamalara izin verilir, ancak diğer yerel ayarlar için düzgün çalışacak yalnızca bir kodlama vardır. (`Windows` da, `UTF-8` kodlaması herhangi bir yerel ayarda kullanılabilir.) `CREATE DATABASE` süper kullanıcıların yerel ayarlardan bağımsız olarak `SQL_ASCII` kodlamasını belirtmesine izin verir, ancak bu seçim kullanımdan kaldırılmıştır ve yerel ile kodlama uyumlu olmayan veriler veritabanında depolanırsa karakter dizesi işlevlerinin yanlış davranışı.

Şablon olarak `template0` kullanılması dışında, kodlama ve yerel ayarlar şablon veritabanınınkilerle eşleşmelidir. Bunun nedeni, diğer veritabanlarının belirtilen kodlamaya uymayan veriler içerebilmesidir veya sıralama düzeni `LC_COLLATE` ve `LC_CTYPE` tarafından etkilenen dizinler içerebilir. Bu tür verilerin kopyalanması, yeni ayarlara göre bozuk bir veritabanına neden olacaktır. Ancak `template0`'ın etkilenecek herhangi bir veri veya dizin içermediği bilinmektedir.

`CONNECTION LIMIT` seçeneği yalnızca yaklaşık olarak uygulanır; Veritabanı için sadece bir bağlantı `“slot”` kaldığında aynı anda iki yeni oturum başlarsa, her ikisinin de başarısız olması mümkündür. Ayrıca sınır, süper kullanıcılara(superusers) veya arka plan çalışan işlemlerine karşı uygulanmaz.

## Örnekler

Yeni bir veritabanı oluşturmak için:

`CREATE DATABASE lusiadas;`

Salesspace'in varsayılan tablo alanıyla kullanıcı satış uygulamasına ait bir veritabanı satışı oluşturmak için:

`CREATE DATABASE sales OWNER salesapp TABLESPACE salesspace;`

Farklı dilde `music` isimli bir veritabanı oluşturmak için:

```
CREATE DATABASE music
    LOCALE 'sv_SE.utf8'
    TEMPLATE template0;
```

Bu örnekte, belirtilen yerel ayar `template1` içindekinden farklıysa `TEMPLATE` `template0` yan cümlesi gereklidir. (Değilse, yerel ayarı açıkça belirtmek gereksizdir.)

Farklı bir dilde ve farklı bir karakter seti kodlamasına sahip bir `music2` veritabanı oluşturmak için:

```
CREATE DATABASE music2
    LOCALE 'sv_SE.iso885915'
    ENCODING LATIN9
    TEMPLATE template0;
```

Belirtilen `locale` ve kodlama ayarları eşleşmelidir, aksi takdirde bir hata bildirilecektir.

Yerel ayar adlarının işletim sistemine özgü olduğunu ve bu nedenle yukarıdaki komutların her yerde aynı şekilde çalışmayabileceğini unutmayın.

## Uyumluluk

`SQL standardı`'nda `CREATE DATABASE` ifadesi yoktur. Veritabanları, yaratımı uygulama tanımlı olan kataloglara eşdeğerdir.

## Ayrıca bakınız

[ALTER DATABASE](https://www.postgresql.org/docs/current/sql-alterdatabase.html), [DROP DATABASE](https://www.postgresql.org/docs/current/sql-dropdatabase.html)

[PostgreSQL Resmi Belgelendirme Sayfası](https://www.postgresql.org/docs/current/sql-createdatabase.html)