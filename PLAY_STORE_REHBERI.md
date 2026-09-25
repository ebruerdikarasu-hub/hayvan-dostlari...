# Hayvan Dostları — Play Store'a Yükleme Rehberi

Önce önemli bir gerçek: Ben (Claude) sana çalıştırılabilir bir Android uygulaması (.apk/.aab) **doğrudan üretemem** — bunun için gerçek bir derleme/imzalama ortamı ve Google hesabın gerekiyor. Ama sana bu paketi (web uygulaması + ikonlar + manifest) hazırladım ve aşağıdaki adımları **kodsuz** şekilde, kendi başına tamamlayabilirsin. Toplam süre: genelde 1-3 saat + Google'ın inceleme süresi (birkaç gün).

## Elindeki Paket
- `index.html` → oyunun kendisi (5 dilli, sesli, PWA-uyumlu)
- `manifest.json`, `service-worker.js` → uygulamanın "gerçek bir app gibi" çalışmasını sağlayan dosyalar
- `icon-*.png`, `favicon-*.png`, `apple-touch-icon-180.png` → uygulama simgeleri
- `PRIVACY_POLICY.md` → Play Store'un **zorunlu** istediği gizlilik politikası (Türkçe + İngilizce)

---

## 1. Adım: Uygulamayı Kendi Alan Adında Barındır
Google, bir web uygulamasını Android paketine çevirirken senin **sahibi olduğun** bir web adresi ister (bu yüzden bu dosyaları doğrudan claude.ai üzerinden yayına alamayız). En kolay ve ücretsiz seçenek: **GitHub Pages**.

1. github.com'da ücretsiz bir hesap aç.
2. Yeni bir repo oluştur (ör. `hayvan-dostlari`).
3. Bu klasördeki tüm dosyaları (`index.html`, `manifest.json`, `service-worker.js`, ikonlar) o repoya yükle.
4. Repo ayarlarından **Settings → Pages** kısmına git, "Deploy from branch" seç, `main` dalını seç, kaydet.
5. Birkaç dakika sonra sana `https://kullaniciadin.github.io/hayvan-dostlari/` gibi bir adres verecek. Bu senin **canlı web adresin**.

*(Alternatifler: (Netlify şu an bu hesapta kredi kısıtı nedeniyle kullanılamıyor, o yüzden GitHub Pages en garantili seçenek.))*

## 2. Adım: PWABuilder ile Android Paketi Oluştur
1. https://www.pwabuilder.com adresine git.
2. 1. adımda aldığın web adresini kutuya yapıştır, "Start" de.
3. PWABuilder sitene bakıp bir puan verecek (manifest, ikon, service worker kontrolü) — hazırladığım dosyalar sayesinde bu puan yüksek çıkmalı.
4. "Package for stores" → **Android** seçeneğine tıkla.
5. Paket türü olarak **Trusted Web Activity (TWA)** seçilecek (varsayılan, doğru olan budur).
6. "Generate" de, indirdiğin zip dosyasının içinde bir `.aab` (Android App Bundle) dosyası ve bir imza anahtarı (keystore) bulacaksın.
   ⚠️ **Bu keystore dosyasını mutlaka güvenli bir yere yedekle** — ileride uygulamanı güncellemek için tekrar aynı anahtara ihtiyacın olacak, kaybedersen o uygulamayı bir daha güncelleyemezsin.
7. PWABuilder sana bir de `assetlinks.json` dosyası verecek — bunu 1. adımdaki sitenin (`.well-known/assetlinks.json` yoluna) yüklemen gerekiyor. Bu, "bu Android uygulaması gerçekten senin web sitenin sahibidir" doğrulamasıdır; atlarsan uygulama tarayıcı adres çubuğu gösteren bir mod açar (çirkin durur ama çalışır).

## 3. Adım: Google Play Console Hesabı
1. https://play.google.com/console adresinden **tek seferlik 25 USD** ödeyerek geliştirici hesabı aç (kimlik doğrulama birkaç gün sürebilir).
2. "Uygulama Oluştur" de, adını gir (ör. "Hayvan Dostları"), dil olarak Türkçe (ve istersen İngilizce ekle), "Uygulama" ve "Ücretsiz" seç.

## 4. Adım: Mağaza Listeleme Bilgileri
Play Console seni adım adım yönlendirecek, istenecekler:
- **Kısa açıklama** (80 karakter): ör. "5-8 yaş için sesli, sevgi dolu, çok dilli eğitici hayvan oyunu"
- **Uzun açıklama** (4000 karaktere kadar): özellikleri anlat (10 bölüm, eşleştirme oyunu, 2 kişilik yarışma, 5 dil, rozetler)
- **Uygulama simgesi**: `icon-512.png` (hazır)
- **Öne çıkan görsel (feature graphic)**: 1024x500 px — bunu henüz hazırlamadım, istersen ayrıca hazırlayabilirim
- **Ekran görüntüleri**: en az 2 tane, telefon için — uygulamayı GitHub Pages adresinden açıp birkaç ekran görüntüsü alman yeterli
- **Gizlilik Politikası URL'si**: `PRIVACY_POLICY.md` dosyasını da yine GitHub/Netlify üzerine koyup linkini burada vereceksin (ya da basitçe bir Google Docs/Notion sayfasına yapıştırıp linkini paylaşabilirsin)

## 5. Adım: Data Safety (Veri Güvenliği) Formu
Uygulama hiçbir veri toplamadığı ve sunucuya göndermediği için bu formda büyük çoğunlukla **"Hayır"** işaretleyeceksin:
- Veri topluyor musunuz? → Uygulama içindeki isim/avatar sadece cihazda kalıyor, sunucuya gönderilmiyor → "Veri toplanmıyor" olarak işaretleyebilirsin.
- Reklam var mı? → Hayır (v1'de gerçek reklam yok, sadece eğlenceli bir "mola" ekranı var).
- Üçüncü taraf paylaşım? → Hayır.

⚠️ İleride 9-10. adımlardaki gibi gerçek AdMob reklamı veya Play Billing satın alma eklersen, bu formu **güncellemen zorunlu** — reklam SDK'sı ve ödeme sağlayıcısı birer "üçüncü taraf" sayılır ve doğru beyan edilmesi gerekir.

## 6. Adım: İçerik Derecelendirmesi (Content Rating)
IARC anketini dolduracaksın — şiddet, korku, kullanıcı etkileşimi vb. sorulara dürüstçe "yok/hayır" dersen muhtemelen **"Herkes / 3 Yaş+"** derecesi çıkacaktır.

## 7. Adım: Hedef Kitle ve İçerik (Target Audience)
Burada "bu uygulama çocuklara mı yönelik?" sorusu çok önemli:
- Eğer **evet, birincil hedef kitle çocuklar** dersen → Google Play **Aile Politikaları (Families Policy)** devreye girer: reklam olursa sadece onaylı "kid-safe" SDK'lar kullanılabilir, dış bağlantı/sosyal özellik olamaz, veri toplama çok kısıtlıdır.
- v1'de reklam/takip/dış bağlantı olmadığı için bu politikaya zaten uygunsun — dürüstçe "çocuklara yönelik" işaretlemeni öneririm.

## 8. Adım: Yükle ve Gönder
1. "Production" (Üretim) sekmesinden 2. adımda indirdiğin `.aab` dosyasını yükle.
2. Ülke/bölge seç (hepsi ya da Türkiye + istediğin ülkeler).
3. "Gözden geçir ve yayınla" de.
4. Google incelemesi genelde **birkaç saat ile birkaç gün** sürer; çocuklara yönelik uygulamalarda inceleme biraz daha sıkı olabilir.

---

## Yayından Önce Son Kontrol Listesi
- [x] Satın alma öncesi **ebeveyn kontrolü (parental gate)** eklendi — basit bir toplama sorusu çözülmeden Premium satın alma tetiklenmiyor. Bu, Google Play Aile Politikaları'nın ve Apple'ın (ileride iOS'a geçersen) çocuk uygulamaları için zorunlu tuttuğu bir kural.
- [x] Hiçbir zaman çalışmayacak "Uzaktan Yarışma" seçeneği, mağaza sürümünde tamamen gizlendi (kafa karıştıran/bozuk görünen bir özellik kalmadı).
- [x] Sahte satın alma / sahte reklam yok — `purchasePremium()` ve `maybeShowInterstitial()` sadece gerçek native eklenti varsa çalışıyor, yoksa dürüst bir mesaj gösteriyor.
- [ ] `assetlinks.json` doğru yüklendi mi (TWA'nın adres çubuğu göstermemesi için)
- [ ] Gizlilik politikası linki çalışıyor mu
- [ ] En az 2 ekran görüntüsü hazır mı
- [ ] Feature graphic (1024x500) hazır mı
- [ ] Uygulamayı gerçek bir Android telefonda (Chrome üzerinden PWA olarak "Ana ekrana ekle" ile) test ettin mi
- [ ] Ses/dil değişimi telefonda düzgün çalışıyor mu (bazı Android cihazlarda Hollandaca/Arapça seslendirme yüklü olmayabilir — bu bir uygulama hatası değil, cihaz kısıtıdır)

## Bilmen Gereken Sınırlamalar
- **"Uzaktan/Çevrimiçi Yarışma"** özelliği, sadece Claude.ai içindeki demo ortamında çalışır; Play Store'a yüklenen sürümde bu buton otomatik olarak pasif görünür (kod zaten buna göre yazıldı, hiçbir hata vermez).

---

## 9. Gelir Modeli — Premium (Gerçek Ödeme)

Uygulama artık gerçek bir **freemium** modeliyle geliyor:
- **Ücretsiz:** Türkçe + İngilizce diller, Hayvanlar/Renkler/Sayılar bölümleri, 6 çiftli eşleştirme oyunu.
- **Premium (kilitli):** Almanca/Hollandaca/Arapça, kalan 7 bölüm, 2 Kişilik Yarışma modu, 8 çiftli zor eşleştirme.

Kod içinde `purchasePremium()` adında bir fonksiyon var — bu fonksiyon, telefonda gerçek bir native satın alma eklentisi (Google Play Billing) bulursa onu çağırır; bulamazsa (yani şu anki TWA paketinde) kullanıcıya "Bu satın alma Play Store sürümünde etkinleşir" mesajını gösterir ve **hiçbir zaman sahte bir "satın alındı" göstermez.** Bu kasıtlı — Google, çalışmayan/sahte satın alma butonlarını reddeder.

**Gerçek ödeme almak için** Google Play Billing'i uygulamana bağlaman gerekiyor. Bunun için TWA yetmez, aşağıdaki "10. Adım"daki gibi Capacitor'e geçmen lazım. Adımlar:
1. Play Console'da uygulamanı oluşturduktan sonra **Monetize → Products → In-app products** kısmından `premium_unlock` adında (kodda geçen isimle birebir aynı) bir ürün oluştur, fiyatını belirle (örn. 29,99 TL / tek seferlik).
2. Capacitor projesine `@capacitor-community/in-app-purchases` (veya güncel muadili) eklentisini kur.
3. `purchasePremium()` fonksiyonundaki `window.CapacitorPurchases.purchase('premium_unlock')` çağrısı, bu eklentinin gerçek API'siyle eşleşecek şekilde küçük bir uyarlama isteyebilir — eklentinin kendi dokümantasyonundaki örnek koda bakılmalı.
4. Test için Play Console'da "Lisans Test Kullanıcıları" ekleyip gerçek para harcamadan satın alma akışını deneyebilirsin.

## 10. Gelir Modeli — Reklamlar (AdMob)

Reklam eklemek istiyorsan bilmen gereken en önemli gerçek: **şu anki TWA paketiyle gerçek reklam gösteremezsin.** TWA sadece web sitenin bir pencere içinde açılmış halidir; içine native AdMob reklamı yerleştirilemez. Gerçek reklam için şu yola geçmen gerekiyor:

### TWA'dan Capacitor'e Geçiş
1. Bilgisayarına Node.js kur (nodejs.org).
2. Bu klasörde bir terminal aç, sırayla:
   ```
   npm init -y
   npm install @capacitor/core @capacitor/cli @capacitor/android
   npx cap init "Hayvan Dostları" "com.senintakma.hayvandostlari"
   ```
3. `index.html`, `manifest.json`, ikonları bir `www` klasörüne taşı, `capacitor.config.json` içinde `webDir: "www"` olarak ayarla.
4. `npx cap add android` — bu sana gerçek bir Android Studio projesi oluşturur.
5. AdMob eklentisini kur: `npm install @capacitor-community/admob` ve dokümantasyonundaki Android kurulum adımlarını (AndroidManifest.xml'e App ID ekleme vb.) uygula.
6. **AdMob hesabı aç** (admob.google.com), uygulamanı ekle, bir **Interstitial (geçiş reklamı)** birim ID'si oluştur.
7. Kod tarafında zaten bir kanca hazır: `maybeShowInterstitial()` fonksiyonu, her 10 soruda bir otomatik çağrılıyor. Bu fonksiyonun içine gerçek eklenti çağrını (`AdMob.prepareInterstitial(...)`, `AdMob.showInterstitial()`) yazman yeterli — mantığı ve tetiklenme noktası zaten kurulu.
8. `npx cap sync` ile değişiklikleri Android projesine aktar, Android Studio'da aç, çalıştır/derle.

### Çocuklara Yönelik Reklam Kuralları (çok önemli)
- AdMob hesabında uygulamanı **"Çocuklara Yönelik" (Tagged for Child-Directed Treatment / TFCD)** olarak işaretlemen **yasal bir zorunluluk** (ABD'de COPPA, AB'de GDPR-K).
- Bu işaretleme yapıldığında AdMob sadece **kişiselleştirilmemiş, bağlamsal** reklamlar gösterir — bu, reklam doluluk oranını ve geliri belirgin şekilde düşürür ama yasal ve Play Store politikalarına uygun kalmanı sağlar.
- Google Play Console'da "Hedef Kitle" bölümünde uygulamayı çocuklara yönelik işaretlersen, **Aile Politikaları** devreye girer: sadece Google'ın onayladığı SDK'lar (AdMob dahil, doğru ayarlarla) kullanılabilir.
- **Dürüst bir uyarı:** Çocuk odaklı reklamcılıkta gelir, yetişkin uygulamalarına göre genelde çok daha düşüktür (kişiselleştirme yok, doluluk oranı düşük). İlk aydan büyük gelir beklememeni öneririm — Premium (tek seferlik/uygulama içi satın alma) genelde çocuk uygulamalarında reklamdan daha güvenilir bir gelir kaynağı oluyor.

### Reklam mı, Premium mü, İkisi Birden mi?
Önerim: **Premium'u öncelikle yayınla** (zaten kodda hazır, ek native iş sadece Play Billing entegrasyonu). Reklamı, kullanıcı sayın arttıktan ve Capacitor'e geçmeye vaktin olduğunda ikinci adım olarak ekle. İkisini aynı anda yapmaya çalışmak ilk uygulama için işleri karmaşıklaştırır.

