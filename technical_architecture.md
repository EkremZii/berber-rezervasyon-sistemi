# Berber Rezervasyon Sistemi - Teknik Mimari

## 1. GENEL MİMARİ YAKLAŞIMI

### 1.1 Mikroservis Mimarisi
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   React Web     │    │  Flutter App    │    │   Admin Panel   │
│   (Frontend)    │    │   (Mobile)      │    │   (React)       │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                    ┌─────────────┴─────────────┐
                    │      API Gateway          │
                    │    (Rate Limiting,        │
                    │     Authentication)       │
                    └─────────────┬─────────────┘
                                  │
        ┌─────────────────────────┼─────────────────────────┐
        │                         │                         │
┌───────▼────────┐    ┌──────────▼──────────┐    ┌────────▼────────┐
│  Auth Service  │    │  Booking Service    │    │ Payment Service │
│  (Node.js)     │    │   (Node.js)         │    │   (Node.js)     │
└────────────────┘    └─────────────────────┘    └─────────────────┘
        │                         │                         │
        └─────────────────────────┼─────────────────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │      MySQL Database       │
                    │    (Primary Database)     │
                    └───────────────────────────┘
```

## 2. BACKEND MİMARİSİ (Node.js + TypeScript)

### 2.1 Proje Yapısı
```
backend/
├── src/
│   ├── controllers/          # API endpoint handlers
│   ├── services/            # Business logic
│   ├── models/              # Database models
│   ├── middleware/          # Authentication, validation
│   ├── routes/              # API routes
│   ├── utils/               # Helper functions
│   ├── config/              # Configuration files
│   └── types/               # TypeScript type definitions
├── tests/                   # Unit and integration tests
├── docs/                    # API documentation
└── package.json
```

### 2.2 Ana Servisler

#### Auth Service
```typescript
// Kullanıcı kimlik doğrulama
- JWT token yönetimi
- OTP doğrulama (SMS/Email)
- Role-based access control
- Password hashing (bcrypt)
```

#### Booking Service
```typescript
// Rezervasyon yönetimi
- Rezervasyon oluşturma/güncelleme
- Tarih/saat kontrolü
- Berber müsaitlik kontrolü
- İptal/iade işlemleri
```

#### Payment Service
```typescript
// Ödeme işlemleri
- Ödeme gateway entegrasyonu
- Havuz sistemi yönetimi
- Komisyon hesaplama
- İade işlemleri
```

#### Notification Service
```typescript
// Bildirim sistemi
- SMS gönderimi
- Email gönderimi
- Push notification
- WebSocket real-time updates
```

### 2.3 Database Schema (MySQL)

#### Users Tablosu
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_verified BOOLEAN DEFAULT FALSE,
    language ENUM('tr', 'en') DEFAULT 'tr',
    notification_preferences JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### Barbers Tablosu
```sql
CREATE TABLE barbers (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    business_name VARCHAR(255) NOT NULL,
    address TEXT NOT NULL,
    city ENUM('lefkosa', 'girne', 'guzelyurt', 'iskele', 'magusa', 'lefke') NOT NULL,
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    phone VARCHAR(20) NOT NULL,
    license_document VARCHAR(500),
    contract_document VARCHAR(500),
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    rating DECIMAL(3, 2) DEFAULT 0.00,
    total_reviews INT DEFAULT 0,
    subscription_type ENUM('free', 'premium_ranking') DEFAULT 'free',
    subscription_expires_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

#### Services Tablosu
```sql
CREATE TABLE services (
    id INT PRIMARY KEY AUTO_INCREMENT,
    barber_id INT NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    duration_minutes INT NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (barber_id) REFERENCES barbers(id)
);
```

#### Bookings Tablosu
```sql
CREATE TABLE bookings (
    id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    barber_id INT NOT NULL,
    service_id INT NOT NULL,
    booking_date DATE NOT NULL,
    booking_time TIME NOT NULL,
    status ENUM('pending', 'confirmed', 'completed', 'cancelled') DEFAULT 'pending',
    total_amount DECIMAL(10, 2) NOT NULL,
    commission_amount DECIMAL(10, 2) NOT NULL,
    payment_status ENUM('pending', 'paid', 'refunded') DEFAULT 'pending',
    barber_confirmed_at TIMESTAMP NULL,
    completed_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES users(id),
    FOREIGN KEY (barber_id) REFERENCES barbers(id),
    FOREIGN KEY (service_id) REFERENCES services(id)
);
```

## 3. FRONTEND MİMARİSİ

### 3.1 React Web Uygulaması

#### Proje Yapısı
```
frontend-web/
├── src/
│   ├── components/          # Reusable components
│   │   ├── ui/             # Base UI components
│   │   ├── forms/          # Form components
│   │   └── layout/         # Layout components
│   ├── pages/              # Page components
│   ├── hooks/              # Custom React hooks
│   ├── services/           # API service calls
│   ├── store/              # State management (Redux/Zustand)
│   ├── utils/              # Helper functions
│   └── types/              # TypeScript types
├── public/
└── package.json
```

#### Ana Sayfalar
- **Ana Sayfa**: Berber arama ve filtreleme
- **Berber Detay**: Hizmetler, değerlendirmeler, rezervasyon
- **Rezervasyon**: Tarih/saat seçimi, ödeme
- **Profil**: Kullanıcı bilgileri, geçmiş rezervasyonlar
- **Giriş/Kayıt**: Kimlik doğrulama

### 3.2 Flutter Mobil Uygulaması

#### Proje Yapısı
```
frontend-mobile/
├── lib/
│   ├── models/             # Data models
│   ├── services/           # API services
│   ├── screens/            # UI screens
│   ├── widgets/            # Reusable widgets
│   ├── providers/          # State management
│   └── utils/              # Helper functions
├── assets/
└── pubspec.yaml
```

#### Ana Özellikler
- **Harita Entegrasyonu**: Google Maps Flutter plugin
- **Push Notifications**: Firebase Cloud Messaging
- **Offline Support**: Local database (SQLite)
- **Biometric Auth**: Fingerprint/Face ID

## 4. API TASARIMI

### 4.1 RESTful API Endpoints

#### Authentication
```
POST /api/auth/register
POST /api/auth/login
POST /api/auth/verify-otp
POST /api/auth/refresh-token
POST /api/auth/logout
```

#### Barbers
```
GET /api/barbers?city={city}&lat={lat}&lng={lng}&radius={radius}
GET /api/barbers/{id}
GET /api/barbers/{id}/services
GET /api/barbers/{id}/reviews
POST /api/barbers/register
PUT /api/barbers/{id}
```

#### Bookings
```
GET /api/bookings (user's bookings)
POST /api/bookings
PUT /api/bookings/{id}/cancel
GET /api/bookings/{id}
```

#### Payments
```
POST /api/payments/process
GET /api/payments/{id}/status
POST /api/payments/{id}/refund
```

### 4.2 WebSocket Events
```typescript
// Real-time updates
- booking_confirmed
- booking_cancelled
- payment_processed
- new_review
```

## 5. GÜVENLİK

### 5.1 Authentication & Authorization
- **JWT Tokens**: Access + Refresh token pattern
- **Rate Limiting**: API endpoint koruması
- **CORS**: Cross-origin request kontrolü
- **Input Validation**: Tüm girişlerin doğrulanması

### 5.2 Data Protection
- **Password Hashing**: bcrypt ile şifreleme
- **Sensitive Data**: Kredi kartı bilgileri şifrelenmiş
- **HTTPS**: Tüm iletişim SSL/TLS
- **SQL Injection**: Prepared statements

## 6. PERFORMANS OPTİMİZASYONU

### 6.1 Database
- **Indexing**: Sık kullanılan sorgular için indexler
- **Connection Pooling**: Veritabanı bağlantı havuzu
- **Query Optimization**: Sorgu performans analizi

### 6.2 Caching
- **Redis**: Session ve cache yönetimi
- **CDN**: Static dosyalar için
- **Browser Caching**: Frontend asset caching

### 6.3 Monitoring
- **Logging**: Winston ile log yönetimi
- **Error Tracking**: Sentry entegrasyonu
- **Performance Monitoring**: APM araçları

## 7. DEPLOYMENT

### 7.1 Backend Deployment
- **Docker**: Containerization
- **AWS/GCP**: Cloud deployment
- **PM2**: Process management
- **Nginx**: Reverse proxy

### 7.2 Frontend Deployment
- **Web**: Vercel/Netlify
- **Mobile**: Google Play Store + Apple App Store
- **CI/CD**: GitHub Actions

## 8. TEST STRATEJİSİ

### 8.1 Backend Testing
- **Unit Tests**: Jest + Supertest
- **Integration Tests**: API endpoint testleri
- **E2E Tests**: Cypress

### 8.2 Frontend Testing
- **React**: Jest + React Testing Library
- **Flutter**: Flutter Test + Integration Tests

## 9. GELİŞTİRME ARAÇLARI

### 9.1 Development Tools
- **Code Editor**: VS Code
- **Version Control**: Git + GitHub
- **API Testing**: Postman/Insomnia
- **Database**: MySQL Workbench

### 9.2 Code Quality
- **Linting**: ESLint + Prettier
- **Type Checking**: TypeScript strict mode
- **Code Review**: GitHub Pull Requests
- **Documentation**: JSDoc + Swagger
