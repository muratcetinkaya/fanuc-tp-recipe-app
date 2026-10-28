# FANUC Reçete Yöneticisi (KAREL + XML + Web)

🌐 [English](README.md) | **Türkçe** | [Deutsch](README.de.md)

**FANUC robot kontrolcüsünde reçete depolama ve yönetimi** için minimal, genel bir örnek.
Sadece dahili özellikler kullanılıyor: KAREL, kontrolcü üzerindeki XML dosyaları (FR:), register'lar ve Web Server sayfası (iPendant / tarayıcı).

Buradaki bir reçete sadece **isim + 3 sayıdan** oluşur (kutu uzunluğu, genişliği, yüksekliği) – istediğin kadar alan ekleyebilirsin.

## Nasıl çalışır

```
 Web sayfası (recipe_hmi.stm)
   |  combo box  -> SR[1]   (seçili dosya, örn. "box_small.xml")
   |  edit kutuları -> SR[2], R[11..13]   (EDIT tarafı)
   v
 recipe_save.kl   SAVE / CREATE / DELETE   -> FR:\recipes\<isim>.xml
 recipe_load.kl   VIEW (-> edit register'ları)  /  LOAD (-> aktif register'lar SR[3], R[1..3])
```

| Buton | Program | Ne yapar |
|---|---|---|
| New Recipe | recipe_save | edit alanlarını temizler |
| Save Recipe | recipe_save | SR[2] + R[11..13] değerlerini XML dosyasına yazar |
| View Recipe | recipe_load | seçili XML'i edit alanlarına okur |
| Load Recipe | recipe_load | seçili XML'i robot programının kullandığı **aktif** register'lara okur |
| Delete Recipe | recipe_save | seçili XML dosyasını siler |

Edit / Aktif ayrımı sayesinde robot yüklü reçeteyle çalışmaya devam ederken diğer reçetelere bakabilir veya onları değiştirebilirsin.

## Register haritası

| Register | Anlamı |
|---|---|
| SR[1] | seçili dosya (combo box) |
| SR[2] / SR[3] | reçete adı – edit / aktif |
| R[11..13] | uzunluk, genişlik, yükseklik – edit |
| R[1..3] | uzunluk, genişlik, yükseklik – aktif (TP/KAREL programında bunları kullan) |

## Kurulum
1. **Önce KAREL'i etkinleştir: sistem değişkenlerinde `$KAREL_ENB` değerini `0`'dan `1`'e çek.** Bu yapılmazsa KAREL programları çalışmaz.
2. `recipe_save.kl` ve `recipe_load.kl` dosyalarını translate et (ROBOGUIDE veya `ktrans`) ve `.pc` dosyalarını kontrolcüye yükle.
3. Web sayfası `recipe_hmi.stm` dosyasını kontrolcüye kopyala. **FANUC web sayfaları `.stm` uzantılı olmak zorundadır.**
4. **Sayfanın dizinini `$TX_SCREEN` sistem değişkeninde tanımla.** `recipe_hmi.stm` dosyasının bulunduğu cihaz/dizin `$TX_SCREEN` içinde belirtilmelidir, aksi halde kontrolcünün web sunucusu sayfayı bulamaz.
5. `FR:\recipes\` klasörünü oluştur (istersen `example/box_small.xml` dosyasını kopyala).
6. KAREL programlarının web'den çağrılmasına izin ver (Setup > Host Comm / HTTP authentication, KAREL CGI etkin).
7. Sayfayı iki şekilde açabilirsin:
   - **Pendant üzerinden:** iPendant'taki **Browser** menüsünü aç ve sayfayı `$TX_SCREEN` içinde verdiğin **isimle** seç.
   - **PC tarayıcısından:** `$TX_SCREEN` içinde ayarladığın dizin üzerinden aç: `http://<robot-ip>/<ayarlanan-dizin>/recipe_hmi.stm`

## Notlar
- Değerler integer (mm) olarak kaydedilir. Ondalık gerekiyorsa x10 veya x100 olarak sakla.
- `TPOUT[5] = FALSE` bir "robot meşgul" kilidi olarak kullanılıyor, böylece döngü ortasında reçete değiştirilemez – kendi sinyaline göre uyarla.
- XML, `XML_ADDTAG` / `XML_SCAN` / `XML_GETDATA` ile her seferinde tek bir tag okunarak işlenir.

## Yazar
**Muratcan Cetinkaya**
Mail: mcetinkaya40@gmail.com
Web: https://www.muratcancetinkaya.net
