# PostgreSQL Administration Cheatsheet
## Install PostgreSQL

1. Download installer di [sini](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)
2. Setelah download selesai, run installernya
3. Pilih Installation directory atau biarkan default
4. Pilih Components PostgreSQL Server, pgAdmin, dan Command Line Tools. 
5. Pilih Data directory atau biarkan default
6. Masukkan password yang diinginkan untuk akun superuser
7. Masukkan port yang diinginkan atau biarkan default
8. Pilih lokasi geografis server database atau biarkan default
9. Periksa ringkasan pengaturan yang telah dipilih dan pastikan sudah benar
10. Setelah itu, installasi akan dimulai dan tunggu sampai selesai
11. Saat selesai, maka postgreSQL bisa langsung digunakan. 
 
## Connect ke Database
ada beberapa cara untuk connect ke database postgresSQL, bisa melalui psql, pgAdmin atau dbeaver. berikut tata caranya:

### SQL Shell (psql)
SQL Shell (psql) adalah **program berbasis terminal tempat kita dapat menulis dan menjalankan sintaks SQL di terminal command-line.**

1. Cari dan buka **SQL Shell (psql)** di menu window
2. Tulis server yang ingin dihubungkan atau tekan **[Enter]** untuk memilih **[localhost]** secara default
3. Tulis database yang ingin digunakan atau tekan **[Enter]** untuk memilih **[postgres]** secara default
4. Tulis port yang ingin digunakan atau tekan **[Enter]** untuk memilih **[5432]** secara default
5. Tulis username yang ingin digunakan atau tekan **[Enter]** untuk memilih **[postgres]** secara default
6. Tulis password untuk username yang digunakan sebelumnya
7. Jika commandnya berubah menjadi **postgres=#** maka kamu berhasil terkoneksi ke servernya

### pgAdmin
PgAdmin merupakan **antarmuka berbasis web yang digunakan untuk mengelola database Postgresql**

1. Cari dan buka **pgAdmin4** di menu window
2. Tekan dropdown **Servers**
3. Pilih Server yang PostgreSQL <versi\> contohnya **PostgreSQL 16**
4. Masukkan **password** yang dimiliki user yang tertera pada prompt
5. Jika logo servernya berubah maka artinya sudah berhasil terhubung. 

### DBeaver
DBeaver adalah **software atau aplikasi berbasis GUI (Graphical User Interface) yang digunakan untuk mengelola dan mengakses berbagai jenis basis data.**

1. [Download](https://dbeaver.io/download/) dan install DBeaver
2. Cari dan buka **DBeaver** di menu window
3. Pada Menu Bar, tekan **Database -> New Database Connection -> Pilih PostgreSQL**
4. Isi **Host**, **Database**, dan **Port** server yang akan digunakan
5. Setelah itu isi **credential** yang akan dipakai
6. Jika semua konfigurasi sudah benar tekan finish dan dbeaver akan memulai mendownload driver yang diperlukan
7. Tekan nama server yang telah dibuat sebelumnya pada sidebar bagian kiri
8. Jika terdapat **bulatan centang berwarna hijau** pada logo maka artinya **berhasil** terhubung ke server
 
## Managing Databases
Database adalah **sistem yang berfungsi sebagai mengumpulkan file, tabel, atau arsip yang terhubung dan disimpan dalam berbagai media elektronik.**

### Create Database
Untuk membuat database dapat menggunakan statement `CREATE DATABASE`, yaitu:

    CREATE DATABASE database_name
    WITH
       [OWNER =  role_name]
       [TEMPLATE = template]
       [ENCODING = encoding]
       [LC_COLLATE = collate]
       [LC_CTYPE = ctype]
       [TABLESPACE = tablespace_name]
       [ALLOW_CONNECTIONS = true | false]
       [CONNECTION LIMIT = max_concurrent_connection]
       [IS_TEMPLATE = true | false ];

Dengan:

- `Owner` : Menetapkan role yang akan menjadi pemilik database. Default nya adalah role yang mengeksekusi command
    
- `Template` : Menetapkan template database untuk database baru. Default nya adalah template1
    
- `Encoding` : Menentukan kumpulan karakter untuk database baru. Default nya UTF-8
    
-  `LC_COLLATE` : Menentukan aturan pengurutan karakter dalam database, seperti urutan alfabet atau aturan khusus pengurutan yang disesuaikan dengan bahasa atau aturan tertentu.
    
-   `LC_CTYPE` : Mengatur pengenalan jenis karakter, seperti huruf besar ke huruf kecil, atau perbedaan dalam pengenalan karakter khusus dalam bahasa tertentu.
    
-   `Tablespace` : Menentukan nama tablespace untuk database baru.
    
-   `ALLOW_CONNECTIONS` : Jika false, maka tidak dapat terhubung ke database.
    
-   `CONNECTION LIMIT` : Menentukan koneksi bersamaan maksimum ke database baru. Defaultnya adalah -1 yang artinya tidak terbatas.
    
- `IS_TEMPLATE` : Jika true, setiap pengguna dengan hak istimewa CREATEDB dapat mengkloningnya. Jika false, hanya pengguna super atau pemilik database yang dapat mengkloningnya.

### Alter Database
Statement `ALTER DATABASE` dapat melakukan:
#### Mengubah atribut database
    ALTER  DATABASE  name  WITH  option;
Dengan option:
- `IS_TEMPLATE` 
- `CONNECTION LIMIT`
-  `ALLOW_CONNECTIONS`

#### Mengubah nama database
    ALTER DATABASE database_name
    RENAME TO new_name;
#### Mengubah pemilik database
    ALTER DATABASE database_name
    OWNER TO new_owner | current_user | session_user;
#### Mengubah tablespace default database

    ALTER DATABASE database_name
    SET TABLESPACE new_tablespace;
#### Mengubah default sesi untuk variabel konfigurasi run-time

    ALTER DATABASE database_name
    SET configuration_parameter = value;

### Drop Database
Statement `DROP DATABASE` menghapus database dari server PostgreSQL. Syntax nya adalah:

    DROP DATABASE [IF EXISTS] database_name
    [WITH (FORCE)]
   - Jika database tidak ditemukan, PostgreSQL akan error.
   - `DROP DATABASE` akan error, jika masih terkoneksi ke database tersebut kecuali menggunakan option FORCE
   - Statement `DROP DATABASE` menghapus database dari kedua catalog entry dan data directory. Karena PostgreSQL tidak mengizinkan mengembalikan operasi ini, kita harus menggunakannya dengan hati-hati.

### Copy Database
Untuk melakukan copy database dalam server yang sama adalah:

    CREATE DATABASE targetdb 
    WITH TEMPLATE sourcedb;

Untuk melakukan copy database dalam server yang berbeda adalah:

 1. `pg_dump -U postgres -d sourcedb -f sourcedb.sql`
2.  `CREATE  DATABASE targetdb;`
3.  `psql -U postgres -d targetdb -f sourcedb.sql`

## Managing Schemas
Schema adalah **wadah yang berisi logika dari tabel dan objek lain nya pada sebuah database.** Schema memungkinkan kita mengatur dan memberi nama pada objek database di dalam database.

Untuk mengakses objek dalam schema, Kita perlu mengkualifikasi objek tersebut dengan menggunakan sintaks berikut:

    schema_name.object_name

Sebuah database mungkin berisi satu atau lebih schema. Namun, suatu schema hanya dimiliki oleh satu database. Selain itu, dua schema dapat memiliki objek berbeda yang memiliki nama yang sama.

### Create Schema
Pernyataan `CREATE SCHEMA` memungkinkan membuat skema baru di database saat ini. Berikut sintaks nya: 

    CREATE SCHEMA [IF NOT EXISTS] schema_name;

Dalam sintaks ini:
- Pertama, tentukan nama schema setelah kata kunci `CREATE SCHEMA`. Nama schema harus unik dalam database saat ini.
- Kedua, secara opsional gunakan `IF NOT EXISTS` untuk membuat schema baru secara kondisional hanya jika schema tersebut tidak ada. Mencoba membuat schema baru yang sudah ada tanpa menggunakan opsi `IF NOT EXISTS` akan menghasilkan error.

Kita juga dapat membuat schema untuk pengguna:

    CREATE SCHEMA [IF NOT EXISTS] 
    AUTHORIZATION username;

Dalam hal ini, schema akan memiliki nama yang sama dengan nama **username**


PostgreSQL memungkinkan kita membuat schema dan list of objects seperti table dan views menggunakan satu pernyataan sebagai berikut:

    CREATE SCHEMA schema_name
        CREATE TABLE table_name1 (...)
        CREATE TABLE table_name2 (...)
        CREATE VIEW view_name1
            SELECT select_list FROM table_name1;

### Alter Schema
Pernyataan `ALTER SCHEMA` memungkinkan untuk mengubah definisi schema. Misalnya, Kita dapat mengganti nama schema sebagai berikut:

    ALTER SCHEMA schema_name RENAME TO new_name;

Selain mengganti nama schema, `ALTER SCHEMA` juga memungkinkan untuk mengubah pemilik schema ke yang baru seperti berikut:

    ALTER SCHEMA schema_name 
    OWNER TO { new_owner | CURRENT_USER | SESSION_USER};

### Drop Schema
`DROP SCHEMA` menghapus schema dan semua objectnya dari database. Berikut sintaks nya:

    DROP SCHEMA [IF EXISTS] schema_name 
    [ CASCADE | RESTRICT ];
Dalam sintaks ini:

- Pertama, tentukan nama schema yang ingin dihapus setelah kata kunci `DROP SCHEMA.`
- Kedua, gunakan opsi `IF EXISTS` untuk menghapus schema secara kondisional hanya jika schema itu ada.
- Ketiga, gunakan `CASCADE` untuk menghapus schema dan semua objek yang bergantung pada objek tersebut. Jika ingin menghapus schema hanya ketika schema itu kosong, kita dapat menggunakan opsi `RESTRICT`. Secara **default**, `DROP SCHEMA` menggunakan opsi `RESTRICT`.

PostgreSQL memungkinkan untuk menghapus beberapa schema secara bersamaan dengan menggunakan satu pernyataan :

    DROP SCHEMA [IF EXISTS] schema_name1 [,schema_name2,...] 
    [CASCADE | RESTRICT];

## Managing Tablespace
Tablespace adalah **lokasi disk tempat PostgreSQL menyimpan file data yang berisi objek database seperti indeks dan tabel.**

PostgreSQL memiliki dua tablespace default: 
- `pg_default` tablespace menyimpan data pengguna. 
- `pg_global` tablespace menyimpan data global.

### Create Tablespace
Untuk membuat tablespace baru, gunakan pernyataan `CREATE TABLESPACE`, seperti berikut:

    CREATE TABLESPACE tablespace_name
    OWNER user_name
    LOCATION directory_path;

Setelah membuat Tablespace baru, Kita dapat menentukannya dalam pernyataan `CREATE DATABASE`, `CREATE TABLE` dan `CREATE INDEX` untuk menyimpan file data object di tablespace.

### Alter Tablespace
Setelah membuat tablespace, Kita dapat mengubah definisinya dengan menggunakan pernyataan `ALTER TABLESPACE` sebagai berikut:

    ALTER TABLESPACE tablespace_name
    action;

action nya dapat berupa: 

#### Ganti nama 
    ALTER TABLESPACE tablespace_name 
    RENAME TO new_name;

#### Ganti pemilik

    ALTER  TABLESPACE tablespace_name 
    OWNER TO new_owner;

#### Menetapkan parameter

    ALTER  TABLESPACE tablespace_name 
    SET parameter_name = value;

### Drop Tablespace
Pernyataan `DROP TABLESPACE` menghapus tablespace dari database:

    DROP  TABLESPACE [IF  EXISTS] tablespace_name;
Dalam sintaks ini:

- Pertama, tentukan nama tablespace yang ingin dihapus setelah kata kunci `DROP TABLESPACE`.
- Kedua, gunakan opsi `IF EXISTS` untuk menginstruksikan PostgreSQL agar mengeluarkan pemberitahuan alih-alih error ketika tablespace tidak ada.

Hanya pemilik tablespace atau pengguna super yang dapat menjalankan pernyataan `DROP TABLESPACE` untuk menghapus tablespace.
