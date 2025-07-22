# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: `<Nama Lengkap>`  
**NIM**: `<Nomor Induk Mahasiswa>`  
**Modul yang Dikerjakan**:  
**Modul 5 – Audit System Call dan Modifikasi Kernel xv6**

---

## 📌 Deskripsi Singkat Tugas

Modul ini bertujuan untuk menambahkan **fitur audit** terhadap pemanggilan syscall di sistem operasi `xv6`. Dengan fitur ini, kernel dapat mencatat syscall apa saja yang dipanggil oleh setiap proses beserta waktu terjadinya (tick). Hasil audit dapat ditampilkan melalui program user-level atau syscall tertentu.

---

## 🛠️ Rincian Implementasi

- Menambahkan file baru `audit.c` untuk menyimpan dan mengelola buffer audit syscall  
- Menambahkan struktur `struct audit_entry` di `defs.h` dan buffer audit kernel  
- Memodifikasi `syscall.c` untuk mencatat semua syscall yang dipanggil ke dalam audit log  
- Menambahkan syscall baru seperti `getaudit()` untuk mengambil isi audit buffer  
- Memodifikasi `sysproc.c`, `syscall.h`, `user.h`, dan `usys.S` untuk syscall audit  
- Menambahkan validasi: hanya proses dengan PID 1 yang dapat mengakses log audit  
- Memperbarui `Makefile` untuk memasukkan `audit.o` dalam kompilasi kernel  
- Menambahkan program user-space seperti `auditlog.c` untuk menampilkan log audit  

---

## ✅ Uji Fungsionalitas

- `auditlog`: program uji untuk mencetak isi log audit syscall  
- `auditattack`: program uji untuk mencoba akses log dari PID non-root  
- `auditfill`: program uji untuk mengisi log audit sampai penuh  
- Syscall normal (`ls`, `echo`, `mkdir`) juga secara otomatis masuk ke audit log  

---

## 📷 Hasil Uji
### 📍 Contoh Output `auditlog`:
