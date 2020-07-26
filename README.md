[![Build Status](https://travis-ci.com/algorand/go-algorand.svg?token=25XP72ADqbCQJ3TJVC9S&branch=master)](https://travis-ci.com/algorand/go-algorand)

go-algorand
====================
Implementasi resmi Algorand Go

[Algorand](https://algorand.com) adalah blockchain murni tanpa izin, murni yang memberikan
desentralisasi, skalabilitas, keamanan, dan finalitas transaksi.

## Pengenalan ##

[Situs web pengembang](https://developer.algorand.org/) Algorand memiliki informasi terbaru
tentang menggunakan dan menginstal platform algorand.
## Refferensi untuk Memulai ##

Pengembangan dilakukan menggunakan [Bahasa Program Golang](https://golang.org/).
Versi go ditentukan dalam file [go.mod](https://github.com/algorand/go-algorand/blob/master/go.mod). Jika Anda memerlukan bantuan, silakan kunjungi
[situs web dokumentasi resmi Go](https://golang.org/doc/).

Kami saat ini berusaha agar bisa di kembangkan di Debian dengan Ubuntu 18.04
saat ini Tim Teknik Algorand menggunakan Linux dan OSX.

sebelum melanjutkan silahkan [install (brew)](https://brew.sh). [Baca persyaratan instalasi
 disini](https://docs.brew.sh/Instalation)
 
Pengaturan Awal:
```bash
git clone https://github.com/algorand/go-algorand
cd go-algorand
./scripts/configure_dev.sh
```


Pada titik ini Anda siap membangun go-algorand. Kami menggunakan `make` dan sudah memiliki 
beberapa target untuk mengotomatisasi tugas-tugas umum.
#### build
```bash
make install
```

#### test
```bash
# unit tests
make test

# integration tests
make integration
```

#### style and checks
```bash
make fmt
make lint
make fix
make vet
```
or alternatively
```bash
make sanity
```

### menjalankan node

Setelah perangkat lunak di siapkan, Anda akan menemukan binari di `$ {GOPATH} / bin`, dan sebuah data
direktori akan diinisialisasi di `~ / .algorand`. Mulai node dengan
`${GOPATH}/bin/goal node start -d ~/.algorand`, lalu gunakan` $ {GOPATH} / bin / carpenter -d
~ / .algorand` untuk melihat aktivitas. 
Kunjungi [Forum Algorand Developer](https://developer.algorand.org/) untuk lebih lengkap nya

#### Menyediakan direktori data Anda sendiri

Anda dapat menjalankan node dari direktori lain selain `~ / .algorand` dan bergabung dengan jaringan
selain mainnet. Cukup buat direktori baru dan salin ke dalamnya
File `genesis.json` untuk jaringan. Sebagai contoh:
```bash
mkdir ~/testnet_data
cp installer/genesis/testnet/genesis.json ~/testnet_data/genesis.json
${GOPATH}/bin/goal node start -d ~/testnet_data
```

File Genesis untuk mainnet, testnet, dan betanet dapat ditemukan di
`installer / genesis /`.

## Kontribusi Kode, Dokumentasi, Bug, Dll) ##

Silahkan kunjungi [CONTRIBUTING](CONTRIBUTING.md) document.


## Layout proyek ##
`go-algorand` dibagi menjadi berbagai sub paket.

Paket-paket berikut memberikan fungsionalitas inti ke `algod` dan` kmd`
daemon, serta alat dan perintah lainnya:

  - `crypto` berisi konstruksi kriptografi yang kami gunakan untuk hashing,
    tanda tangan, dan VRF. Ada juga beberapa perincian spesifik Algorand di sini
    tentang kunci pengeluaran, kunci protokol, kunci masuk sekali pakai, dan bagaimana mereka
    saling berhubungan
  - `config` menyimpan parameter konfigurasi. Ini termasuk parameter yang digunakan
    secara lokal oleh node serta parameter yang harus disepakati oleh
    protokol.
  - `data` mendefinisikan berbagai jenis yang digunakan di seluruh basis kode.
     - `basics` memiliki tipe dasar seperti MicroAlgos, data akun, dan
       alamat.
     - `akun` mendefinisikan akun, termasuk akun" root "(yang dapat
       menghabiskan uang) dan akun "partisipasi" (yang dapat berpartisipasi dalam satu
       protokol).
     - `transaksi` mendefinisikan transaksi yang terdapat di ekosistem Algorand.
     Ini termasuk pembayaran standar dan juga
       transaksi pendaftaran pada  key registration .
     - `pembukuan` mendefinisikan blok, yang merupakan kumpulan transaksi
       secara atomik yang berjalan di atas Algorand.
     - `pool` mengimplementasikan kumpulan transaksiyang dilihat oleh node sebelum diusulkan dalam 
       blok.
     - `committee` mengimplementasikan kredensial yang mengotentikasi 
       keanggotaan akun yang berpartisipasi dalam protokol.
  - `ledger` ([README] (ledger / README.md)) berisi status Sistem Ledger Algorand
    , yang menampung urutan blok. Buku Besar yang mencatat semua perubahan yang terjadi di setiap block.
  - `protokol` menyatakan konstanta yang digunakan untuk mengidentifikasi versi protokol, tag untuk
    routing pesan jaringan, dan awalan untuk pemisahan 
    input kriptografi. Ini juga mengimplementasikan encoder kanonik.
  - `network` berisi kode untuk berpartisipasi dalam jaringan mesh berdasarkan
    soket web.
     - `rpcs` berisi HTTP RPC yang digunakan oleh proses` algod` untuk melakukan kueri
       lain.
  - `Agreement`[README](https://github.com/AlgorandID/go-algorand/blob/master/agreement/README.md/) berisi layanan perjanjian,
    yang mengimplementasikan protokol Perjanjian Bizantium Algorand. Protokol ini
    memungkinkan akun yang berpartisipasi untuk dengan cepat mengonfirmasi blok di garpu-aman
    cara, dengan ketentuan bahwa jumlah akun yang cukup menjalankan eksekusi dengan benar
    protokol.
  - `node` mengintegrasikan komponen di atas dan menangani inisialisasi dan
    mematikan. Ini memberikan pertanyaan ke dalam komponen-komponen ini.

`daemon` mendefinisikan dua daemon yang menyediakan layanan bagi klien Algorand:

 - `daemon / algod` memegang daemon` algod`, yang mengimplementasikan partisipasi
     node `algod` memungkinkan sebuah node untuk berpartisipasi dalam protokol,
     mengirim dan konfirmasi informasi sebuah transaksi. Silahkan Lihat Tentang Buku Besar Algorand.
      - `daemon / algod / api` [README](https://github.com/AlgorandID/go-algorand/blob/master/daemon/algod/api/README.md)
      
   - `daemon / kmd` [README](https://github.com/AlgorandID/go-algorand/blob/master/daemon/kmd/README.md) memegang daemon` kmd`. Ini
     memungkinkan node untuk menandatangani transaksi. Karena `kmd` terpisah dari
     `algod`,` kmd` memungkinkan pengguna untuk menandatangani transaksi pada komputer yang memiliki air-gapped computer. 

Paket-paket berikut memungkinkan pengembang untuk berinteraksi dengan sistem Algorand:

   - `cmd` memegang perintah utama yang mendefinisikan titik masuk ke dalam sistem.
      - `cmd / catchupsrv` ([README](https://github.com/AlgorandID/go-algorand/blob/master/cmd/catchupsrv/README.md) adalah alat untuk
        membantu memproses blok bersejarah pada simpul baru.
   - `libgoal` mengekspor Interface Go yang berguna untuk pengembang klien Algorand.
   - `debug` memegang perintah sekunder yang membantu pengembang selama debugging.

Paket-paket berikut berisi alat untuk membantu pengembang Algorand menggunakan jaringan
milik mereka sendiri:

   - `nodecontrol`
   - `tools`
   - `docker`
   - `commandandcontrol`[README](https://github.com/AlgorandID/go-algorand/blob/master/test/commandandcontrol/README.md) adalah alat untuk
     mengotomatiskan jaringan instance algod.
   - `komponen`https://github.com/AlgorandID/go-algorand/blob/master/test/commandandcontrol/README.md
   - `netdeploy`

Sejumlah paket menyediakan utilitas untuk berbagai komponen:
  - `logging` adalah pembungkus` logrus`.
   - `util` berisi berbagai utilitas, termasuk code,sqlite wrapper,
     kumpulan goroutine, antarmuka penghitung waktu, metrik node, dan banyak lagi.

`test` berisi tes ujung ke ujung untuk komponen-komponen di atas.

## Translator - Algorand Indonesia ##
[Algorand](https://algorand.com)
[developer site url](https://developer.algorand.org/)
