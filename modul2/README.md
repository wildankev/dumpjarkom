# Dokumentasi Subnetting, Routing, & Internet (Nomor 1–3)

## Nomor 1. Dokumentasi Pengelompokan Subnet

Prefix IP: **10.78**  
Netmask: **/24 (255.255.255.0)** untuk semua subnet sesuai aturan.

### A. Subnet LAN
| Switch | Subnet /24   | Gateway (Router) | Host Anggota |
|--------|--------------|------------------|--------------|
| Switch1 | 10.78.3.0/24 | BlackPanther:eth1 = 10.78.3.1 | CaptainAmerica (10.78.3.10), Falcon (10.78.3.20–25 DHCP) |
| Switch2 | 10.78.4.0/24 | BlackPanther:eth2 = 10.78.4.1 | WinterSoldier (10.78.4.10), Hawkeye (10.78.4.30–35 DHCP) |
| Switch3 | 10.78.5.0/24 | BlackWidow:eth2 = 10.78.5.1  | Thor (10.78.5.100–105 DHCP), ScarletWitch (10.78.5.40–45 DHCP) |
| Switch4 | 10.78.6.0/24 | Vision:eth0 = 10.78.6.1       | Hulk (10.78.6.50–55 DHCP) |
| Switch5 | 10.78.7.0/24 | Vision:eth1 = 10.78.7.1       | SpiderMan (10.78.7.60–65 DHCP), DoctorStrange (10.78.7.110–115 DHCP) |

### B. Subnet Antar-Router
| Link | Subnet /24   | IP Sisi A | IP Sisi B |
|------|--------------|-----------|-----------|
| BlackPanther ↔ IronMan | 10.78.100.0/24 | BlackPanther:10.78.100.2 | IronMan:10.78.100.1 |
| IronMan ↔ BlackWidow   | 10.78.101.0/24 | IronMan:10.78.101.1 | BlackWidow:10.78.101.2 |

### C. WAN
| Node    | Interface | Konfigurasi |
|---------|-----------|-------------|
| IronMan | eth0      | DHCP dari NAT |

---

## Nomor 2. Routing Antar Node

Tujuan: agar semua node dapat saling berkomunikasi.  
Metode: Konfigurasi routing pada router IronMan, BlackPanther, BlackWidow, dan Vision.  
Bukti: Falcon dapat ping ke SpiderMan, DoctorStrange, dan ScarletWitch.

### Konfigurasi Router

**IronMan**
```ini
auto eth1
iface eth1 inet static
    address 10.78.100.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.78.101.1
    netmask 255.255.255.0

post-up sysctl -w net.ipv4.ip_forward=1
post-up ip route add 10.78.3.0/24 via 10.78.100.2
post-up ip route add 10.78.4.0/24 via 10.78.100.2
post-up ip route add 10.78.5.0/24 via 10.78.101.2
post-up ip route add 10.78.6.0/24 via 10.78.101.2
post-up ip route add 10.78.7.0/24 via 10.78.101.2
```

**BlackPanther**
```ini
auto eth0
iface eth0 inet static
    address 10.78.100.2
    netmask 255.255.255.0

auto eth1
iface eth1 inet static
    address 10.78.3.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.78.4.1
    netmask 255.255.255.0

post-up sysctl -w net.ipv4.ip_forward=1
post-up ip route add default via 10.78.100.1
```

**BlackWidow**
```ini
auto eth0
iface eth0 inet static
    address 10.78.101.2
    netmask 255.255.255.0

auto eth1
iface eth1 inet static
    address 10.78.6.2
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.78.5.1
    netmask 255.255.255.0

post-up sysctl -w net.ipv4.ip_forward=1
post-up ip route add default via 10.78.101.1
post-up ip route add 10.78.7.0/24 via 10.78.6.1
```

**Vision**
```ini
auto eth0
iface eth0 inet static
    address 10.78.6.1
    netmask 255.255.255.0

auto eth1
iface eth1 inet static
    address 10.78.7.1
    netmask 255.255.255.0

post-up sysctl -w net.ipv4.ip_forward=1
post-up ip route add default via 10.78.6.2
```

### Konfigurasi Host (sementara statis untuk uji)

**Falcon**
```ini
auto eth0
iface eth0 inet static
    address 10.78.3.20
    netmask 255.255.255.0
    gateway 10.78.3.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**CaptainAmerica**
```ini
auto eth0
iface eth0 inet static
    address 10.78.3.10
    netmask 255.255.255.0
    gateway 10.78.3.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**Hawkeye**
```ini
auto eth0
iface eth0 inet static
    address 10.78.4.30
    netmask 255.255.255.0
    gateway 10.78.4.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**WinterSoldier**
```ini
auto eth0
iface eth0 inet static
    address 10.78.4.10
    netmask 255.255.255.0
    gateway 10.78.4.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**ScarletWitch**
```ini
auto eth0
iface eth0 inet static
    address 10.78.5.40
    netmask 255.255.255.0
    gateway 10.78.5.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**Thor**
```ini
auto eth0
iface eth0 inet static
    address 10.78.5.100
    netmask 255.255.255.0
    gateway 10.78.5.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**Hulk**
```ini
auto eth0
iface eth0 inet static
    address 10.78.6.50
    netmask 255.255.255.0
    gateway 10.78.6.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**SpiderMan**
```ini
auto eth0
iface eth0 inet static
    address 10.78.7.60
    netmask 255.255.255.0
    gateway 10.78.7.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**DoctorStrange**
```ini
auto eth0
iface eth0 inet static
    address 10.78.7.110
    netmask 255.255.255.0
    gateway 10.78.7.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

### Verifikasi Routing
- Dari Falcon → ping SpiderMan, DoctorStrange, ScarletWitch → **sukses**.  
- Dari CaptainAmerica → ping Hulk, Thor → **sukses**.  
- Dari SpiderMan → ping Falcon, WinterSoldier → **sukses**.

---

## Nomor 3. Koneksi Internet

Tujuan: agar node dapat mengakses internet melalui NAT di IronMan.

### Konfigurasi NAT di IronMan
```ini
post-up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
pre-down iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

### DNS Resolver (sementara)
Tambahkan di host client:
```ini
dns-nameservers 8.8.8.8 1.1.1.1
```
atau langsung edit `/etc/resolv.conf`.

### Verifikasi Internet
- Dari Falcon:
```bash
ping -c 4 8.8.8.8
ping -c 4 google.com
```
- Dari CaptainAmerica:
```bash
ping -c 4 google.com
```
- Dari SpiderMan:
```bash
ping -c 4 google.com
```
- Dari Thor:
```bash
ping -c 4 google.com
```

Semua berhasil setelah DNS diset ✅

## Pendahuluan
Pada soal nomor 4 sampai 6, dilakukan konfigurasi DHCP server, DHCP relay, dan pengaturan IP dinamis untuk beberapa node klien (Falcon, Hawkeye, Hulk, ScarletWitch, Thor, SpiderMan, dan DoctorStrange). Tujuannya adalah membagikan alamat IP otomatis sesuai dengan rentang yang ditentukan, memastikan konektivitas antar jaringan, serta menguji konfigurasi melalui verifikasi konektivitas.

---

## Nomor 4 — Konfigurasi DHCP Falcon, Hawkeye, dan Hulk

### DHCP Server (CaptainAmerica)
File: `/etc/dhcp/dhcpd.conf`
```conf
# Subnet LAN3 - Falcon
subnet 10.78.3.0 netmask 255.255.255.0 {
  option routers 10.78.3.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  pool {
    range 10.78.3.20 10.78.3.25;
  }
}

# Subnet LAN4 - Hawkeye
subnet 10.78.4.0 netmask 255.255.255.0 {
  option routers 10.78.4.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  pool {
    range 10.78.4.30 10.78.4.35;
  }
}

# Subnet LAN6 - Hulk
subnet 10.78.6.0 netmask 255.255.255.0 {
  option routers 10.78.6.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  pool {
    range 10.78.6.50 10.78.6.55;
  }
}
```

### BlackPanther (Relay untuk LAN4)
```conf
SERVERS="10.78.3.10"
INTERFACES="eth2 eth1"
OPTIONS=""
```

### Vision (Relay untuk LAN6)
```conf
SERVERS="10.78.3.10"
INTERFACES="eth0"
OPTIONS=""
```

### Falcon (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname falcon
```

### Hawkeye (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname hawkeye
```

### Hulk (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname hulk
```

---

## Nomor 5 — Konfigurasi DHCP ScarletWitch dan Thor

### DHCP Server (CaptainAmerica)
```conf
subnet 10.78.5.0 netmask 255.255.255.0 {
  option routers 10.78.5.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  class "scarletwitch" { match if option host-name = "scarletwitch"; }
  pool {
    allow members of "scarletwitch";
    range 10.78.5.40 10.78.5.45;
  }

  class "thor" { match if option host-name = "thor"; }
  pool {
    allow members of "thor";
    range 10.78.5.100 10.78.5.105;
  }
}
```

### BlackWidow (Relay untuk LAN5)
```conf
SERVERS="10.78.3.10"
INTERFACES="eth2 eth0"
OPTIONS=""
```

### ScarletWitch (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname scarletwitch
```

### Thor (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname thor
```

---

## Nomor 6 — Konfigurasi DHCP SpiderMan dan DoctorStrange

### DHCP Server (CaptainAmerica)
```conf
subnet 10.78.7.0 netmask 255.255.255.0 {
  option routers 10.78.7.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  class "spiderman" { match if option host-name = "spiderman"; }
  pool {
    allow members of "spiderman";
    range 10.78.7.60 10.78.7.65;
  }

  class "doctorstrange" { match if option host-name = "doctorstrange"; }
  pool {
    allow members of "doctorstrange";
    range 10.78.7.110 10.78.7.115;
  }
}
```

### Vision (Relay untuk LAN7)
```conf
SERVERS="10.78.3.10"
INTERFACES="eth0 eth1"
OPTIONS=""
```

### SpiderMan (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname spiderman
```

### DoctorStrange (Client)
```ini
auto eth0
iface eth0 inet dhcp
hostname doctorstrange
```

---

## Verifikasi
- Cek IP pada klien dengan `ip -br addr` → harus jatuh sesuai rentang.  
- Uji ping gateway (`ping -c 2 <gateway>`) → harus reply.  
- Uji ping internet (`ping -c 2 8.8.8.8` dan `ping -c 2 google.com`).  
- Cek leases di server: `tail -n 50 /var/lib/dhcp/dhcpd.leases`.  

Dengan ini, konfigurasi DHCP nomor 4–6 sudah selesai dan dapat digunakan untuk mendistribusikan IP sesuai soal.

## Nomor 7. Pengaturan Waktu Peminjaman IP (Lease Time)

### 🎯 Tujuan
Mengatur lease time per subnet sesuai ketentuan soal:
- Switch2 (LAN4) → Default 5 menit, Maksimal 2 jam
- Switch5 (LAN7) → Default 10 menit, Maksimal 2 jam
- Switch1 (LAN3) & Switch3 (LAN5) → Default 2 menit, Maksimal 100 menit

### 🛠️ Strategi
- Lease time diatur pada DHCP server (CaptainAmerica) melalui parameter:
  - `default-lease-time`
  - `max-lease-time`
- Konversi menit → detik:
  - 2 menit = 120, 5 menit = 300, 10 menit = 600, 100 menit = 6000, 2 jam = 7200

### 📂 Konfigurasi DHCP Server (CaptainAmerica)
```conf
# LAN3 (Switch1)
subnet 10.78.3.0 netmask 255.255.255.0 {
  option routers 10.78.3.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  default-lease-time 120;
  max-lease-time 6000;

  pool { range 10.78.3.20 10.78.3.25; }
}

# LAN4 (Switch2)
subnet 10.78.4.0 netmask 255.255.255.0 {
  option routers 10.78.4.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  default-lease-time 300;
  max-lease-time 7200;

  pool { range 10.78.4.30 10.78.4.35; }
}

# LAN5 (Switch3)
subnet 10.78.5.0 netmask 255.255.255.0 {
  option routers 10.78.5.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  default-lease-time 120;
  max-lease-time 6000;

  class "scarletwitch" { match if option host-name = "scarletwitch"; }
  pool { allow members of "scarletwitch"; range 10.78.5.40 10.78.5.45; }

  class "thor" { match if option host-name = "thor"; }
  pool { allow members of "thor"; range 10.78.5.100 10.78.5.105; }
}

# LAN7 (Switch5)
subnet 10.78.7.0 netmask 255.255.255.0 {
  option routers 10.78.7.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;

  default-lease-time 600;
  max-lease-time 7200;

  class "spiderman" { match if option host-name = "spiderman"; }
  pool { allow members of "spiderman"; range 10.78.7.60 10.78.7.65; }

  class "doctorstrange" { match if option host-name = "doctorstrange"; }
  pool { allow members of "doctorstrange"; range 10.78.7.110 10.78.7.115; }
}
```

### 📂 Konfigurasi Node Klien
Semua klien tetap DHCP:
```ini
auto eth0
iface eth0 inet dhcp
hostname <nama-node>
```

- Falcon → DHCP lease 120 detik (2 menit)
- Hawkeye → DHCP lease 300 detik (5 menit)
- ScarletWitch & Thor → DHCP lease 120 detik (2 menit)
- SpiderMan & DoctorStrange → DHCP lease 600 detik (10 menit)

### ✅ Verifikasi
- Gunakan `udhcpc -i eth0 -n -q -R -x hostname:<node>` untuk renew IP
- Cek lease time muncul pada log `udhcpc`
- Cek server: `tail -n 20 /var/lib/dhcp/dhcpd.leases`

---

## Nomor 8. IP Statis via DHCP Reservation

### 🎯 Tujuan
Mengatur agar:
- Hawkeye → 10.78.4.5
- Thor → 10.78.5.5
- SpiderMan → 10.78.7.5

### 🛠️ Strategi
- Gunakan **DHCP reservation** berdasarkan **MAC address**.  
- Setiap host diberi `fixed-address`.  
- Node tetap DHCP mode, tapi selalu mendapat IP statis.

### 📂 Konfigurasi DHCP Server (CaptainAmerica)
Tambahkan host declaration di `/etc/dhcp/dhcpd.conf`:

```conf
# Reservation untuk Hawkeye
host hawkeye {
  hardware ethernet <MAC_Hawkeye>;
  fixed-address 10.78.4.5;
}

# Reservation untuk Thor
host thor {
  hardware ethernet <MAC_Thor>;
  fixed-address 10.78.5.5;
}

# Reservation untuk SpiderMan
host spiderman {
  hardware ethernet <MAC_Spiderman>;
  fixed-address 10.78.7.5;
}
```

> Ganti `<MAC_Node>` dengan MAC address asli (`ip link show eth0` pada klien).

### 📂 Konfigurasi Node Klien
Semua klien tetap DHCP:
```ini
auto eth0
iface eth0 inet dhcp
hostname <nama-node>
```

- Hawkeye → akan selalu mendapat 10.78.4.5
- Thor → akan selalu mendapat 10.78.5.5
- SpiderMan → akan selalu mendapat 10.78.7.5

### ✅ Verifikasi
1. Restart DHCP server:
   ```bash
   service isc-dhcp-server restart
   ```
2. Renew di klien:
   ```bash
   udhcpc -i eth0 -n -q -R -x hostname:hawkeye
   ip -br addr
   ```
   (ulang untuk Thor & SpiderMan)
3. Cek di server:
   ```bash
   tail -n 20 /var/lib/dhcp/dhcpd.leases
   ```
   Harus muncul lease `.5` sesuai node.

---

# Kesimpulan
- Nomor 7: berhasil mengatur lease time berbeda-beda sesuai subnet.  
- Nomor 8: berhasil membuat reservation agar Hawkeye, Thor, dan SpiderMan selalu mendapat IP statis `.5`.  

## Nomor 9. DHCP Failover dengan WinterSoldier sebagai Backup

### 🎯 Tujuan
Mengimplementasikan **DHCP Failover** dengan:
- **CaptainAmerica** → DHCP Primary.
- **WinterSoldier** → DHCP Secondary (backup).

Sehingga layanan DHCP tetap berjalan meskipun salah satu server mati.

### 🧠 Konsep
- ISC DHCP mendukung **failover protocol** pada port TCP 647.
- **Primary** bertanggung jawab utama, **Secondary** mengambil alih saat primary mati.
- Lease database disinkronisasi antar kedua server.

### 🛠️ Konfigurasi

#### CaptainAmerica (Primary)
File: `/etc/dhcp/dhcpd.conf`
```conf
ddns-update-style none;
authoritative;

failover peer "dhcp-failover" {
  primary;
  address 10.78.3.10;
  port 647;
  peer address 10.78.3.20;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  mclt 3600;
  split 128;
  load balance max seconds 3;
}

# Reservation (contoh)
host hawkeye {
  hardware ethernet aa:bb:cc:dd:ee:01;
  fixed-address 10.78.4.5;
}
host thor {
  hardware ethernet aa:bb:cc:dd:ee:02;
  fixed-address 10.78.5.5;
}
host spiderman {
  hardware ethernet aa:bb:cc:dd:ee:03;
  fixed-address 10.78.7.5;
}

# Subnet LAN3 (contoh)
subnet 10.78.3.0 netmask 255.255.255.0 {
  option routers 10.78.3.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
  default-lease-time 120;
  max-lease-time 6000;

  pool {
    failover peer "dhcp-failover";
    range 10.78.3.20 10.78.3.25;
  }
}

# Subnet lainnya (LAN4–LAN7) identik dengan soal nomor 7–8 (masing-masing pakai pool failover).
```

#### WinterSoldier (Secondary)
File: `/etc/dhcp/dhcpd.conf`
```conf
ddns-update-style none;
authoritative;

failover peer "dhcp-failover" {
  secondary;
  address 10.78.3.20;
  port 647;
  peer address 10.78.3.10;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  load balance max seconds 3;
}

# Reservation dan subnet identik dengan CaptainAmerica
```

#### Router (DHCP Relay)
`/etc/default/isc-dhcp-relay`
```conf
SERVERS="10.78.3.10 10.78.3.20"
INTERFACES="eth0 eth1 eth2"
OPTIONS=""
```

Restart:
```bash
service isc-dhcp-relay restart
```

### ✅ Verifikasi
1. Cek log failover:
   ```bash
   tail -n 50 /var/log/syslog | grep -i failover
   ```
   Harus ada: `Failover association dhcp-failover now in normal state`.

2. Uji failover:
   - Stop DHCP di CaptainAmerica → `service isc-dhcp-server stop`
   - Renew klien (misalnya Falcon) → tetap dapat IP dari WinterSoldier.

3. Start lagi DHCP CaptainAmerica → state kembali normal.

---

## Nomor 10. DHCP Load Balancing

### 🎯 Tujuan
Mengaktifkan **Load Balancing** DHCP, di mana CaptainAmerica dan WinterSoldier berbagi beban 50:50 saat melayani klien.

### 🧠 Konsep
- Parameter `split 128` = 50:50 (primary:secondary).
- `load balance max seconds 3` = jika partner telat, server lain ikut merespons.

### 🛠️ Konfigurasi
Konfigurasi di **CaptainAmerica** dan **WinterSoldier** identik dengan nomor 9, dengan parameter failover seperti di atas (`split 128` dan `load balance max seconds 3`).

### ✅ Verifikasi
1. **Normal State**  
   Kedua server harus menampilkan:
   ```
   Failover association dhcp-failover now in normal state
   ```

2. **Tes dari banyak klien**  
   Jalankan DHCP renew di beberapa node (Falcon, Hawkeye, Thor, SpiderMan, dll).  
   Sebagian permintaan dijawab CaptainAmerica, sebagian WinterSoldier.

3. **Pantau OFFER** dengan `tcpdump`:
   ```bash
   tcpdump -ni eth0 port 67 or port 68 -vv
   ```
   Harus terlihat OFFER berasal dari dua server berbeda.

4. **Lease database sinkron**  
   Cek `/var/lib/dhcp/dhcpd.leases` → berisi klien dari kedua server.

---

## 📌 Kesimpulan
- **Nomor 9**: Konfigurasi failover berhasil bila kedua server saling sinkron dan klien tetap mendapat IP meskipun satu server mati.  
- **Nomor 10**: Dengan `split 128`, klien dilayani secara bergantian oleh CaptainAmerica dan WinterSoldier (load balancing).  
- Kombinasi ini memberikan **redundansi + distribusi beban** pada sistem DHCP.
