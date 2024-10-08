Pada `docker-compose.yml`, bagian `networks` digunakan untuk mendefinisikan dan mengatur jaringan yang digunakan oleh layanan (containers) yang didefinisikan di dalam file tersebut. Dengan menggunakan jaringan (networks), beberapa container dapat berkomunikasi satu sama lain secara langsung melalui nama service atau container, tanpa harus menggunakan `localhost` atau menghubungkan port secara manual di host.

### Fungsi `networks` di `docker-compose.yml`

1. **Isolasi Antar Layanan (Service Isolation)**:
   Setiap layanan (service) di dalam `docker-compose.yml` akan diisolasi dalam jaringan yang ditentukan. Container dalam satu jaringan bisa berkomunikasi satu sama lain menggunakan nama layanan sebagai host. Namun, container di luar jaringan yang sama tidak bisa mengakses container lain kecuali port diekspos ke host.

2. **Kemudahan dalam Komunikasi Antar Container**:
   Dengan mendefinisikan jaringan, container yang berjalan dalam jaringan yang sama bisa berkomunikasi satu sama lain menggunakan nama container atau nama service. Misalnya, service `app` bisa mengakses PostgreSQL di service `db` dengan `host=db` alih-alih menggunakan IP address atau `localhost`.

3. **Default Networks**:
   Jika tidak ditentukan secara eksplisit, Docker Compose akan membuat jaringan default untuk container yang didefinisikan di dalam file `docker-compose.yml`.

4. **Custom Networks**:
   Kamu bisa mendefinisikan jaringan khusus (custom networks) dengan konfigurasi spesifik, seperti jenis jaringan (bridge, overlay, dll.) atau pengaturan driver.

### Contoh Sederhana `networks` di `docker-compose.yml`

```yaml
version: '3.8'
services:
  app:
    build:
      context: .
    ports:
      - "8080:8080"
    networks:
      - my-network

  db:
    image: postgres
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root_password
      POSTGRES_DB: mydb
    networks:
      - my-network

networks:
  my-network:
    driver: bridge
```

#### Penjelasan:

- **Services:**
  - **app**: Aplikasi Go atau layanan utama. Service ini akan berjalan di jaringan `my-network`.
  - **db**: Layanan PostgreSQL yang juga akan berjalan di jaringan yang sama, yaitu `my-network`.
  
- **Networks:**
  - `my-network`: Sebuah jaringan yang didefinisikan dengan driver `bridge`. Docker akan membuat jaringan ini, dan kedua layanan `app` dan `db` akan bergabung dalam jaringan ini sehingga bisa saling berkomunikasi.
  
#### Cara Kerja:

- `app` dan `db` dapat berkomunikasi satu sama lain melalui nama service mereka. Misalnya, dalam aplikasi Go, kamu bisa menyetel `DB_HOST=db` untuk menghubungkan aplikasi ke database, tanpa perlu mengetahui IP address PostgreSQL.
- Kedua container ini tidak dapat diakses dari luar kecuali port-port tertentu diekspos ke host, seperti yang dilakukan pada `ports: "8080:8080"` untuk service `app`.

### Jenis-Jenis Network Drivers

1. **Bridge** (default):
   - Jaringan internal Docker, ideal untuk komunikasi antar container yang berjalan di host yang sama.
   - Container dapat berkomunikasi satu sama lain di jaringan yang sama, tetapi tidak dengan container lain di luar jaringan tersebut.

2. **Host**:
   - Menggunakan jaringan host langsung, container akan menggunakan jaringan dan IP yang sama dengan host.
   - Tidak ideal jika kamu ingin isolasi antar container.

3. **Overlay**:
   - Digunakan dalam konteks Docker Swarm atau ketika container berjalan di beberapa host.
   - Memungkinkan container di beberapa host berkomunikasi dalam satu jaringan.

4. **None**:
   - Tidak ada jaringan. Container tidak akan memiliki akses ke jaringan apa pun.

### Kesimpulan:
Bagian `networks` dalam `docker-compose.yml` memungkinkan kamu untuk mengatur komunikasi antar container dalam satu atau beberapa jaringan. Ini memudahkan manajemen komunikasi tanpa harus menggunakan IP address atau memetakan port secara manual.
