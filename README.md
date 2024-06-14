# E-ticaret Projesi Backend

Bu proje, Bandırma Onyedi Eylül Üniversitesi E-ticaret Bitirme Projesi'nin backend kısmını oluşturmaktadır. Proje, modern ve kullanıcı dostu bir e-ticaret platformu geliştirmeyi amaçlamaktadır. Backend, kullanıcı kimlik doğrulama, ürün yönetimi, sipariş işlemleri ve daha fazlasını yönetmek için Node.js ve Sequelize kullanılarak geliştirilmiştir.

## Proje Genel Bakış

Bu backend, e-ticaret platformunun tüm sunucu tarafı işlevselliklerini sağlamaktadır. Kullanıcıların ürün arayabilmesi, sipariş verebilmesi ve satıcıların ürün ekleyebilmesi için gerekli API'leri içermektedir.

## Kullanılan Teknolojiler

- **Node.js**: Sunucu tarafı JavaScript çalışma ortamı.
- **Express**: Node.js için web uygulama çatısı.
- **Sequelize**: Node.js için promise tabanlı ORM.
- **MySQL**: Veritabanı yönetim sistemi.

## Kurulum ve Çalıştırma

### Gereksinimler

- Node.js (v14 veya üzeri)
- MySQL veritabanı

### Kurulum Adımları

1. **Projeyi Klonlayın**

```bash
git clone https://github.com/umutkarakas34/ecommerce-project.git
cd ecommerce-project/backend
```

2. **Gerekli Bağımlılıkları Yükleyin**

```bash
npm install
```

3. **Veritabanını Kurun**

MySQL üzerinde bir veritabanı oluşturun ve `config/config.json` dosyasını veritabanı bilgilerinize göre düzenleyin.

`utility/db.js` dosyasını aşağıdaki gibi yapılandırın:

```javascript
const Sequelize = require('sequelize');

const sequelize = new Sequelize('shopping', 'root', 'yourpassword', {
    dialect: 'mysql',
    host: 'localhost'
});

module.exports = sequelize;
```

4. **Veritabanı migrasyonlarını çalıştırmak için sequelize.sync() yorum satırındaki kodu yorum satırından çıkarın.**

5. **Sunucuyu Başlatın**

```bash
npm start
```

Sunucu varsayılan olarak `http://localhost:5000` adresinde çalışacaktır.

## Kod Yapısı

```javascript
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
const connection = require('./utility/db.js');
const adminRoutes = require('./routes/admin.js');
const sellerRoutes = require('./routes/seller.js');
const userRoutes = require('./routes/user.js');
const port = 5000;
const relationships = require('./models/relationship.js');
const sequelize = require('./utility/db');
const session = require('express-session');
const cors = require('cors');
require('dotenv').config();
const path = require('path');

// sequelize.sync({force:true});

app.use(cors());
app.use(express.json({ limit: "30mb", extended: true }));
app.use(express.urlencoded({ limit: "30mb", extended: true }));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.use('/admin', adminRoutes);
app.use('/seller', sellerRoutes);
app.use('/user', userRoutes);

app.use('/public', express.static(path.join(__dirname, 'public')));

app.listen(port, () => {
    console.log(`Sunucu ${port} portunda çalışıyor...`);
});
```

## API Dokümantasyonu

API uç noktaları ve kullanım bilgileri aşağıdaki gibidir:

### Kullanıcı İşlemleri

- **Giriş Yapma**: `POST /user/login`
- **Kayıt Olma**: `POST /user/register`
- **Profilimi Görüntüleme**: `GET /user/my-account`
- **Profilimi Güncelleme**: `PUT /user/update-account`

### Sepet ve Ürün İşlemleri

- **Sepetimi Görüntüleme**: `GET /user/my-basket`
- **En Çok Satan Ürünleri Görüntüleme**: `GET /user/topselling`
- **Sepete Ürün Ekleme**: `POST /user/add-item`
- **Sepetteki Ürünü Güncelleme**: `POST /user/update-item`
- **Sepetten Ürün Silme**: `POST /user/delete-item`
- **Sepeti Temizleme**: `POST /user/clear-cart`

### Liste İşlemleri

- **Listeleri Görüntüleme**: `GET /user/lists`
- **Liste Oluşturma**: `POST /user/create-list`
- **Liste Silme**: `POST /user/delete-list/:listId`
- **Liste Güncelleme**: `POST /user/update-list/:listId`
- **Herkese Açık Listeyi Görüntüleme**: `GET /user/publicList/:slug`

### Liste - Ürün İşlemleri

- **Listedeki Ürünleri Görüntüleme**: `GET /user/lists/:listId/items`
- **Listeye Ürün Ekleme**: `POST /user/add-Item-to-List`
- **Listeden Ürün Çıkarma**: `POST /user/remove-Item-to-List`

### Adres İşlemleri

- **Adresleri Görüntüleme**: `GET /user/addresses`
- **Adres Oluşturma**: `POST /user/create-address`
- **Adresi Güncelleme**: `PUT /user/addresses/:addressId`
- **Adres Silme**: `DELETE /user/addresses/:addressId`

### Sipariş İşlemleri

- **Siparişleri Görüntüleme**: `GET /user/orders`
- **Sipariş Detaylarını Görüntüleme**: `GET /user/order/:orderId`
- **Sipariş Ürünlerini Görüntüleme**: `GET /user/orderItems/:orderId`
- **Sipariş Oluşturma**: `POST /user/create-order`
- **Sipariş Ürünü İptal Etme**: `POST /user/cancel-order-item`
- **Sipariş İptal Etme**: `POST /user/cancel-order`

### Favori İşlemleri

- **Favorileri Görüntüleme**: `GET /user/favorites`
- **Favori Ürün Ekleme**: `POST /user/addFavoriteItem`
- **Favori Ürün Silme**: `POST /user/deleteFavoriteItem`

### Ürün Değerlendirme İşlemleri

- **Ürün Yorumlarını Görüntüleme**: `GET /user/product-comments/:productId`
- **Kendi Ürün Yorumlarımı Görüntüleme**: `GET /user/my-product-comments`
- **Ürün Yorum Ekleme**: `POST /user/create-product-comment`
- **Ürün Yorumu Güncelleme**: `POST /user/update-product-comment`
- **Ürün Yorumu Silme**: `POST /user/delete-product-comment`

### Satıcı Değerlendirme İşlemleri

- **Satıcı Yorumlarını Görüntüleme**: `GET /user/seller-comments/:sellerId`
- **Kendi Satıcı Yorumlarımı Görüntüleme**: `GET /user/my-seller-comments`
- **Satıcı Yorumu Ekleme**: `POST /user/create-seller-comment`
- **Satıcı Yorumu Güncelleme**: `POST /user/update-seller-comment`
- **Satıcı Yorumu Silme**: `POST /user/delete-seller-comment`

### Takip İşlemleri

- **Satıcı Takip Etme/Bırakma**: `POST /user/follow`
- **Takip Durumunu Kontrol Etme**: `GET /user/follow-status/:sellerId`
- **Takip Edilen Satıcıları Görüntüleme**: `GET /user/followed-sellers`

### İade İşlemleri

- **İade Talebi Oluşturma**: `POST /user/create-return`
- **İade Taleplerimi Görüntüleme**: `GET /user/my-returns`
- **İade Talebi İptal Etme**: `POST /user/cancel-return`

### Ürün Soru İşlemleri

- **Ürün Sorusu Sorma**: `POST /user/create-product-question`
- **Sorularımı Görüntüleme**: `GET /user/my-questions`
- **Ürün Sorularını Görüntüleme**: `GET /user/products/:productId/answered-questions`

### Kategori İşlemleri

- **Kategorileri Görüntüleme**: `GET /user/categories`
- **Alt Kategorileri Görüntüleme**: `GET /user/categories/:categoryId`

### Arama İşlemleri

- **Ürün Arama**: `GET /user/urunAra`

### Slug Kullanımı

- **Ürünleri Slug ile Görüntüleme**: `GET /user/product/:productSlug`
- **Satıcı Ürünlerini Slug ile Görüntüleme**: `GET /user/products/:productSlug`
- **Satıcı Ürünlerini Görüntüleme**: `GET /user/seller-products/:sellerSlug`
- **Satıcı Ürün Detaylarını Görüntüleme**: `GET /user/sellerProducts/:productId/:sellerProductId`
- **Satıcı Bilgilerini Görüntüleme**: `GET /user/sellerInfo/:sellerSlug`
- **Kategori Ürünlerini Slug ile Görüntüleme**: `GET /user/category/:categorySlug`
- **Marka Ürünlerini Slug ile Görüntüleme**: `GET /user/brand/:brandSlug`
- **Ürün Fotoğraflarını Görüntüleme**: `GET /user/productPhoto/:productId`
- **Yorum Kontrolü**: `GET /user/commentControl/:productId`

## Katkıda Bulunma

Katkılar memnuniyetle karşılanır! Herhangi bir fikriniz, öneriniz varsa veya geliştirmeye yardımcı olmak istiyorsanız, bir sorun açabilir veya pull request gönderebilirsiniz.

## Lisans

Bu proje MIT Lisansı altında lisanslanmıştır. Ayrıntılar için [LICENSE](LICENSE) dosyasına bakınız.

---

Bandırma Onyedi Eylül Üniversitesi E-ticaret Bitirme Projesi'nin bir parçası olarak bu backend'i geliştirdik. Projeyi keşfetmekte ve geliştirilmesine katkıda bulunmakta özgürsünüz!

---

Bu projenin tamamı [mmutlucod/eCommerce](https://github.com/mmutlucod/eCommerce) linkinde frontend ile backend birleştirilmiş haldedir.
