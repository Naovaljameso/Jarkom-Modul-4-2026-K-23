# Praktikum Komunikasi Data & Jaringan Komputer — Modul 4
## Kelompok K23 · Prefix IP `10.75.0.0`

| Nama | NRP | Bagian |
|---|---|---|
| Naoval James Osamah | 5027251092 | Cisco Packet Tracer — **VLSM** |
| Nur Rizki Syahbana | 5027251095 | GNS3 — **CIDR** |

Sesuai ketentuan soal, CPT dan GNS3 memakai metode classless yang berbeda: **VLSM di CPT** dan **CIDR di GNS3**.

---

## Daftar Isi
1. [Struktur Repository](#1-struktur-repository)
2. [Topologi](#2-topologi)
3. [Pembagian Subnet (Rute)](#3-pembagian-subnet-rute)
4. [Perhitungan VLSM (CPT)](#4-perhitungan-vlsm-cpt)
5. [Tree VLSM](#5-tree-vlsm)
6. [Konfigurasi Cisco Packet Tracer](#6-konfigurasi-cisco-packet-tracer)
7. [Routing Statis](#7-routing-statis)
8. [Pengujian (Ping) CPT](#8-pengujian-ping-cpt)
9. [Perhitungan & Konfigurasi CIDR (GNS3)](#9-perhitungan--konfigurasi-cidr-gns3)

---

## 1. Struktur Repository

```text
Jarkom-Modul-4-2026-K-23/
├── README.md
├── Jarkom-Modul-4-2026-K-23.xlsx        # Spreadsheet perhitungan (Rute, VLSM, CIDR)
├── images/
│   ├── topologi/                        # Topologi CPT (polos dan dengan penanda subnet)
│   ├── tree/                            # Tree VLSM dan Tree CIDR
│   └── ping/                            # Screenshot hasil ping (01–16) CPT & GNS3
├── modul 4 jarkom VLSM cisco/
│   └── Jarkom-Modul-4-2026-K-23.pkt     # File Cisco Packet Tracer
└── modul 4 jarkom CIDR GNS3/
    ├── CIDR K23.gns3project             # Project GNS3
    └── script.txt                       # Script konfigurasi node
```

---

## 2. Topologi

Topologi The Wired dibangun di Cisco Packet Tracer sesuai gambar soal. **Cyberia adalah satu-satunya router yang terhubung ke Cloud**, dan jumlah host tiap node tertulis pada nama perangkat (contoh: `JJ (95 host)`).

![Topologi CPT](images/topologi/Topologi-VLSM-K23-a.png)

Topologi yang sama dengan penanda setiap subnet (A1–A25) beserta prefix-nya:

![Topologi dengan subnet](images/topologi/Topologi-VLSM-K23-b.png)

**Perangkat:**
- 13 router Cisco 2811. Router yang butuh lebih dari 2 port (Cyberia, Wired, Navi, Copland, Schumann, Tachibana, Deus, Protocol7, Knights, Phantoma) ditambah modul **NM-2FE2W** (port Fa1/0 dan Fa1/1).
- 13 switch, 25 node host, 1 Cloud.
- Kabel **cross-over** untuk router–router dan router–node langsung (Juri, Akashic, Eiri); **straight-through** untuk sambungan lewat switch.

---

## 3. Pembagian Subnet (Rute)

Satu subnet adalah sekumpulan perangkat yang terhubung **tanpa melewati router**. Sesuai poin 8 soal:

> **Jumlah IP = jumlah host + jumlah interface router pada subnet tersebut**

Netmask dipilih dari jumlah bit host terkecil `n` yang memenuhi **2ⁿ − 2 ≥ jumlah IP**, lalu prefix = 32 − n.

| Subnet | Rute | Perhitungan | Jumlah IP | Netmask |
|---|---|---|---|---|
| A1 | Cyberia > Switch1 > JJ, MyuMyu | 95 + 30 + 1 | 126 | /25 |
| A2 | Cyberia > Wired | 2 router | 2 | /30 |
| A3 | Wired > Switch2 > Psyche, Copland, Schumann | 4 router | 4 | /29 |
| A4 | Psyche > Switch3 > Karl, Lin | 700 + 180 + 1 | 881 | /22 |
| A5 | Copland > Juri | 31 + 1 | 32 | /26 |
| A6 | Copland > Switch4 > Masayuki | 300 + 1 | 301 | /23 |
| A7 | Schumann > Akashic | 3 + 1 | 4 | /29 |
| A8 | Schumann > Accela | 2 router | 2 | /30 |
| A9 | Accela > Switch5 > Omnipresence, Denpa | 510 + 3 + 1 | 514 | /22 |
| A10 | Wired > Navi | 2 router | 2 | /30 |
| A11 | Navi > Switch6 > Alice, Reika | 208 + 45 + 1 | 254 | /24 |
| A12 | Navi > Switch7 > Taro | 57 + 1 | 58 | /26 |
| A13 | Navi > Switch8 > Yasuo, Miho, Kuma | 120 + 18 + 4 + 1 | 143 | /24 |
| A14 | Wired > Tachibana | 2 router | 2 | /30 |
| A15 | Tachibana > Switch9 > Collective, Ghost | 260 + 6 + 1 | 267 | /23 |
| A16 | Tachibana > Deus | 2 router | 2 | /30 |
| A17 | Deus > Switch10 > Lilith, Specter | 77 + 21 + 1 | 99 | /25 |
| A18 | Deus > Protocol7 | 2 router | 2 | /30 |
| A19 | Protocol7 > Switch11 > Chisa, Hodgeson | 23 + 9 + 1 | 33 | /26 |
| A20 | Protocol7 > Knights | 2 router | 2 | /30 |
| A21 | Knights > Eiri | 2 + 1 | 3 | /29 |
| A22 | Knights > Phantoma | 2 router | 2 | /30 |
| A23 | Phantoma > Switch12 > Semi | 14 + 1 | 15 | /27 |
| A24 | Phantoma > Shibuya | 2 router | 2 | /30 |
| A25 | Shibuya > Switch13 > Lain, Mika | 57 + 4 + 1 | 62 | /26 |
| **Total** | | | **2814** | **/19** |

**Kenapa total /19, bukan /20?** Yang dijumlahkan adalah ukuran blok tiap subnet, bukan jumlah IP-nya:

| Prefix | Ukuran blok | Banyak | Total |
|---|---|---|---|
| /22 | 1024 | 2 | 2048 |
| /23 | 512 | 2 | 1024 |
| /24 | 256 | 2 | 512 |
| /25 | 128 | 2 | 256 |
| /26 | 64 | 4 | 256 |
| /27 | 32 | 1 | 32 |
| /29 | 8 | 3 | 24 |
| /30 | 4 | 9 | 36 |
| | | **Jumlah** | **4188** |

Blok /20 hanya berisi 4096 alamat (< 4188), sehingga dibutuhkan **/19 (8192 alamat)**.

---

## 4. Perhitungan VLSM (CPT)

Metode mengikuti Jeremy's IT Lab (CCNA Day 15 — Subnetting Part 3, VLSM):

1. Urutkan subnet dari kebutuhan IP **terbesar ke terkecil**.
2. Cari jumlah bit host `n` dengan 2ⁿ − 2 ≥ jumlah IP → prefix = 32 − n.
3. **Network ID** subnet pertama = `10.75.0.0`; subnet berikutnya dimulai **tepat setelah broadcast** subnet sebelumnya.
4. **Broadcast** = semua bit host bernilai 1.
5. **Range IP** = network + 1 sampai broadcast − 1.

### Contoh perhitungan biner

**A4 (/22), n = 10** — bit host masuk ke oktet ke-3:
```text
Netmask   : 11111111.11111111.111111|00.00000000 = 255.255.252.0
Network   : 00001010.01001011.000000|00.00000000 = 10.75.0.0
Broadcast : 00001010.01001011.000000|11.11111111 = 10.75.3.255
```
Subnet berikutnya: 10.75.3.255 + 1 = **10.75.4.0**

**A1 (/25), n = 7:**
```text
Netmask   : 11111111.11111111.11111111.1|0000000 = 255.255.255.128
Network   : 00001010.01001011.00001110.0|0000000 = 10.75.14.0
Broadcast : 00001010.01001011.00001110.0|1111111 = 10.75.14.127
```

### Hasil pembagian IP VLSM

| Subnet | Jumlah IP | Prefix | Network ID | Netmask | Broadcast | Range IP |
|---|---|---|---|---|---|---|
| A4 | 881 | /22 | 10.75.0.0 | 255.255.252.0 | 10.75.3.255 | 10.75.0.1 - 10.75.3.254 |
| A9 | 514 | /22 | 10.75.4.0 | 255.255.252.0 | 10.75.7.255 | 10.75.4.1 - 10.75.7.254 |
| A6 | 301 | /23 | 10.75.8.0 | 255.255.254.0 | 10.75.9.255 | 10.75.8.1 - 10.75.9.254 |
| A15 | 267 | /23 | 10.75.10.0 | 255.255.254.0 | 10.75.11.255 | 10.75.10.1 - 10.75.11.254 |
| A11 | 254 | /24 | 10.75.12.0 | 255.255.255.0 | 10.75.12.255 | 10.75.12.1 - 10.75.12.254 |
| A13 | 143 | /24 | 10.75.13.0 | 255.255.255.0 | 10.75.13.255 | 10.75.13.1 - 10.75.13.254 |
| A1 | 126 | /25 | 10.75.14.0 | 255.255.255.128 | 10.75.14.127 | 10.75.14.1 - 10.75.14.126 |
| A17 | 99 | /25 | 10.75.14.128 | 255.255.255.128 | 10.75.14.255 | 10.75.14.129 - 10.75.14.254 |
| A25 | 62 | /26 | 10.75.15.0 | 255.255.255.192 | 10.75.15.63 | 10.75.15.1 - 10.75.15.62 |
| A12 | 58 | /26 | 10.75.15.64 | 255.255.255.192 | 10.75.15.127 | 10.75.15.65 - 10.75.15.126 |
| A19 | 33 | /26 | 10.75.15.128 | 255.255.255.192 | 10.75.15.191 | 10.75.15.129 - 10.75.15.190 |
| A5 | 32 | /26 | 10.75.15.192 | 255.255.255.192 | 10.75.15.255 | 10.75.15.193 - 10.75.15.254 |
| A23 | 15 | /27 | 10.75.16.0 | 255.255.255.224 | 10.75.16.31 | 10.75.16.1 - 10.75.16.30 |
| A3 | 4 | /29 | 10.75.16.32 | 255.255.255.248 | 10.75.16.39 | 10.75.16.33 - 10.75.16.38 |
| A7 | 4 | /29 | 10.75.16.40 | 255.255.255.248 | 10.75.16.47 | 10.75.16.41 - 10.75.16.46 |
| A21 | 3 | /29 | 10.75.16.48 | 255.255.255.248 | 10.75.16.55 | 10.75.16.49 - 10.75.16.54 |
| A2 | 2 | /30 | 10.75.16.56 | 255.255.255.252 | 10.75.16.59 | 10.75.16.57 - 10.75.16.58 |
| A8 | 2 | /30 | 10.75.16.60 | 255.255.255.252 | 10.75.16.63 | 10.75.16.61 - 10.75.16.62 |
| A10 | 2 | /30 | 10.75.16.64 | 255.255.255.252 | 10.75.16.67 | 10.75.16.65 - 10.75.16.66 |
| A14 | 2 | /30 | 10.75.16.68 | 255.255.255.252 | 10.75.16.71 | 10.75.16.69 - 10.75.16.70 |
| A16 | 2 | /30 | 10.75.16.72 | 255.255.255.252 | 10.75.16.75 | 10.75.16.73 - 10.75.16.74 |
| A18 | 2 | /30 | 10.75.16.76 | 255.255.255.252 | 10.75.16.79 | 10.75.16.77 - 10.75.16.78 |
| A20 | 2 | /30 | 10.75.16.80 | 255.255.255.252 | 10.75.16.83 | 10.75.16.81 - 10.75.16.82 |
| A22 | 2 | /30 | 10.75.16.84 | 255.255.255.252 | 10.75.16.87 | 10.75.16.85 - 10.75.16.86 |
| A24 | 2 | /30 | 10.75.16.88 | 255.255.255.252 | 10.75.16.91 | 10.75.16.89 - 10.75.16.90 |

---

## 5. Tree VLSM

Blok `10.75.0.0/19` dibelah dua terus-menerus sampai setiap subnet mendapat tempat.
- **Hitam**: blok perantara (dibelah lagi)
- **Merah**: subnet yang dipakai (A1–A25)
- **Biru**: blok sisa (tidak dipakai)

![Tree VLSM](images/tree/Tree-VLSM-K23.png)

---

## 6. Konfigurasi Cisco Packet Tracer

### Aturan pemberian IP
- Interface router pada subnet LAN memakai **first usable**, dan menjadi **gateway** node di subnet itu.
- Link router–router (/30): router yang lebih dekat ke Cyberia memakai first usable.
- A3 (Switch2, 4 router): Wired `.33`, Psyche `.34`, Copland `.35`, Schumann `.36`.
- Port `Fa0/0` Cyberia (ke Cloud) tidak diberi IP.

### Pemetaan port router

| Router | Fa0/0 | Fa0/1 | Fa1/0 | Fa1/1 |
|---|---|---|---|---|
| Cyberia | Cloud | Switch1 | Wired | — |
| Wired | Cyberia | Switch2 | Navi | Tachibana |
| Psyche | Switch2 | Switch3 | — | — |
| Copland | Switch2 | Juri | Switch4 | — |
| Schumann | Switch2 | Akashic | Accela | — |
| Accela | Schumann | Switch5 | — | — |
| Navi | Wired | Switch6 | Switch7 | Switch8 |
| Tachibana | Wired | Switch9 | Deus | — |
| Deus | Tachibana | Switch10 | Protocol7 | — |
| Protocol7 | Deus | Switch11 | Knights | — |
| Knights | Protocol7 | Eiri | Phantoma | — |
| Phantoma | Knights | Switch12 | Shibuya | — |
| Shibuya | Phantoma | Switch13 | — | — |

---

## 7. Routing Statis

Topologi berbentuk pohon dengan Cyberia di puncak, sehingga routing dibuat seefisien mungkin dengan dua aturan:

1. **Default route (`0.0.0.0/0`) ke arah atas** (menuju Cyberia) pada setiap router selain Cyberia.
2. **Route spesifik ke arah bawah**, hanya untuk subnet di bawah router tersebut yang tidak terhubung langsung.

Cyberia cukup memiliki **satu route ringkasan** `10.75.0.0/19` ke Wired; subnet A1 dan A2 tetap dipakai langsung karena prefix-nya lebih spesifik. Router ujung (Psyche, Copland, Accela, Navi, Shibuya) cukup memiliki 1 default route.

*(Konfigurasi lengkap routing statis CPT dapat dilihat pada file Packet Tracer terlampir)*

---

## 8. Pengujian (Ping) CPT

Pengujian dilakukan dari **JJ** (subnet A1, paling atas) dan **Lain** (subnet A25, paling bawah) ke **seluruh 24 node lain**. Setiap balasan ping juga membuktikan jalur pulangnya, sehingga seluruh route di 13 router teruji.

**Hasil: 48/48 tes — semua 4/4 Reply, 0% loss.**

**01 JJ-ke-Karl-Lin-Juri**
![01-JJ-ke-Karl-Lin-Juri](images/ping/01-JJ-ke-Karl-Lin-Juri.jpg)

**02 JJ-ke-Masayuki-Akashic-Omnipresence**
![02-JJ-ke-Masayuki-Akashic-Omnipresence](images/ping/02-JJ-ke-Masayuki-Akashic-Omnipresence.jpg)

*(Sisa screenshot CPT tersimpan di folder images/ping/)*

---

## 9. Perhitungan & Konfigurasi CIDR (GNS3)

Pada GNS3, desain IP menggunakan pendekatan **CIDR (Classless Inter-Domain Routing)** yang menggabungkan subnet ke dalam blok Supernet. Selain itu, pengerjaan diwajibkan menggunakan sistem shell script pada Docker Container.

### 9.1 Pembagian IP (CIDR)
Penggabungan subnet dan pembagian IP lengkapnya dapat dilihat secara rinci pada sheet **Penggabungan - CIDR** dan **Pembagian IP - CIDR** di file `Jarkom-Modul-4-2026-K-23.xlsx`.

Berikut adalah hirarki penggabungan supernet utamanya:
- **C1 (Supernet Keseluruhan)**: `10.75.0.0/17`
  - **B1 (Cabang Kiri - Jalur Psyche dst)**: `10.75.32.0/19`
  - **B2 (Cabang Kanan - Jalur Navi)**: `10.75.64.0/22`
  - **B3 (Cabang Bawah - Jalur Tachibana dst)**: `10.75.80.0/21`

### 9.2 Tree CIDR
Visualisasi pemecahan subnet dari root supernet (C1) hingga host dapat dilihat pada diagram pohon berikut:

![Tree CIDR](images/tree/Tree-CIDR-K23.png)

### 9.3 Konfigurasi Perangkat GNS3
Sesuai aturan Modul 4, seluruh host (PC) digantikan menggunakan Docker Container berbasis Alpine Linux ringan yaitu `ardhptr21/alpinet:latest`. 

Seluruh pengaturan IP, Routing Statis, dan Forwarding tidak diset melalui GUI, melainkan ditulis menggunakan script bash `cat << 'EOF' > /root/setup.sh` di dalam terminal konsol perangkat.

**Contoh Script Gateway Utama & NAT Cloud (Router Cyberia):**
```bash
cat << 'EOF' > /root/setup.sh
#!/bin/bash
ip link set dev eth0 up
ip link set dev eth1 up
ip link set dev eth2 up

# IP Static & Default Route NAT GNS3
ip addr add 192.168.122.100/24 dev eth0 2>/dev/null || true
ip route add default via 192.168.122.1 dev eth0 2>/dev/null || true

# IP Internal Cyberia
ip addr add 10.75.0.1/25 dev eth1 2>/dev/null || true
ip addr add 10.75.0.129/30 dev eth2 2>/dev/null || true

# Forwarding & NAT Masquerade
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE 2>/dev/null || true

# Route Supernet C1 ke seluruh internal The Wired
ip route add 10.75.0.0/17 via 10.75.0.130 2>/dev/null || true
EOF
bash /root/setup.sh
```

**Contoh Script Ujung Host Docker (Host Lain):**
```bash
cat << 'EOF' > /root/setup.sh
#!/bin/bash
ip addr add 10.75.86.2/26 dev eth0 2>/dev/null || true
ip link set dev eth0 up
ip route add default via 10.75.86.1 2>/dev/null || true
EOF
bash /root/setup.sh
```

*(Kumpulan lengkap script setiap node di GNS3 disertakan dalam file `script.txt` di dalam folder CIDR GNS3).*

### 9.4 Pengujian GNS3
Pengujian di terminal node **Lain** (titik ujung terdalam jaringan). Keduanya menunjukkan balasan sukses (*0% packet loss*), menandakan konfigurasi routing Supernet/Subnet CIDR dan proses NAT Gateway berjalan sempurna.

**1. Ping Lintas Subnet (Node Lain ke Node JJ)**
```text
Lain:~# ping 10.75.0.2
PING 10.75.0.2 (10.75.0.2) 56(84) bytes of data.
64 bytes from 10.75.0.2: icmp_seq=1 ttl=56 time=3.53 ms
64 bytes from 10.75.0.2: icmp_seq=2 ttl=56 time=1.88 ms
...
--- 10.75.0.2 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5008ms
```

**2. Ping ke Internet (Node Lain via NAT Cloud)**
```text
Lain:~# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=104 time=24.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=104 time=24.5 ms
...
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
```
