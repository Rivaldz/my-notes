Ketika kamu menambahkan layanan baru (app ketiga, misalnya Laravel) ke dalam `docker-compose.yml`, kamu **tidak selalu perlu melakukan build ulang untuk semua layanan**. Hanya layanan yang memerlukan perubahan atau build ulang yang perlu dibangun kembali. Berikut penjelasan lebih detail terkait skenario penambahan aplikasi baru di Docker Compose:

### 1. **Penambahan Layanan Baru (Contoh Laravel)**
Jika kamu menambahkan layanan Laravel sebagai `app3` dalam `docker-compose.yml`, layanan yang sudah ada seperti `app1` dan `app2` tidak perlu di-build ulang kecuali ada perubahan dalam konteks build-nya.

Contoh penambahan layanan Laravel ke dalam `docker-compose.yml` yang sudah ada:

```yaml
version: '3.8'
services:
  app1:
    build:
      context: ./app1
      dockerfile: Dockerfile
    ports:
      - "8081:8080"
    networks:
      - my-network

  app2:
    build:
      context: ./app2
      dockerfile: Dockerfile
    ports:
      - "8082:8080"
    networks:
      - my-network

  app3:
    build:
      context: ./laravel-app
      dockerfile: Dockerfile
    ports:
      - "8083:80"
    networks:
      - my-network

networks:
  my-network:
    driver: bridge
```

Dalam contoh di atas:
- Kamu menambahkan `app3` yang berisi Laravel dengan build context `./laravel-app`.
- Layanan `app1` dan `app2` tidak memerlukan build ulang kecuali ada perubahan pada konteks atau Dockerfile mereka.

### 2. **Perintah yang Digunakan**
Setelah menambahkan layanan baru dalam `docker-compose.yml`, kamu bisa menggunakan perintah berikut untuk menambahkannya tanpa membuild ulang layanan lain:

```bash
docker-compose up -d --build app3
```

Penjelasan:
- `docker-compose up -d --build app3`: Ini hanya akan membuild `app3` (Laravel) yang baru ditambahkan dan tidak membuild ulang `app1` atau `app2`.
- `-d`: Untuk menjalankan container di latar belakang (detached mode).

### 3. **Menghindari Build Ulang yang Tidak Perlu**
Jika kamu menjalankan `docker-compose up --build` tanpa menyebutkan layanan secara spesifik, Docker Compose akan memeriksa semua layanan dan membuild ulang hanya jika ada perubahan pada konteks build atau Dockerfile. Jadi, jika `app1` dan `app2` tidak berubah, Docker Compose akan melewati build untuk mereka.

### 4. **Manajemen Volume dan Database**
Jika layanan Laravel (`app3`) memerlukan database, pastikan kamu sudah mengonfigurasi volume untuk data persisten dan menyambungkan ke container database (misalnya PostgreSQL atau MySQL) yang sudah ada.

Contoh tambahan untuk database:

```yaml
services:
  app3:
    build:
      context: ./laravel-app
      dockerfile: Dockerfile
    ports:
      - "8083:80"
    networks:
      - my-network
    depends_on:
      - db

  db:
    image: postgres
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    networks:
      - my-network
    volumes:
      - pgdata:/var/lib/postgresql/data

networks:
  my-network:
    driver: bridge

volumes:
  pgdata:
```

Dalam contoh ini, `app3` (Laravel) tergantung pada layanan `db` (PostgreSQL), sehingga keduanya terhubung melalui jaringan `my-network`.

### Kesimpulan:
- **Tidak perlu** build ulang semua layanan jika kamu hanya menambahkan layanan baru.
- Gunakan `docker-compose up -d --build <service>` untuk membuild layanan baru tanpa mempengaruhi layanan yang lain.
- Pastikan Docker Compose sudah terkonfigurasi dengan benar untuk jaringan dan volume jika ada ketergantungan seperti database atau storage.
