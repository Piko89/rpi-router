# 🍓 Raspberry Pi Router Kontrol Scripti

Bu script, Raspberry Pi'nizi kolayca bir router/access point'e dönüştürmenizi sağlar. Wifi üzerinden aldığı interneti Ethernet üzerinden paylaşabilir veya tam tersi yönde çalışabilir.

## 🚀 Özellikler

- **Statik IP Atama**: eth0 arayüzüne otomatik olarak 192.168.1.2/24 IP adresi atar
- **DHCP Sunucusu**: 192.168.1.10-100 aralığında IP dağıtır
- **NAT Yönlendirme**: İnternet bağlantısını paylaşır (wlan0 → eth0)
- **Esnek Kontrol**: Her özelliği ayrı ayrı açıp kapatabilirsiniz
- **Durum Takibi**: Tüm ayarları tek komutla kontrol edebilirsiniz

## 📋 Gereksinimler

- Raspberry Pi (herhangi bir model)
- Raspberry Pi OS (Raspbian)
- Root yetkisi (sudo)
- dnsmasq paketi yüklü olmalı

```bash
sudo apt update
sudo apt install dnsmasq
```

## 📁 Kurulum

1. Script dosyasını indirin:
```bash
git clone https://github.com/Piko89/rpi-router
```

2. Çalıştırma yetkisi verin:
```bash
cd rpi-router
chmod +x rpi-router.sh
```

## 🎯 Kullanım

### 🟢 Temel Komutlar

| Komut | Açıklama |
|-------|----------|
| `sudo ./rpi-router.sh start` | Router modunu başlat (tüm özellikler aktif) |
| `sudo ./rpi-router.sh stop` | Router modunu durdur |
| `sudo ./rpi-router.sh status` | Durum bilgilerini göster |

### ⚙️ Detaylı Kontrol Komutları

#### IP Adresi Yönetimi
- `sudo ./rpi-router.sh ip-static` → eth0'a statik IP (192.168.1.2/24) ata
- `sudo ./rpi-router.sh ip-dhcp` → eth0'u DHCP client moduna geçir

#### DHCP Sunucusu
- `sudo ./rpi-router.sh dhcp-on` → DHCP sunucusunu aç
- `sudo ./rpi-router.sh dhcp-off` → DHCP sunucusunu kapat

#### NAT Yönlendirme
- `sudo ./rpi-router.sh nat-on` → NAT yönlendirmeyi aç
- `sudo ./rpi-router.sh nat-off` → NAT yönlendirmeyi kapat

## 💡 Kullanım Senaryoları

### Senaryo 1: Wi-Fi'yi Ethernet Üzerinden Paylaşma
```bash
# Router modunu başlat
sudo ./rpi-router.sh start

# Ethernet kablosunu bilgisayarınıza bağlayın
# Bilgisayarınız otomatik olarak 192.168.1.x IP alacak
```

### Senaryo 2: Sadece DHCP Sunucusu Olarak Kullanma
```bash
# Statik IP ata
sudo ./rpi-router.sh ip-static

# Sadece DHCP'yi aç
sudo ./rpi-router.sh dhcp-on
```

### Senaryo 3: Ayarları Sıfırlama
```bash
# Router modunu durdur
sudo ./rpi-router.sh stop

# DHCP moduna geç
sudo ./rpi-router.sh ip-dhcp
```

## 📊 Durum Kontrolü

`status` komutu ile aşağıdaki bilgileri görebilirsiniz:

- **Interface IP Adresleri**: eth0 ve wlan0 IP'leri
- **IP Modu**: Static/DHCP durumu
- **IP Yönlendirme**: Aktif/Pasif
- **DHCP Sunucu Durumu**: Çalışıyor/Durmuş
- **NAT Tablosu**: Yönlendirme kuralları

```bash
sudo ./rpi-router.sh status
```

**Örnek Çıktı:**
```
====== DURUM BİLGİSİ ======
- eth0 IP: 192.168.1.2/24
- wlan0 IP: 10.0.1.15/24
- IP modu: STATIC (192.168.1.2/24)
- IP yönlendirme: 1
- DHCP sunucusu: active
- NAT tablosu:
    0     0 MASQUERADE  all  --  *      wlan0   0.0.0.0/0   0.0.0.0/0
==========================
```

## ⚠️ Önemli Notlar

- **Root Yetkisi**: Tüm komutlar `sudo` ile çalıştırılmalıdır
- **Ağ Arayüzleri**: Script eth0 (Ethernet) ve wlan0 (Wi-Fi) arayüzlerini kullanır
- **IP Aralığı**: DHCP sunucusu 192.168.1.10-100 aralığında IP dağıtır
- **Gateway IP**: Router IP adresi 192.168.1.2 olarak ayarlanır
- **Kalıcılık**: Ayarlar yeniden başlatmada sıfırlanır (systemd servis yapılabilir)
- **iptables Backend**: Modern Raspberry Pi OS sürümlerinde `iptables-nft` kullanılır

## 🔧 Özelleştirme

Script başındaki değişkenleri düzenleyerek ayarları değiştirebilirsiniz:

```bash
ETH_INTERFACE="eth0"        # Ethernet arayüzü
WLAN_INTERFACE="wlan0"      # Wi-Fi arayüzü
STATIC_IP="192.168.1.2/24"  # Statik IP adresi
DHCP_CONF="/etc/dnsmasq.conf" # DHCP yapılandırma dosyası
```

## 🆘 Sorun Giderme

### DHCP Sunucusu Başlamıyor
```bash
# dnsmasq loglarını kontrol edin
sudo journalctl -u dnsmasq -f
```

### NAT Çalışmıyor
```bash
# IP forwarding kontrolü
cat /proc/sys/net/ipv4/ip_forward

# iptables kurallarını listele
sudo iptables -t nat -L -n -v
```

### Arayüz IP'si Atanmıyor
```bash
# Arayüz durumunu kontrol edin
ip link show eth0
ip addr show eth0
```

## 📝 Lisans

Bu proje MIT lisansı altında dağıtılmaktadır.

---

**🔗 Faydalı Bağlantılar:**
- [Raspberry Pi Resmi Dokümantasyonu](https://www.raspberrypi.org/documentation/)
