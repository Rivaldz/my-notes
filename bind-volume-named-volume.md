Dalam Docker, ada dua mekanisme utama untuk melakukan mounting storage ke dalam container, yaitu **bind mounts** dan **named volumes**. Kedua mekanisme ini memungkinkan Anda menyimpan data di luar container sehingga data tidak hilang ketika container dihapus atau di-*rebuild*. Mari kita bahas lebih detail tentang kedua jenis ini dan kapan sebaiknya digunakan:

---

## **1. Bind Mounts**
Bind mounts mengizinkan Anda untuk *mount* file atau folder dari sistem host (mesin fisik atau virtual yang menjalankan Docker) ke dalam container di direktori tertentu.

### **Cara Kerja**:
- File atau folder di host langsung digunakan di dalam container, dengan *path* spesifik.
- Perubahan yang terjadi pada file di host akan langsung terlihat di container, dan sebaliknya.
- Lokasi *mount* pada host ditentukan secara eksplisit saat Anda menjalankan container.

### **Kapan Menggunakan**:
- **Development**: Bind mounts sangat berguna ketika Anda ingin menyinkronkan folder project lokal dengan container agar perubahan di file (misalnya kode aplikasi) segera terlihat dalam container tanpa harus rebuild container.
- **Kebutuhan khusus file host**: Jika Anda perlu akses ke file atau folder tertentu di host yang digunakan dalam proses di dalam container, bind mounts adalah pilihan yang tepat.

### **Contoh Bind Mount**:
Dalam `docker-compose.yml` atau perintah `docker run`:
```yaml
volumes:
  - ./app:/var/www/html
```
Atau menggunakan perintah Docker CLI:
```bash
docker run -v /path/to/host/folder:/container/path my-app-image
```
**Penjelasan**:
- `./app` adalah folder di host.
- `/var/www/html` adalah direktori di dalam container tempat folder tersebut akan *mounted*.

### **Kelebihan**:
- **Kontrol penuh**: Anda bisa menentukan secara tepat direktori atau file dari sistem host yang di-*mount*.
- **Sederhana**: Cukup menentukan lokasi path, data akan langsung tersedia di container.

### **Kekurangan**:
- **Portability**: Jika Anda memindahkan project ke mesin lain, *path* yang berbeda bisa menyebabkan masalah karena *mount path* bergantung pada struktur folder di host.
- **Permissions**: Bisa terjadi masalah perizinan antara sistem file host dan container.

---

## **2. Named Volumes**
Named volumes adalah storage yang dikelola oleh Docker sendiri. Anda tidak menentukan lokasi fisik penyimpanannya di host; Docker yang menangani itu secara otomatis. Volume ini digunakan untuk menyimpan data yang persist di luar lifecycle container.

### **Cara Kerja**:
- Docker membuat dan mengelola volume di lokasi yang ditentukan oleh Docker engine (secara default di `/var/lib/docker/volumes/` di Linux).
- Data di dalam named volumes tidak terhapus meskipun container dihapus, kecuali volume tersebut dihapus secara eksplisit.
- Volume ini diidentifikasi dengan nama yang bisa Anda berikan saat membuatnya.

### **Kapan Menggunakan**:
- **Production**: Named volumes sering digunakan untuk aplikasi yang memerlukan data yang persist, misalnya database, data logs, atau data aplikasi yang tidak boleh hilang ketika container dimodifikasi atau di-*rebuild*.
- **Portability**: Volume ini bagus untuk portabilitas. Anda tidak perlu khawatir tentang di mana folder atau file berada di sistem host karena Docker yang mengelolanya.

### **Contoh Named Volume**:
Dalam `docker-compose.yml`:
```yaml
volumes:
  my-app-data:
    driver: local

services:
  app:
    image: my-app-image
    volumes:
      - my-app-data:/var/www/html/storage
```
**Penjelasan**:
- `my-app-data` adalah nama volume.
- `/var/www/html/storage` adalah path di dalam container tempat volume tersebut di-*mount*.

Atau menggunakan perintah Docker CLI:
```bash
docker run -v my-app-data:/container/path my-app-image
```

### **Kelebihan**:
- **Portability**: Karena Docker yang mengelola volume, Anda tidak perlu mengkhawatirkan lokasi spesifik dari data tersebut di host.
- **Persistence**: Data di named volumes tidak akan terhapus kecuali Anda secara eksplisit menghapus volume tersebut.
- **Keamanan**: Volumes tidak terikat dengan struktur file sistem host secara langsung, sehingga lebih aman dari perubahan tidak sengaja.

### **Kekurangan**:
- **Akses langsung terbatas**: Anda tidak bisa langsung mengakses file di volume dari host kecuali menggunakan perintah Docker seperti `docker cp` atau `docker run`.

---

## **Perbandingan Bind Mounts vs Named Volumes**

| **Fitur**                  | **Bind Mounts**                                            | **Named Volumes**                                       |
|----------------------------|------------------------------------------------------------|---------------------------------------------------------|
| **Definisi**                | Meng-*mount* folder/file host ke container.                | Docker yang mengelola storage, tanpa menentukan path host. |
| **Control**                 | Path di host harus ditentukan secara eksplisit.            | Docker menangani penyimpanan secara otomatis.            |
| **Portability**             | Kurang portable, bergantung pada struktur folder di host.  | Lebih portable, Docker yang mengatur penyimpanan.        |
| **Use Case**                | Development, testing, akses file host.                     | Production, persistensi data (database, logs, dll.).     |
| **Performance**             | Sedikit lebih lambat jika digunakan secara ekstensif.      | Performanya lebih baik dalam jangka panjang.             |
| **Access to data**          | Akses langsung ke data di host.                            | Akses data di volume hanya dengan Docker CLI.            |
| **Data management**         | Data ada di host, bisa diakses langsung.                   | Docker menyimpan dan mengelola data di dalam environment-nya. |

---

## **Named Volumes vs Bind Mounts: Kapan Menggunakan yang Mana?**

- **Bind Mounts**:
  - Untuk **development** agar kode project di host langsung terupdate di container tanpa rebuild.
  - Jika perlu akses file/folder di host secara real-time dari dalam container.

- **Named Volumes**:
  - Untuk **production**, karena lebih aman dan persist data lebih baik.
  - Untuk **data critical** yang harus persist tanpa tergantung struktur file di host.
  - Untuk **database**, **logs**, atau **cache** yang harus tersimpan meskipun container di-*rebuild*.

---

## **Kesimpulan**:
- **Bind mounts** cocok untuk situasi di mana Anda memerlukan akses langsung ke file sistem host, seperti saat mengembangkan aplikasi.
- **Named volumes** adalah pilihan yang lebih baik untuk menyimpan data di production karena lebih aman dan mudah diatur tanpa perlu memikirkan path di host.
