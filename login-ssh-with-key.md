Berikut penjelasan lebih rinci dengan konteks bahwa **Server A** adalah server yang ingin Anda akses melalui SSH, dan **Ubuntu client** adalah mesin lokal yang Anda gunakan untuk melakukan koneksi ke **Server A**.

### Tujuan:
Anda ingin mengakses **Server A** dari **Ubuntu client** menggunakan SSH key pair (RSA), sehingga Anda tidak perlu memasukkan password setiap kali login ke **Server A**.

### Langkah-langkah:

#### 1. Generate RSA Key Pair di **Ubuntu Client**

Pada **Ubuntu client**, jalankan perintah berikut untuk membuat RSA key pair (private dan public key):

```bash
ssh-keygen -t rsa -b 4096
```

- **`-t rsa`**: Mengindikasikan bahwa Anda akan menggunakan algoritma RSA.
- **`-b 4096`**: Mengatur panjang kunci menjadi 4096 bit, yang lebih aman daripada 2048 bit.

**Proses**:
- **Enter file in which to save the key**: Tekan `Enter` untuk menyimpan kunci pada lokasi default (`/home/user/.ssh/id_rsa`).
- **Enter passphrase**: Anda bisa mengisi dengan passphrase untuk perlindungan tambahan, atau bisa dikosongkan jika Anda tidak menginginkan passphrase.

Setelah selesai, key pair akan dihasilkan:
- **Private key**: Disimpan di `/home/user/.ssh/id_rsa` (jangan pernah bagikan file ini).
- **Public key**: Disimpan di `/home/user/.ssh/id_rsa.pub` (file ini akan digunakan pada **Server A**).

#### 2. Salin Public Key ke **Server A**

Agar Anda bisa login ke **Server A** tanpa password, public key (`id_rsa.pub`) dari **Ubuntu client** harus ditambahkan ke **Server A**.

Ada dua cara untuk melakukannya:

##### Cara 1: Menggunakan `ssh-copy-id`

1. Di **Ubuntu client**, jalankan perintah berikut untuk menyalin public key ke **Server A**:
   ```bash
   ssh-copy-id user@serverA_ip
   ```

   - Gantilah `user` dengan nama pengguna di **Server A**.
   - Gantilah `serverA_ip` dengan alamat IP atau hostname dari **Server A**.

2. Anda akan diminta memasukkan password **Server A**. Setelah berhasil, public key akan ditambahkan ke file `~/.ssh/authorized_keys` di **Server A**.

##### Cara 2: Salin Manual Public Key

Jika `ssh-copy-id` tidak tersedia, Anda bisa melakukannya secara manual:

1. Di **Ubuntu client**, salin public key dengan menjalankan:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

2. Salin output dari perintah di atas.

3. Masuk ke **Server A** menggunakan SSH dengan password:
   ```bash
   ssh user@serverA_ip
   ```

4. Setelah login ke **Server A**, tambahkan public key ke file `~/.ssh/authorized_keys`:
   ```bash
   echo "PASTE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
   ```

5. Atur izin yang benar pada file `authorized_keys`:
   ```bash
   chmod 600 ~/.ssh/authorized_keys
   ```

#### 3. Uji Koneksi ke **Server A**

Sekarang coba lakukan login dari **Ubuntu client** ke **Server A** tanpa password:

```bash
ssh user@serverA_ip
```

Jika semuanya diatur dengan benar, Anda akan login tanpa diminta memasukkan password (kecuali jika Anda menggunakan passphrase).

Jika private key disimpan di lokasi non-standar, gunakan opsi `-i` untuk menentukan lokasi private key:

```bash
ssh -i /path/to/private_key user@serverA_ip
```

### Kesimpulan:

1. **Generate RSA Key Pair**: Buat kunci SSH di **Ubuntu client**.
2. **Salin Public Key ke Server A**: Gunakan `ssh-copy-id` atau salin secara manual ke **Server A**.
3. **Uji Koneksi**: Login ke **Server A** dari **Ubuntu client** tanpa password.

Dengan cara ini, Anda bisa mengakses **Server A** menggunakan SSH dengan autentikasi RSA key tanpa perlu memasukkan password setiap kali.
