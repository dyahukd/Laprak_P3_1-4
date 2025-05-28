# Laprak_P3_1-4

### Soal 1

> Sebelum membuat sistem operasi, Budiman diberitahu dosennya bahwa Ia harus melakukan beberapa tahap terlebih dahulu. Tahap-tahapan yang dimaksud adalah untuk mempersiapkan seluruh prasyarat dan melakukan instalasi-instalasi sebelum membuat sistem operasi. Lakukan seluruh tahapan prasyarat hingga perintah ini pada modul!

**Answer:**

* **Code:**

  ```bash
  # Update package list dan install seluruh dependensi yang dibutuhkan
  sudo apt -y update
  sudo apt -y install qemu-system build-essential bison flex libelf-dev libssl-dev bc grub-common grub-pc libncurses-dev libssl-dev mtools grub-pc-bin xorriso tmux

  # Buat direktori kerja dan masuk ke dalamnya
  mkdir -p osboot
  cd osboot

  # Unduh source code kernel Linux versi 6.1.1
  wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.1.tar.xz

  # Ekstrak arsip kernel
  tar -xvf linux-6.1.1.tar.xz
  cd linux-6.1.1

  # Konfigurasi kernel
  make tinyconfig
  make menuconfig
  ```

  > **Pengaturan menuconfig :**

  ```
  [✔] General Setup > Configure standard kernel features > Enable support for printk
  [✔] General Setup > Configure standard kernel features > Enable futex support
  [✔] General Setup > Initial RAM filesystem and RAM disk (initramfs/initrd) support
  [✔] General Setup > Control Group Support
  [✔] Enable the block layer > Legacy autoloading support
  [✔] Enable the block layer > Partition type > Advanced Partition Selection
  [✔] Device Drivers > Character devices > Enable TTY
  [✔] Device Drivers > Character devices > Virtio console
  [✔] Device Drivers > Character devices > /dev/mem virtual device support
  [✔] Device Drivers > Network device support > Virtio Network Driver
  [✔] Device Drivers > Serial ATA / Parallel ATA
  [✔] Device Drivers > Block Devices > Virtio block driver
  [✔] Device Drivers > Block Devices > loopback device support
  [✔] Device Drivers > Block Devices > RAM block device support
  [✔] Device Drivers > Virtio drivers
  [✔] Device Drivers > Virtualization Drivers
  [✔] Device Drivers > Generic Driver Options > Maintain a devtmpfs at filesystems
  [✔] Device Drivers > Generic Driver Options > Automount devtmpfs at /dev
  [✔] Executable file formats > Kernel Support for ELF binaries
  [✔] Executable file formats > Kernel Support scripts starting with #!
  [✔] File Systems > FUSE support
  [✔] File Systems > The extended 3 filesystem
  [✔] File Systems > The extended 4 filesystem
  [✔] File Systems > Second extended fs support
  [✔] File Systems > Virtio Filesystem
  [✔] File Systems > Kernel automounter support
  [✔] File Systems > Pseudo File Systems > /proc file system support
  [✔] File Systems > Pseudo File Systems > sysctl support
  [✔] File Systems > Pseudo File Systems > sysfs file system support
  [✔] Networking Support > Networking options > Unix domain sockets
  [✔] Networking Support > Networking options > TCP/IP Networking
  ```

  ```bash
  # Kompilasi kernel menggunakan seluruh core
  make -j$(nproc)

  # Salin hasil build kernel ke direktori osboot
  cp arch/x86/boot/bzImage ..
  ```

---

* **Explanation:**

  * **Instalasi Dependensi:**
    Semua tools seperti `qemu`, `build-essential`, `bison`, `libssl-dev`, `grub-pc`, dll., dibutuhkan untuk membangun dan menjalankan kernel hasil compile secara virtual.

  * **Persiapan Direktori:**
    `osboot` digunakan sebagai workspace untuk seluruh file OS, mulai dari kernel hingga file initramfs.

  * **Download Kernel Linux:**
    Menggunakan `wget` untuk mengambil kernel Linux versi 6.1.1 dari situs resmi.

  * **Ekstraksi dan Konfigurasi Kernel:**
    File `.tar.xz` diekstrak lalu kernel dikonfigurasi menggunakan:

    * `make tinyconfig`: konfigurasi minimal
    * `make menuconfig`: untuk mengaktifkan berbagai fitur penting seperti driver dan filesystem.

  * **Build Kernel:**
    Kernel dikompilasi dengan memanfaatkan semua inti prosesor (`make -j$(nproc)`), menghasilkan file `bzImage` yang siap digunakan sebagai kernel bootable.

---

* **Screenshot :**

1. Hasil akhir `sudo apt install ...` (bagian terminal setelah selesai install) : ![](https://drive.google.com/uc?export=view&id=1rd2SArbf4lQBh_kLMVacnSIbIYr96h6j)
   Pengunduhan Source Kode Kernel : ![](https://drive.google.com/uc?export=view&id=1gxqU7XmlDSzsoQaQuz7VFwowCYHCuGQO)
2. Proses ekstraksi kernel (`tar -xvf`) : ![](https://drive.google.com/uc?export=view&id=1tle67yNLWwJjzwfKfKTF_1DN2_PWShwG)
3. Tampilan `make menuconfig` saat mencentang salah satu opsi (misal: `/proc file system support`) : ![](https://drive.google.com/uc?export=view&id=1JzVn7yT2GJteOp-zoPo4vP-zRmm_caBu)
4. Proses kompilasi kernel (`make -j$(nproc)`) : ![](https://drive.google.com/uc?export=view&id=1p-OLIyFdMcPdq2bxwvP8ekY2CSW8yOBh)
5. Bukti file `bzImage` berhasil dibuat di direktori `osboot` : ![](https://drive.google.com/uc?export=view&id=1OKavAZshsI5WqRkTHm-3ujKvgBwb1ngn)

---

### Soal 2

> Setelah seluruh prasyarat siap, Budiman siap untuk membuat sistem operasinya. Dosen meminta untuk sistem operasi Budiman harus memiliki directory `bin`, `dev`, `proc`, `sys`, `tmp`, dan `sisop`. Lagi-lagi Budiman meminta bantuanmu. Bantulah Ia dalam membuat directory tersebut!

**Answer:**

* **Code:**

  ```bash
  # Masuk sebagai root/superuser
  sudo bash

  # Jalankan perintah ini di dalam direktori osboot
  mkdir -p myramdisk/{bin,dev,proc,sys,tmp,sisop,etc,root,home}
  ```

* **Explanation:**

  * **`sudo bash`**
    Perintah ini digunakan untuk membuka shell sebagai root, agar pengguna memiliki izin penuh untuk membuat dan memodifikasi struktur direktori sistem operasi yang sedang dikembangkan.

  * **`mkdir -p myramdisk/{bin,dev,proc,sys,tmp,sisop,etc,root,home}`**

    * `mkdir -p` adalah perintah untuk membuat direktori secara rekursif. Artinya, jika direktori `myramdisk` belum ada, maka akan otomatis dibuat terlebih dahulu.
    * `{bin,dev,proc,sys,tmp,sisop,etc,root,home}` adalah daftar direktori penting yang dibutuhkan oleh sistem operasi minimal:

      * `bin`: tempat menyimpan binary/command yang dapat dijalankan
      * `dev`: tempat perangkat virtual seperti `/dev/null`, `/dev/tty`, dsb.
      * `proc`: digunakan oleh filesystem `/proc` (informasi runtime kernel)
      * `sys`: untuk filesystem `/sys`, menyimpan info sistem dan device
      * `tmp`: tempat file sementara
      * `sisop`: direktori tambahan khusus sistem operasi Budiman
      * `etc`: konfigurasi sistem
      * `root`: home direktori untuk root user
      * `home`: direktori home untuk user biasa

* **Screenshot :**

1. Terminal saat menjalankan `mkdir -p myramdisk/...` di dalam direktori `osboot` : ![](https://drive.google.com/uc?export=view&id=1joNEQsVjy5YOGv0yh8Mujuctaf3Oy4md)
2. Direktori myramdisk : ![](https://drive.google.com/uc?export=view&id=1dRjaoS8CEc2kqQmYUv7yLdfeCmxpJksZ)
3. Hasil direktori di myramdisk : ![](https://drive.google.com/uc?export=view&id=19qfJi386RivvWr5y7E0f3nvc5e4BaHWM)

---

### Soal 3

> Budiman lupa, Ia harus membuat sistem operasi ini dengan sistem **Multi User** sesuai permintaan Dosennya. Ia meminta kembali kepadamu untuk membantunya membuat beberapa user beserta direktori tiap user-nya di bawah direktori home. Buat pula password tiap user-usernya dan aplikasikan dalam sistem operasi tersebut!

**Answer:**

* **Code:**

  ```bash
  # Jalankan ini di direktori osboot
  mkdir -p myramdisk/home/{Budiman,guest,praktikan1,praktikan2}
  ```

  **Salin File Device ke Direktori `dev`:**

  ```bash
  cp -a /dev/null myramdisk/dev
  cp -a /dev/tty* myramdisk/dev
  cp -a /dev/zero myramdisk/dev
  cp -a /dev/console myramdisk/dev
  ```

  **Salin BusyBox ke Direktori `bin`:**

  ```bash
  cp /usr/bin/busybox myramdisk/bin
  cd myramdisk/bin
  ./busybox --install .
  ```

  **Generate Hash Password dengan `openssl`:**

  ```bash
  openssl passwd -1 Iniroot
  openssl passwd -1 PassBudi
  openssl passwd -1 guest
  openssl passwd -1 praktikan1
  openssl passwd -1 praktikan2
  ```

  **Isi file `passwd` di `myramdisk/etc/passwd`:**

  ```text
  root:$1$Y05VNICS$vfe1EftFcIXznqBsQOhH.0:0:0:root:/root:/bin/sh
  Budiman:$1$G4621z5S$m5aYdSYqru7xzCxwiER7M1:1001:100:Budiman:/home/Budiman:/bin/sh
  guest:$1$EGTsq3bA$A1cRPCD2yx3IjXMlZQFIh1:1002:100:guest:/home/guest:/bin/sh
  praktikan1:$1$uDRr5AsW$0BCffCfqD.IemSQB7L8ac.:1003:100:praktikan1:/home/praktikan1:/bin/sh
  praktikan2:$1$fKrl8Y25$phGb94mMxeSBkODD8ewxU0:1004:100:praktikan2:/home/praktikan2:/bin/sh
  ```

  > *Nilai hash password sesuai hasil `openssl passwd -1 ...` di komputer masing-masing.*

  **Isi file `group` di `myramdisk/etc/group`:**

  ```text
  root:x:0:
  bin:x:1:root
  sys:x:2:root
  tty:x:5:root,Budiman,guest,praktikan1,praktikan2
  disk:x:6:root
  wheel:x:10:root,Budiman,guest,praktikan1,praktikan2
  users:x:100:Budiman,guest,praktikan1,praktikan2
  ```

---

* **Explanation:**

  * **Pembuatan Direktori User:**
    Direktori `myramdisk/home/` digunakan sebagai basis home untuk setiap user:

    * `/home/Budiman`
    * `/home/guest`
    * `/home/praktikan1`
    * `/home/praktikan2`

  * **Penyalinan Device File:**
    File seperti `/dev/null`, `/dev/tty`, `/dev/zero`, dan `/dev/console` sangat penting agar shell dan aplikasi dapat berjalan dengan baik. Penyalinan dengan `-a` menjaga permission dan metadata.

  * **BusyBox:**
    Merupakan binary utilitas Unix all-in-one. Setelah disalin ke folder `bin`, perintah `./busybox --install .` membuat symlink untuk semua perintah seperti `ls`, `cp`, `cat`, dsb., agar shell bisa menjalankannya.

  * **Hash Password:**
    `openssl passwd -1` digunakan untuk menghasilkan hash MD5 yang digunakan di dalam file `passwd` agar aman dan tidak disimpan dalam bentuk teks biasa.

  * **File `passwd` dan `group`:**
    Kedua file ini berfungsi sebagai basis sistem manajemen user:

    * `passwd`: menyimpan informasi login, UID, GID, direktori home, shell, dan password hash.
    * `group`: menentukan grup mana saja yang dimiliki oleh user-user tersebut.

---

* **Screenshot:**

1. Isi direktori `myramdisk/home` : ![](https://drive.google.com/uc?export=view&id=19qfJi386RivvWr5y7E0f3nvc5e4BaHWM)
2. Isi direktori `myramdisk/dev` setelah penyalinan device file : ![](https://drive.google.com/uc?export=view&id=1oQwi6nvUVl9mIUgTbLgSUZDlD1kuSfB5)
3. Hasil dari `openssl passwd -1 ...` untuk masing-masing user : ![](https://drive.google.com/uc?export=view&id=1V5hFHeHFctj-0jo1M97ff4_ix_dKIfbP)
4. Isi direkrori `myramdisk/etc` : ![](https://drive.google.com/uc?export=view&id=1V5hFHeHFctj-0jo1M97ff4_ix_dKIfbP)
5. Isi file `passwd` dan `group` di `myramdisk/etc/` : ![](https://drive.google.com/uc?export=view&id=1mJgGeTIZCq_zkjdAqIB1bjPARUmZ93RB)
   ![](https://drive.google.com/uc?export=view&id=1gKIdb1JH9tM8fXW3cf3yU6ZJvhiJGsaD)

---

### Soal 4

> Dosen meminta Budiman membuat sistem operasi ini memiliki **superuser** layaknya sistem operasi pada umumnya. User `root` yang sudah kamu buat sebelumnya akan digunakan sebagai superuser dalam sistem operasi milik Budiman. Superuser yang dimaksud adalah user dengan otoritas penuh yang dapat mengakses seluruhnya. Akan tetapi user lain **tidak boleh memiliki otoritas yang sama**. Dengan begitu user-user selain root tidak boleh mengakses `./root`.
> Buatlah sehingga tiap user selain superuser **tidak dapat mengakses `./root`**!

**Answer:**

* **Code:**

  Jalankan perintah berikut di dalam direktori `myramdisk`:

  ```bash
  chown root:root root
  chmod 700 root
  ```

---

* **Explanation:**

  * **`chown root:root root`**

    * Perintah ini mengubah kepemilikan direktori `root` agar dimiliki oleh user `root` dan grup `root`.
    * Hal ini memastikan bahwa hanya user dengan UID 0 (superuser) yang memiliki hak kepemilikan atas folder tersebut.

  * **`chmod 700 root`**

    * Mengatur permission direktori `root` menjadi:

      * **`7` (rwx)** untuk pemilik (user root)
      * **`0` (- - -)** untuk grup
      * **`0` (- - -)** untuk user lain
    * Ini berarti **hanya root** yang dapat membaca, menulis, dan mengeksekusi (masuk ke dalam) direktori `./root`.
    * User lain seperti `Budiman`, `guest`, dan `praktikanX` **tidak akan bisa membuka atau mengakses isi folder tersebut**.

  * Dengan konfigurasi ini, sistem operasi Budiman telah menerapkan **prinsip privilege separation**, yaitu hanya root user yang memiliki otoritas penuh.

---

* **Screenshot:**

1. Permission dan Owner Berubah ke Root : ![](https://drive.google.com/uc?export=view&id=14LH0GAa-jWyWrNHhf4JhJE6c55zeE2Al)
2. Akses Direktori Root Oleh User Non-Root Gagal : ![](https://drive.google.com/uc?export=view\&id=1K42kzpy51ZgKBubBM3tvzB1Y5FwWgKx_)

---
