# ✨ WIBUVPN LITE ✨

![Version](https://img.shields.io/badge/Version-9.2_Patched-blue.svg)
![Status](https://img.shields.io/badge/Status-Stable-brightgreen.svg)
![OS](https://img.shields.io/badge/OS-Ubuntu_20.04_|_22.04_|_Debian_10_|_11-orange.svg)
![Arch](https://img.shields.io/badge/Arch-x86__64-lightgrey.svg)

Script Auto-Install VPN multi-protokol untuk VPS Linux. Ringan, cepat, dan dilengkapi panel menu SSH interaktif dengan manajemen akun lengkap.

---

## 📋 Daftar Isi

- [Kebutuhan Sistem](#-kebutuhan-sistem)
- [Protokol & Layanan](#-protokol--layanan)
- [Fitur Utama](#-fitur-utama)
- [Cara Instalasi](#-cara-instalasi)
- [Menu & Penggunaan](#-menu--penggunaan)
- [Changelog v9.1](#-changelog-v91-patch)
- [Troubleshooting](#-troubleshooting)
- [Kontak](#-kontak)

---

## 💻 Kebutuhan Sistem

| Komponen   | Minimum                          |
| ---------- | -------------------------------- |
| **OS**     | Ubuntu 20.04 / 22.04, Debian 10 / 11 |
| **Arch**   | x86_64 (AMD64)                   |
| **RAM**    | 512 MB                           |
| **Akses**  | Root                             |
| **VPS**    | KVM / Xen (OpenVZ tidak didukung) |

---

## 🌐 Protokol & Layanan

| Protokol       | Keterangan                                   |
| -------------- | -------------------------------------------- |
| **SSH**        | OpenSSH, Dropbear, SSH WebSocket, SSH SSL    |
| **Vmess**      | Xray Core, multi-transport (ws, grpc)        |
| **Vless**      | Xray Core, multi-transport (ws, grpc)        |
| **Trojan**     | Xray Core, multi-transport                   |
| **Shadowsocks**| Xray Core                                    |
| **SlowDNS**    | DNS Tunneling                                |
| **NoobzVPN**   | Protokol kustom anti-blokir                  |
| **BadVPN**     | UDP Gateway (port 7100, 7200, 7300)          |
| **HAProxy**    | Multi-port load balancer                     |

---

## 🚀 Fitur Utama

- **Manajemen Akun Lengkap** — Create, Delete, Renew, Lock, Unlock, Detail, Trial, Change Quota, Change IP Limit untuk semua protokol.
- **Auto Expired** — Akun SSH otomatis terhapus saat expired, termasuk semua data pendukung (quota, IP limit, detail file).
- **IP Limiter** — Pembatasan jumlah IP login per akun dengan notifikasi Telegram dan auto-lock 5 menit.
- **Quota Limiter** — Pembatasan bandwidth per akun (dalam GB).
- **Trial Account** — Buat akun trial SSH dengan durasi menit, otomatis terhapus bersih.
- **Bot Telegram** — Notifikasi otomatis saat create/delete akun, IP lock, dan auto-backup.
- **Multi Bot Support** — Mendukung 5 jenis bot (Vermilion, CyberVPN, Private, Public, Give).
- **Auto Backup** — Backup otomatis ke Telegram.
- **SSL/TLS** — Sertifikat via ZeroSSL (anti limit Let's Encrypt).
- **Bandwidth Monitor** — Monitor penggunaan bandwidth via vnstat.
- **WARP** — Cloudflare WARP integration.
- **IPv4 Forced** — Semua deteksi IP menggunakan `ipv4.icanhazip.com`, aman walau IPv6 disabled.

---

## 🛠️ Cara Instalasi

### Langkah 1 — Update & Reboot

```bash
export DEBIAN_FRONTEND=noninteractive NEEDRESTART_MODE=a NEEDRESTART_SUSPEND=1 && \
apt update -y && apt upgrade -y && apt install -y wget curl screen dos2unix && reboot
```

> Tunggu 1-2 menit setelah VPS reboot, lalu login kembali.

### Langkah 2 — Install Script

```bash
sysctl -w net.ipv6.conf.all.disable_ipv6=1 && \
sysctl -w net.ipv6.conf.default.disable_ipv6=1 && \
wget -O install.sh https://raw.githubusercontent.com/WBVPN/WIBUVPN-LITE/main/install.sh && \
chmod +x install.sh && screen -S setup ./install.sh
```

### Reconnect (Jika Terputus)

```bash
screen -r -d setup
```

---

## 📖 Menu & Penggunaan

Setelah instalasi selesai dan VPS reboot, ketik `menu` untuk masuk ke panel utama.

```
┌─────────────────────────────────────────────────────┐
│                   WIBU TUNNELLING                   │
└─────────────────────────────────────────────────────┘
 [01] SSH              [11] Bot Telegram
 [02] Vmess            [12] Bot Notification
 [03] Vless            [13] Backup & Restore
 [04] Trojan           [14] Bandwidth
 [05] Shadowsocks      [15] Running
 [06] Noobzvpns        [16] Reboot
 [07] Slowdns          [17] Dropbear Version
 [08] Warp             [18] Change Banner SSH
 [09] Domain           [19] Update Script
 [10] Fix Off          [20] Others Settings
```

### Sub-Menu SSH

```
 [01] Create           [08] Lock
 [02] Trial            [09] Unlock
 [03] Renew            [10] List User Locked
 [04] Delete           [11] Detail Account
 [05] Check Login      [12] Change Limit IP
 [06] List Member      [13] Change Limit Quota
 [07] Delete Expired
```

---

## 📝 Changelog v9.2 (Patch)

### Critical Fixes
- **Xray Crash saat Delete Akun** — Semua fungsi delete akun (vmess, vless, trojan, shadowsocks) menggunakan pattern `sed` range `/^marker/,/^},{/d` yang gagal menghapus entry terakhir di `config.json`. Saat entry terakhir dihapus, sed menghapus sampai EOF → JSON rusak → Xray mati total. Diperbaiki ke `{N;d}` (hapus tepat 2 baris per entry).
- **Xray Crash saat Buat Akun Trial** — Sama, fungsi check login yang menghapus akun saat quota/IP limit tercapai juga pakai pattern yang sama. Sudah diperbaiki.
- **Nginx Crash karena IPv6** — `config/xray.conf` punya `listen [::]:80`, `listen [::]:443`, dll. Kalau IPv6 disabled di VPS, Nginx gagal start (`socket() [::]:80 failed`). Semua `listen [::]:` dihapus dari `xray.conf`.
- **Nginx Crash saat Install** — Installer tidak menghapus `/etc/nginx/sites-enabled/default` (config bawaan Nginx yang juga punya IPv6 listen). Ditambahkan `rm -f` setelah `apt install nginx`.
- **SlowDNS Error di Installer** — Blok `server-sldns.service` di `install.sh` terduplikasi — copy kedua bocor keluar heredoc dan dieksekusi sebagai command, menyebabkan error `[Unit]: command not found`, `[Service]: command not found`, dll. Duplikat dihapus.

### Bug Fixes (dari v9.1)
- **SSH Delete tidak bersih** — `Delete SSH` sekarang menghapus semua data terkait: entry `.ssh.db`, file quota (`/etc/ssh/$user`), file IP limit (`/etc/limit/ssh/ip/$user`), file detail (`/detail/ssh/$user.txt`), dan file HTML (`/var/www/html/ssh-$user.txt`).
- **Delete Expired SSH tidak bersih** — Cleanup yang sama diterapkan untuk `Delete Expired`.
- **Trial SSH auto-delete tidak bersih** — `at` job untuk trial sekarang juga membersihkan semua data pendukung.
- **Vless Renew rusak** — Fungsi renew vless menulis prefix trojan (`#!`) ke `config.json` dan `.vless.db`, menyebabkan akun vless yang di-renew tidak dikenali. Diperbaiki ke prefix yang benar (`#&`).
- **Fungsi duplikat** — `lock_ssh_account()`, `unlock_ssh_account()`, dan `list_ssh_users()` didefinisikan dua kali di dalam `m_ssh()`. Duplikat dihapus.
- **Variabel tidak terdefinisi** — `GREEN` dan `blue_text` dipakai di fungsi lock/unlock SSH tapi tidak ada dalam scope. Ditambahkan.

### Improvements
- **IPv4 Forced** — Semua pemanggilan `icanhazip.com` diganti ke `ipv4.icanhazip.com` di seluruh file (menu, install.sh, backup, cloudflare, autobotbkp). Mencegah kegagalan deteksi IP pada VPS yang IPv6-nya sudah disabled.

---

## ❓ Troubleshooting

### IPv6 Sudah Disabled, Install Gagal?
Script installer memanggil `sysctl -w net.ipv6.conf.all.disable_ipv6=1` di awal. Kalau IPv6 **sudah mati**, command ini tetap jalan tanpa error (set 1 → 1). Masalah sebenarnya biasanya karena `icanhazip.com` mencoba resolve AAAA record (IPv6) lalu timeout. Versi ini sudah diperbaiki — semua pakai `ipv4.icanhazip.com`.

### Menu Tidak Muncul / Command Not Found
```bash
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
menu
```

### Akun SSH Tidak Bisa Dihapus
Bug ini sudah diperbaiki di v9.1. Pada versi lama, `userdel` dijalankan tapi data di `.ssh.db`, file quota, dan file IP limit tidak ikut dihapus, menyebabkan akun "hantu" yang masih muncul di list.

### Permission Denied Saat Buka Menu
IP VPS harus terdaftar di file `ip` pada repository GitHub. Hubungi admin untuk pendaftaran IP.

---

## 📞 Kontak

Pendaftaran IP & support:
👉 **[t.me/wibuvpn](https://t.me/wibuvpn)**

---

## 📁 Struktur Direktori

```
WIBUVPN-LITE/
├── install.sh              # Installer utama
├── update.sh               # Updater script
├── ip                      # Daftar IP yang diizinkan (menu)
├── ip-bot                  # Daftar IP yang diizinkan (bot)
├── menu/menu/
│   ├── menu                # Menu utama (10000+ baris)
│   ├── m-backup            # Menu backup
│   ├── backup              # Backup script
│   ├── backup-bot          # Backup via bot
│   ├── restore             # Restore script
│   ├── update              # Update handler
│   └── ...                 # Sub-menu lainnya
├── config/
│   ├── config.json         # Template Xray config
│   ├── nginx.conf          # Nginx config
│   ├── haproxy → xray.conf # HAProxy / Xray configs
│   └── limiter.sh          # IP limiter daemon
├── files/
│   ├── lite-ssh             # SSH IP limiter
│   ├── lite-vm/vl/trj/shd  # Xray protocol limiters
│   ├── sshd                 # SSHD config
│   ├── ws-stunnel           # WebSocket stunnel
│   └── ...                  # Binary & config files
├── bot/                     # Bot Telegram archives
├── ping/                    # Cek service scripts
└── slowdns/                 # SlowDNS server files
```
