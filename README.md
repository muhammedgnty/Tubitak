# 📡 IoT Tabanlı Meteorolojik Ölçüm ve LoRa Mesh İstasyonu

Bu proje, LoRa mesh haberleşme mimarisi ve ESP32 tabanlı meteorolojik veri toplama, merkezi sunucuya aktarma ve gerçek zamanlı analiz istasyonu altyapısını içerir. 

Proje bütünlüğünü korumak, olası veri ve kod kayıplarının önüne geçmek için tüm ekip üyelerinin aşağıdaki dizin yapısına, geliştirici kurallarına ve Git iş akışına uyması zorunludur.

---

## 📁 Proje Dizin Yapısı

Proje bileşenleri görev alanlarına göre bağımsız dizinlerde yönetilir:

* `firmware/` : ESP32 mikrootenetleyici kodları, sensör (BME280 vb.) sürücüleri ve LoRa haberleşme algoritmaları.
* `server/` : LoRa ağ geçidinden gelen verileri karşılayan arka yüz servisleri ve PostgreSQL veritabanı şemaları.
* `web/` : Meteorolojik verilerin anlık izlendiği ve risk analizlerinin sunulduğu arayüz kodları.
* `docs/` : Devre bağlantı şemaları, pin tabloları ve istasyon kutusu için 3D mekanik modeller.

---

## 📌 Temel Ekip Kuralları (Altın Kurallar)

1. **`main` Dalına Asla Doğrudan Kod Gönderilmez:** `main` dalı her an derlenebilir ve hatasız olmalıdır. Bu dala doğrudan `git push` atılamaz; tüm çalışmalar ayrı dallarda yapılır.
2. **Pull Request (PR) ve Kod İncelemesi Zorunludur:** Geliştirmesi biten her dal için GitHub üzerinden `main` dalına bir PR açılır. Takım içi inceleme ve onay tamamlanmadan kod birleştirilemez (merge edilemez).
3. **Temiz Depo İlkesi (`.gitignore`):** Derleme çıktıları (`.bin`, `.elf`, `build/`, `.pio/`), IDE ayarları (`.vscode/`), sanal ortamlar (`venv/`, `node_modules/`) ve şifre içeren dosyalar (`.env`, `secrets.h`) depoya kesinlikle commit edilemez.
4. **Güne Güncel Kodla Başlanır:** Yeni bir göreve başlamadan önce yerel ortamdaki `main` dalı mutlaka GitHub ile eşitlenir (`git pull`).

---

## 🌿 Dal (Branch) İsimlendirme Standartları

Açılacak her dal, yapılan görevin türünü belirten bir ön ek taşımalıdır:

| Tür | Format | Örnek |
| :--- | :--- | :--- |
| Yeni Özellik | `feature/gorev-adi` | `feature/lora-sx1278-mesh` |
| Hata Düzeltme | `bugfix/hata-adi` | `bugfix/bme280-i2c-timeout` |
| Dokümantasyon | `docs/konu-adi` | `docs/istasyon-pin-semasi` |
| Test / Deneme | `test/deneme-adi` | `test/derin-uyku-enerji-tuketimi` |

---

## 🔄 Adım Adım Günlük Çalışma Döngüsü

### 1. Yeni Bir Göreve Başlama
```bash
# 1. Ana dala geç
git checkout main

# 2. GitHub'daki en güncel hali bilgisayara indir
git pull

# 3. Temiz bir dal aç ve o dala geç
git checkout -b feature/gorev-adi
```

### 2. Geliştirme ve Yerel Kayıt (Commit)
```bash
# Değişiklik yapılan dosyaları kontrol et
git status

# İlgili dosyaları sahneye al
git add dosya1.cpp dosya2.h

# Veya o anki klasördeki tüm değişiklikleri topluca al
git add .

# Yapılan işi net açıklayan bir mesajla kaydet
git commit -m "LoRa paket iletim araligi optimize edildi"
```

### 3. Kodu GitHub'a Gönderme ve PR Açma
```bash
# Dalı ilk defa uzak depoya gönderiyorsan:
git push -u origin feature/gorev-adi

# Sonraki push işlemlerinde sadece:
git push
```
* Tarayıcıdan GitHub depo sayfasına git.
* Sarı şeritteki **Compare & pull request** butonuna tıkla.
* Yapılan geliştirmeyi açıklayan özet metni yaz ve PR'ı oluştur.
* Takım arkadaşlarını reviewer olarak ekle.

### 4. İş Bitimi ve Yerel Temizlik (PR Merge Edildikten Sonra)
PR incelenip `main` dalına merge edildikten sonra bilgisayarındaki geçici dalı sil:
```bash
# 1. Ana dala dön
git checkout main

# 2. Birleştirilen yeni kodu bilgisayarına çek
git pull

# 3. İşi biten yerel dalı sil
git branch -d feature/gorev-adi
```

---

## 💥 Çakışma (Merge Conflict) Çözme Rehberi

Aynı dosyanın aynı satırlarında farklı değişiklikler yapıldığında `git pull` sırasında çakışma meydana gelebilir:

1. Terminalde `CONFLICT` uyarısı veren dosyayı metin editöründe aç.
2. Git'in eklediği işaretleyicileri incele:
   ```text
   <<<<<<< HEAD
   (Senin bilgisayarındaki yerel kod)
   =======
   (GitHub'dan çekilen diğer ekip üyesinin kodu)
   >>>>>>> origin/main
   ```
3. `<<<<<<<`, `=======` ve `>>>>>>>` satırlarını sil.
4. İki tarafın mantığını birleştiren doğru kod bloğunu koruyup dosyayı kaydet.
5. Birleştirmeyi terminalden tamamla:
   ```bash
   git add cakisan_dosya.cpp
   git commit -m "Cakisma cozuldu: En guncel sensor frekansi secildi"
   git push origin main
   ```

---

## 🧰 Sık Kullanılan Git Komutları (Hızlı Başvuru)

**Dal İşlemleri**
* `git branch` : Bilgisayardaki yerel dalları listeler (aktif dalın yanında `*` bulunur).
* `git checkout <dal-adi>` : Var olan başka bir dala geçiş yapar.
* `git checkout -b <yeni-dal>` : Yeni bir dal açar ve anında o dala geçer.
* `git branch -d <dal-adi>` : Merge edilmiş yerel dalı güvenle siler.
* `git branch -D <dal-adi>` : Merge edilmemiş olsa dahi yerel dalı zorla siler.
* `git push origin --delete <dal-adi>` : GitHub üzerindeki uzak dalı siler.

**Durum ve Senkronizasyon**
* `git status` : Değişen, sahnelenen veya izlenmeyen dosyaları gösterir.
* `git log --oneline -n 5` : Son 5 commit kaydını tek satırlık özetler halinde listeler.
* `git pull` : GitHub'daki yenilikleri yerel depoya çeker ve birleştirir.

**Geri Alma ve Temizleme**
* `git restore <dosya>` : Sahneye alınmamış yerel değişiklikleri geri alır (dosyayı son commit haline döndürür).
* `git restore --staged <dosya>` : `git add` ile sahneye eklenen dosyayı sahneden çıkarır.
