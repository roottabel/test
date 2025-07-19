🔧 A. Tambahkan File Baru: pinfo.h
📄 pinfo.h

c
Salin
Edit
struct pinfo {
  int pid[64];
  int mem[64];
  char name[64][16];
};
🧠 B. Modifikasi File yang Ada
1. 📄 user.h
Tambahkan di bagian bawah deklarasi syscall:

c
Salin
Edit
#include "pinfo.h"

int getpinfo(struct pinfo*);
int getreadcount(void);
2. 📄 usys.S
Tambahkan:

asm
Salin
Edit
SYSCALL(getpinfo)
SYSCALL(getreadcount)
3. 📄 defs.h
Tambahkan di bagian deklarasi syscall:

c
Salin
Edit
// syscall
int sys_getpinfo(void);
int sys_getreadcount(void);
4. 📄 syscall.h
Tambahkan syscall ID (setelah yang terakhir):

c
Salin
Edit
#define SYS_getpinfo     22
#define SYS_getreadcount 23
5. 📄 syscall.c
Tambahkan extern dan mapping:

c
Salin
Edit
extern int sys_getpinfo(void);
extern int sys_getreadcount(void);

...

static int (*syscalls[])(void) = {
  ...
  [SYS_getpinfo] = sys_getpinfo,
  [SYS_getreadcount] = sys_getreadcount,
};
6. 📄 sysproc.c
Tambahkan implementasi system call:

c
Salin
Edit
#include "types.h"
#include "x86.h"
#include "defs.h"
#include "date.h"
#include "param.h"
#include "memlayout.h"
#include "mmu.h"
#include "proc.h"
#include "pinfo.h"     // <== tambahkan ini
#include "spinlock.h"  // <== jika belum ada

extern int readcount;

int
sys_getreadcount(void) {
  return readcount;
}

int
sys_getpinfo(void) {
  struct pinfo *p;

  if (argptr(0, (void*)&p, sizeof(*p)) < 0)
    return -1;

  struct proc *cur;
  int i = 0;

  acquire(&ptable.lock);
  for(cur = ptable.proc; cur < &ptable.proc[NPROC]; cur++) {
    if(cur->state != UNUSED && i < 64){
      p->pid[i] = cur->pid;
      p->mem[i] = cur->sz;
      safestrcpy(p->name[i], cur->name, 16);
      i++;
    }
  }
  release(&ptable.lock);
  return 0;
}
7. 📄 sysfile.c
Tambahkan di atas file (global scope):

c
Salin
Edit
int readcount = 0;
Lalu di dalam sys_read() (sekitar baris 200-an), tambahkan:

c
Salin
Edit
readcount++;
Biasanya di bawah pengecekan if(f->read == 0).

🧪 C. Tambahkan Program Uji
📄 ptest.c
c
Salin
Edit
#include "types.h"
#include "stat.h"
#include "user.h"
#include "pinfo.h"

int main() {
  struct pinfo p;
  getpinfo(&p);

  printf(1, "PID\tMEM\tNAME\n");
  for(int i = 0; i < 64 && p.pid[i]; i++) {
    printf(1, "%d\t%d\t%s\n", p.pid[i], p.mem[i], p.name[i]);
  }
  exit();
}
📄 rtest.c
c
Salin
Edit
#include "types.h"
#include "stat.h"
#include "user.h"

int main() {
  char buf[10];
  printf(1, "Read Count Sebelum: %d\n", getreadcount());
  read(0, buf, 5); // baca stdin
  printf(1, "Read Count Setelah: %d\n", getreadcount());
  exit();
}
🧱 D. Modifikasi Makefile
📄 Makefile

Cari baris UPROGS= dan tambahkan:

make
Salin
Edit
UPROGS=\
  ...
  _ptest\
  _rtest\
✅ Langkah Akhir
bash
Salin
Edit
$ make clean
$ make
$ make qemu
Lalu di dalam QEMU shell:

bash
Salin
Edit
$ ptest
$ rtest
Kalau kamu butuh seluruh patch .diff atau ingin saya bundel jadi satu folder .zip, tinggal bilang. Mau lanjut modifikasi misalnya jadi priority-based scheduler? Bisa juga.
