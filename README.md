![LOGO FOCALBOARD](https://github.com/user-attachments/assets/35149aaa-1c36-498d-86d0-76698f1e67b8)
# Focalboard
Untuk Proyek UTS Mata Kuliah Komunikasi Data dan Jaringan Komputer
| [Sekilas Tentang](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#sekilas-tentang) | [Instalasi](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#instalasi) | [Konfigurasi]( https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#konfigurasi) | [Cara Pemakaian](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#cara-pemakaian) | [Pembahasan](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#pembahasan) | [Referensi](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#referensi) |
| -- | -- | -- | -- | -- | -- |


## Sekilas Tentang

Focalboard adalah alat manajemen proyek open-source yang dapat membantu tim mengorganisir dan mengelola pekerjaan mereka. Focalboard memusatkan semua pekerjaan di satu tempat, sehingga setiap anggota dalam tim dapat lebih fokus dalam menyelesaikan tugas mereka. Focalboard juga mempermudah tim untuk mengatur dan memvisualisasikan pekerjaan mereka sesuai kebutuhan, sehingga tim dapat bekerja sesuai dengan style masing-masing.

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)


## Instalasi

1. Login ke perangkat linux
2. Update repository perangkat lunak
   ```
   sudo apt update
   ```
3. Install pembaruan perangkat lunak yang tersedia
   ```
   sudo apt upgrade -y
   ```
4. Install Nginx dan MySQL (MariaDB)
   ```
   sudo apt install nginx mariadb-server mariadb-client -y
   ```
5. Konfigurasi database MySQL (MariaDB) dengan masuk sebagai root dan lakukan konfigurasi awal
   ```
   sudo su
   mysql_secure_installation
   ```
6. Masuk ke MySQL dengan kata sandi root yang telah dibuat
   ```
   mysql -u root -p
   ```
7. Buat database dan pengguna untuk FocalBoard
   ```
   CREATE DATABASE focalboard DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
   GRANT ALL ON focalboard.* TO 'focalboard_rw'@'localhost' IDENTIFIED BY 'Foc@lB0a4d';
   FLUSH PRIVILEGES;
   EXIT;
   ```
8. Unduh dan ekstrak FocalBoard
   ```
   regex='"browser_download_url":"(https:\/\/github.com\/mattermost\/focalboard\/releases\/download\/[^/]*\/focalboard-server-linux-amd64\.tar\.gz)"' && response=$(curl -H "Accept: application/vnd.github.v3+json" https://api.github.com/repos/mattermost/focalboard/releases) && [[ $response =~ $regex ]] && downloadURL="${BASH_REMATCH[1]}"
   wget -O /tmp/focalboard.tar.gz $downloadURL
   ```
9. Ekstrak file yang telah diundur
    ```
    sudo tar -xvzf /tmp/focalboard.tar.gz -C /opt
    ```
10. Edit konfigurasi FocalBoard
   ```
   sudo nano /opt/focalboard/config.json
   ```
Ganti pengaturan dbtype dan dbconfig dengan:
   ```
   "dbtype": "mysql",
   "dbconfig": "focalboard_rw:Foc@lB0a4d@tcp(127.0.0.1:3306)/focalboard",
  ```
11. Konfigurasi Nginx
    ```
    sudo nano /etc/nginx/sites-available/focalboard
    ```
12. Konfigurasi Nginx
    ```
    sudo nano /etc/nginx/sites-available/focalboard
    ```
    Konfigurasi:
    ```
    upstream focalboard {
     server localhost:8000;
     keepalive 32;
    }
    server {
     listen 80 default_server;
     server_name focalboard.example.com;
     location ~ /ws/* {
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";
       client_max_body_size 50M;
       proxy_set_header Host $http_host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_set_header X-Forwarded-Proto $scheme;
       proxy_set_header X-Frame-Options SAMEORIGIN;
       proxy_buffers 256 16k;
       proxy_buffer_size 16k;
       client_body_timeout 60;
       send_timeout 300;
       lingering_timeout 5;
       proxy_connect_timeout 1d;
       proxy_send_timeout 1d;
       proxy_read_timeout 1d;
       proxy_pass http://focalboard;
     }
     location / {
       client_max_body_size 50M;
       proxy_set_header Connection "";
       proxy_set_header Host $http_host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_set_header X-Forwarded-Proto $scheme;
       proxy_set_header X-Frame-Options SAMEORIGIN;
       proxy_buffers 256 16k;
       proxy_buffer_size 16k;
       proxy_read_timeout 600s;
       proxy_cache_revalidate on;
       proxy_cache_min_uses 2;
       proxy_cache_use_stale timeout;
       proxy_cache_lock on;
       proxy_http_version 1.1;
       proxy_pass http://focalboard;
     }
    }
13. Hapus situs default Nginx dan aktifkan konfigurasi FocalBoard:
    ```
    sudo rm /etc/nginx/sites-enabled/default
    sudo ln -s /etc/nginx/sites-available/focalboard /etc/nginx/sites-enabled/focalboard
    sudo nginx -t && sudo /etc/init.d/nginx reload
    ```
14. Buat layanan FocalBoard
    ```
    sudo nano /lib/systemd/system/focalboard.service
    ```
    Konfigurasi:
    ```
    [Unit]
    Description=Focalboard server

    [Service]
    Type=simple
    Restart=always
    RestartSec=5s
    ExecStart=/opt/focalboard/bin/focalboard-server
    WorkingDirectory=/opt/focalboard

    [Install]
    WantedBy=multi-user.target
    Reload layanan dan mulai FocalBoard:
    sudo systemctl daemon-reload
    sudo systemctl enable focalboard.service --now
15. Reload layanan dan mulai FocalBoard:
    ```
    sudo systemctl daemon-reload
    sudo systemctl enable focalboard.service --now
    ```

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)

## Konfigurasi

Setelah melakukan instalasi website Focalboard pada localhost, perlu dilakukan hosting agar website Focalboard dapat diakses oleh seluruh orang melalui internet.
- Mengunjungi situs https://localtonet.com/download, karena Focalboard berbasis linux. Maka, download LocaltoNet berbasis linux dan sesuaikan dengan arsitektur perangkat Anda. Di sini kami download berbasis Linux-64 bit.
  ![Screenshot 2024-10-12 221519](https://github.com/user-attachments/assets/dc1659fb-60d5-482f-979c-999f63997eda)
- Sign up LocaltoNet pada situs [berikut](https://localtonet.com/Identity/Account/Register?returnUrl=%2F) menggunakan Email. Lalu, log in LocaltoNet dengan akun yang sudah dibuat.
  ![Screenshot 2024-10-12 221608](https://github.com/user-attachments/assets/1e2f50a9-4c8e-4768-94ff-ba24fe12b716)
- Pada LocaltoNet, buka halaman “My Tokens”. Pada halaman tersebut terdapat token yang akan digunakan pada step berikutnya.
  ![Screenshot 2024-10-12 221637](https://github.com/user-attachments/assets/05ddfb11-2e29-4c1b-a11b-931a5be83f93)

- Buka terminal Linux, lalu jalankan kode-kode berikut (per baris)
```
wget https://localtonet.com/download/localtonet-linux-x64.zip
unzip localtonet-linux-x64.zip
chmod 777 ./localtonet
./localtonet authtoken PASTE_HERE_COPIED_AUTHTOKEN
```
Pada bagian PASTE_HERE_COPIED_AUTHTOKEN ganti dengan token yang ada di step sebelumnya. 
Kode-kode tersebut dapat juga diakses melalui situs [berikut](https://localtonet.com/blog/how-to-use-localtonet).
- Kembali ke LocaltoNet, lalu ikuti langkah-langkah berikut:
  1. Buka halaman “My Tunnels”
  2. Pada bagian “Port” isi dengan angka 80
  3. Klik “Create” dan tunggu beberapa saat
  4. Jika sudah muncul URL, klik play button hijau pada bagian “Action” hingga button berubah warna menjadi merah
     ![Screenshot 2024-10-12 221704](https://github.com/user-attachments/assets/755e9644-59a6-4353-a6ee-023557102aac)
- Selanjutnya, kembali ke terminal, cek pada bagian “Status”. Jika sudah “OK”, kembali ke LocaltoNet dan klik URL. Lalu, klik “Visit Site”.
  ![Screenshot 2024-10-12 221732](https://github.com/user-attachments/assets/ac4096ef-da6d-4a2c-b3c7-3e17c4bd1dd3)
  ![Screenshot 2024-10-12 221801](https://github.com/user-attachments/assets/dfd54eab-da4f-4e3e-9326-29e58c4f48d6)
- Log in dengan akun yang sudah dibuat pada Focalboard menggunakan username dan password.
  ![Screenshot 2024-10-12 221820](https://github.com/user-attachments/assets/3c039421-7e24-41eb-8d27-64ee4cfdde90)
- Jika berhasil, coba bagikan URL di LocaltoNet tadi kepada teman Anda dan lakukan log in pada Focalboard. Jika bisa, selamat! Anda berhasil melakukan hosting Focalboard.
  ![Screenshot 2024-10-12 221840](https://github.com/user-attachments/assets/389f8b08-c3ef-40b0-bf10-f3fce1f0c541)

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)

## Cara Pemakaian

Cara pemakaian Focalboard ini sangat mudah, karena aplikasi ini telah menyediakan interface yang mudah dimengerti. Berikut untuk lebih jelasnya:
1. Sebelum menggunakan Focalboard kita harus login terlebih dahulu, jika belum memiliki akun maka harus registrasi
2. Setelah login maka akan terlihat tampilan menu Board
3. Menambahkan New Boards
  - Pilih + Tambah board di bagian kiri bawah bilah sisi untuk menambahkan board baru ke Focalboard.
  - Pilih template, seperti Tugas Proyek, dari daftar.
  - Ini menunjukkan tampilan pertama board baru, yang merupakan tabel semua tugas.
    ![Screenshot 2024-10-12 221914](https://github.com/user-attachments/assets/5f717a87-8884-406b-95a4-9edb6f816262)
  - Klik “Berdasarkan Status” di bilah sisi untuk melihat tampilan board.
    ![Screenshot 2024-10-12 221948](https://github.com/user-attachments/assets/92b37368-0009-4b6f-b2c3-36fa88355b60)
  - Klik “Berdasarkan Status” di bilah sisi untuk melihat tampilan board, klik menu properti, dan aktifkan “Prioritas” dan “Tanggal dibuat”. Ini akan menambahkan properti tersebut ke tampilan card.
    ![Screenshot 2024-10-12 222007](https://github.com/user-attachments/assets/334eab46-5059-49fb-b47d-95cdf491da35)
  - Platform ini juga memungkinkan pengguna untuk mengubah pengaturan Group By, Filter, dan Sortir pada tampilan.
  - Pada umumnya, semua perubahan yang Anda buat akan segera disimpan dan dapat dilihat oleh semua pengguna lainnya.
4. Menambahkan Tampilan baru dengan menu tarik-turun di samping nama tampilan, lalu klik Tambah tampilan untuk menambahkan tampilan baru
![Screenshot 2024-10-12 222044](https://github.com/user-attachments/assets/2c6dc969-13cd-4b1d-ab16-584a07803bdc)
  - Menyeret card: Seret card dari satu kolom ke kolom lainnya untuk mengubah properti per grup. Misalnya, seret card ke kolom Selesai untuk menandainya sebagai selesai.
  - Mengedit card
    - Satu set properti: Properti umum untuk semua card di board. Tampilan board dapat mengelompokkan card berdasarkan jenis properti “Pilih” ke dalam kolom yang berbeda.
    - Daftar komentar: Komentar berguna untuk mencatat perubahan atau pencapaian penting.
    - Satu set konten: Konten card dapat terdiri dari teks penurunan harga dan gambar. Gunakan ini untuk mencatat spesifikasi rinci atau keputusan desain untuk suatu item.
    Tekan ESC atau klik di luar dialog editor card untuk menutup editor card
  - Tampilan Tabel, dimana setiap kolom berhubungan dengan properti card. Sel dapat diedit secara langsung, atau Anda dapat membuka editor card untuk sebuah baris dengan mengarahkan kursor ke judul dan mengklik “open”. Klik pada judul untuk mengurutkannya, atau menyisipkan properti baru
    ![Screenshot 2024-10-12 222106](https://github.com/user-attachments/assets/c51ecb84-e1ed-424f-bdd3-6abb3a258400)
  - Card templates, dimana setiap board dapat memiliki satu set card template. Untuk membuatnya, klik pada dropdown di tombol "New" dan pilih “+ New template”.
  - Board templates, dimana Anda bisa membuat templat papan dari menu “+ Add board”. Untuk mengubah papan yang sudah ada menjadi template:
    - Arahkan kursor ke judul papan di bilah sisi.
    - Pilih menu opsi (...),
    - pilih Template baru dari papan.
      ![Screenshot 2024-10-12 222133](https://github.com/user-attachments/assets/0bda5a19-c8af-4945-a29e-d9da1677ebb6)
  - Sharing boards
    - Arahkan kursor ke sisi kanan  board menu.
    - Klik pada menu opsi (...) dan pilih Share board.
    - Nyalakan sakelar yang bertuliskan Publish to web and share this board with anyone
    - Salin tautan yang diberikan.
    - Anda sekarang dapat membagikan tautan yang disalin dengan orang lain. Siapa pun yang memiliki tautan tersebut akan dapat melihat board tersebut)
    - Pilih “Regenerate Token” jika Anda ingin membatalkan semua tautan yang telah dibagikan sebelumnya.
    - Sebuah pesan yang menanyakan “Ini akan membatalkan tautan yang dibagikan sebelumnya. Lanjutkan?” akan muncul. Pilih “OK” dan token akan dibuat ulang.
  - Arsip, dimana Focalboard ini juga memungkinkan pengguna mengekspor arsip semua board dari menu pengaturan.
    ![Screenshot 2024-10-12 222204](https://github.com/user-attachments/assets/8b396d2a-46cd-4224-ad6d-50607c9106cb)

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)

## Pembahasan

Focalboard menggunakan Node.js dan Go dalam pengoperasiannya, dan untuk database, dapat menggunakan MySQL maupun Postgresql. Sebagai alternatif dari Trello, Notion, dan alat manajemen proyek lainnya, kelebihan dari Focalboard antara lain:
- Sepenuhnya open source dan mengizinkan self-host tanpa adanya biaya tambahan sehingga lebih terjangkau untuk digunakan
- Memiliki user interface yang rapih, menarik, dan mudah dinavigasi yang meningkatkan pengalaman pengguna.

Namun demikian, masih dapat beberapa kekurangan pada Focalboard, yaitu, dikarenakan masih dalam pengembangan, Focalboard dalam beberapa kesempatan dapat sangat lambat prosesnya atau mengeluarkan pesan error seperti bug. Jika dibandingkan dengan alat manajemen proyek lain, Trello, perbedaan signifikan yang dapat terlihat adalah:
- Focalboard tidak memungut biaya apapun untuk dapat menggunakannya, sedangkan pengguna harus berlangganan untuk menggunakan Trello.
- Focalboard lebih cocok untuk digunakan oleh bisnis menengah kebawah, sedangkan Trello lebih reliabel sehingga cocok digunakan untuk perusahan besar.
- Focalboard hanya tersedia dalam bentuk browser atau cloud, sedangkan Trello dapat diakses melalui browser, perangkat genggam, maupun terunduh dalam komputer.
- Focalboard tidak memiliki API Support maupun in

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)

## Referensi

1. [Website Focalboard](https://www.focalboard.com/)
2. [Github Focalboard](https://github.com/mattermost-community/focalboard)
3. [Review Focalboard](https://alternativeto.net/software/focalboard/about/)
4. [Perbandingan Focalboard](https://www.spotsaas.com/compare/focalboard-vs-trello)
5. [Instalasi Localtonet](https://localtonet.com/blog/how-to-use-localtonet)

[`kembali ke atas`](https://github.com/allyadzhani/focalboard/tree/main?tab=readme-ov-file#Focalboard)
