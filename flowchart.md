# Berber Rezervasyon Sistemi - Detaylı Flowchart

## 1. SİSTEM MİMARİSİ

### Ana Bileşenler:
- **Frontend Web**: React (Responsive)
- **Frontend Mobile**: Flutter (iOS + Android)
- **Backend**: Node.js + TypeScript
- **Database**: MySQL
- **Harita**: Google Maps API
- **Ödeme**: İşbank, Ziraat Bank API'leri

## 2. KULLANICI TİPLERİ VE AKIŞLAR

### A) MÜŞTERİ AKIŞI

#### 2.1 Müşteri Kayıt/Giriş
```
Müşteri → Kayıt Ol → Telefon/Email Doğrulama → Profil Oluşturma
```

#### 2.2 Berber Arama ve Rezervasyon
```
Müşteri Girişi → İl Seçimi (5 İl) → Berber Listesi → 
Filtreleme (Yakınlık, Yıldız, Hizmet) → Berber Seçimi → 
Hizmet Seçimi → Tarih/Saat Seçimi → Ödeme → Rezervasyon Onayı
```

#### 2.3 Ödeme ve İptal Sistemi
```
Ödeme → Havuzda Bekleme → Hizmet Alımı → Berber Onayı → 
Para Berbere Transfer (%5 Komisyon Kesintisi)

İptal Durumları:
- Aynı gün iptal: %50 iade
- 1 gün önceden iptal: %100 iade
- 1 saatten az kala iptal: İade yok
```

### B) BERBER AKIŞI

#### 2.4 Berber Kayıt ve Onay
```
Berber Kayıt → Ustalık Belgesi + Daire Sözleşmesi Yükleme → 
AI Kontrolü → Manuel Onay → 1 Ay Bedava Deneme → 
Ücretli Üyelik Seçimi
```

#### 2.5 Berber Panel Yönetimi
```
Berber Paneli → Hizmet Ekleme/Düzenleme → Fiyat Belirleme → 
Çalışma Saatleri → Rezervasyon Takibi → Gelir Raporları → 
Müşteri Değerlendirmeleri
```

### C) ADMIN AKIŞI

#### 2.6 Admin Yönetimi
```
Admin Paneli → Berber Onayları → Şikayet Yönetimi → 
Gelir Raporları → Sistem Ayarları → Kullanıcı Yönetimi
```

## 3. DETAYLI VERİTABANI YAPISI

### 3.1 Ana Tablolar
- **users** (müşteriler)
- **barbers** (berberler)
- **services** (hizmetler)
- **bookings** (rezervasyonlar)
- **payments** (ödeme kayıtları)
- **reviews** (değerlendirmeler)
- **complaints** (şikayetler)
- **cities** (5 il)
- **subscriptions** (berber abonelikleri)

### 3.2 İlişkiler
- Bir berber birden fazla hizmet sunabilir
- Bir müşteri birden fazla rezervasyon yapabilir
- Bir rezervasyon bir ödeme ile ilişkili
- Berberler şehirlere göre gruplandırılır

## 4. ÖNEMLİ ÖZELLİKLER

### 4.1 Sıralama Sistemi
- **Temel Sıralama**: Yıldız ortalaması + yakınlık
- **Premium Sıralama**: Aylık 5000TL ödeyen berberler üstte
- **Dinamik**: Rezervasyon sayısı, güncel değerlendirmeler

### 4.2 Bildirim Sistemi
- **SMS/Email**: Müşteri tercihi
- **Push Notification**: Mobil uygulama
- **Otomatik**: Rezervasyon onayı, hatırlatma, iptal

### 4.3 Çoklu Dil
- **Türkçe** (varsayılan)
- **İngilizce**
- Dil seçimi kullanıcı profilinde

## 5. GÜVENLİK VE DOĞRULAMA

### 5.1 Müşteri Doğrulama
- Telefon numarası SMS doğrulama
- Email doğrulama
- Profil bilgileri (isim, soyisim)

### 5.2 Berber Doğrulama
- Ustalık belgesi (AI kontrolü)
- Daire/koçan sözleşmesi
- Manuel admin onayı

## 6. PARA KAZANMA MODELİ

### 6.1 Gelir Kaynakları
1. **Komisyon**: Her işlemden %5
2. **Premium Sıralama**: Aylık 5000TL
3. **Berber Abonelikleri**: 
   - 1 ay: 1000TL
   - 3 ay: 2500TL
   - 6 ay: 4000TL
   - 12 ay: 6800TL

### 6.2 Maliyet Yapısı
- SMS/Email maliyetleri
- Google Maps API maliyetleri
- Sunucu maliyetleri
- Ödeme gateway komisyonları

## 7. TEKNİK DETAYLAR

### 7.1 API Endpoints (Ana)
- `/api/auth/*` - Kimlik doğrulama
- `/api/barbers/*` - Berber işlemleri
- `/api/bookings/*` - Rezervasyon işlemleri
- `/api/payments/*` - Ödeme işlemleri
- `/api/reviews/*` - Değerlendirme işlemleri

### 7.2 Real-time Özellikler
- WebSocket bağlantıları
- Anlık rezervasyon güncellemeleri
- Push notification sistemi

## 8. GELİŞTİRME AŞAMALARI

### Faz 1: Temel Sistem
- Kullanıcı kayıt/giriş
- Berber listesi ve arama
- Basit rezervasyon sistemi

### Faz 2: Ödeme Entegrasyonu
- Ödeme gateway entegrasyonu
- Havuz sistemi
- İptal/iade mantığı

### Faz 3: Gelişmiş Özellikler
- Değerlendirme sistemi
- Bildirim sistemi
- Admin paneli

### Faz 4: Mobil Uygulama
- Flutter geliştirme
- Push notification
- Offline özellikler

## 9. RİSK YÖNETİMİ

### 9.1 Müşteri Kaybı Riski
- **Çözüm**: Sadakat programı, indirimler
- **Önlem**: Müşteri deneyimini optimize etme

### 9.2 Berber Kaybı Riski
- **Çözüm**: Rekabetçi komisyon oranları
- **Önlem**: Berber memnuniyetini artırma

### 9.3 Teknik Riskler
- **Çözüm**: Yedekleme sistemleri
- **Önlem**: Test ortamında kapsamlı testler
