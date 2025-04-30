# Ödeme Kontrolü API Dokümantasyonu

## Genel Bakış

Bu API, ödeme ve çekim işlemlerinin durumlarını kontrol etmek için kullanılır. İşlem entegratörleri, bu API sayesinde gerçekleştirdikleri ödeme ve çekim işlemlerinin son durumlarını sorgulayabilirler.

## Güvenlik

API'ye erişim, `X-API-KEY` başlığında (header) sağlanan API anahtarı ile güvence altına alınmıştır. API anahtarınızı güvenli bir şekilde saklayınız ve üçüncü taraflarla paylaşmayınız.

## Temel URL

Tüm API istekleri için temel URL:

```
https://lipaykripto.com/api
```

## Endpoints

### Ödeme ve Çekim Durumu Kontrolü

```
GET /paymentcontroller
```

Bu endpoint, ödeme ve çekim işlemlerinin durumunu sorgulamak için kullanılır.

#### Sorgu Parametreleri

| Parametre  | Tip     | Zorunlu | Açıklama                                                |
|------------|---------|---------|--------------------------------------------------------|
| type       | string  | Evet    | İşlem tipi. "payment" veya "withdraw" değerlerini alır |
| paymentId  | string  | Evet    | Kontrol edilecek işlemin benzersiz ID'si               |

#### Request Headers

| Header     | Değer          | Açıklama                                        |
|------------|----------------|------------------------------------------------|
| X-API-KEY  | YOUR_PC_API_KEY| Size özel olarak sağlanan API anahtarı         |

#### Örnek İstekler

**Ödeme Durumu Kontrolü:**

```http
GET https://lipaykripto.com/api/paymentcontroller?type=payment&paymentId=test123
X-API-KEY: YOUR_PC_API_KEY
```

**Çekim Durumu Kontrolü:**

```http
GET https://lipaykripto.com/api/paymentcontroller?type=withdraw&paymentId=test456
X-API-KEY: YOUR_PC_API_KEY
```

#### Başarılı Yanıt (200 OK)

```json
{
  "success": true,
  "data": {
    "paymentId": "test123",
    "status": "confirmed",
    "tryAmount": "1000.00"
  }
}
```

#### Yanıt Özellikleri

| Alan          | Tip     | Açıklama                                     |
|---------------|---------|----------------------------------------------|
| success       | boolean | İsteğin başarılı olup olmadığını gösterir    |
| data          | object  | İşlem bilgilerini içeren nesne               |
| data.paymentId| string  | İşlem ID'si                                  |
| data.status   | string  | İşlem durumu (confirmed, failed, pending)    |
| data.tryAmount| string  | İşlem tutarı (TRY cinsinden)                 |

#### Status Değerleri

| Değer     | Açıklama                 |
|-----------|--------------------------|
| confirmed | İşlem onaylandı          |
| failed    | İşlem başarısız oldu     |
| pending   | İşlem beklemede          |

#### Hata Yanıtları

```json
{
  "success": false,
  "error": {
    "code": 401,
    "message": "Geçersiz API anahtarı"
  }
}
```

#### Hata Kodları

| Kod | Açıklama                    |
|-----|----------------------------|
| 400 | Geçersiz istek parametreleri|
| 401 | Geçersiz API anahtarı       |
| 404 | İşlem bulunamadı            |
| 500 | Sunucu hatası               |

## Entegrasyon Adımları

### 1. İstek Oluşturma

**cURL ile Örnek:**

```bash
curl -X GET "https://lipaykripto.com/api/paymentcontroller?type=payment&paymentId=test123" \
     -H "X-API-KEY: YOUR_PC_API_KEY"
```

### 2. Yanıtları İşleme

Başarılı bir yanıt aldığınızda, işlem durumuna göre uygun aksiyonları almalısınız:

- **confirmed**: İşlem onaylandı, kullanıcıya hizmet/ürün sağlayabilirsiniz
- **pending**: İşlem hala işleniyor, biraz sonra tekrar kontrol edin
- **failed**: İşlem başarısız oldu, kullanıcıya bilgi verin
