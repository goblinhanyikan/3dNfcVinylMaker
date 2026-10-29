# Vinil Bardak Altlığı Oluşturucu

🇬🇧 [English version](README.md)

İndirmeden Kullanmak için:
https://goblinhanyikan.github.io/3dNfcVinylMaker/

Herhangi bir görseli, 3B yazıcıyla basılabilecek çok renkli bir bardak altlığı için STL dosyalarına dönüştüren, tarayıcı tabanlı bir araçtır. Altlık Ø100 mm × 3 mm boyutundadır; siyah bir kenarı, her iki yüzünde sizin görseliniz ve baskıyı duraklatarak NFC etiketi yerleştirebileceğiniz kapalı bir iç boşluğu vardır.

Her şey tarayıcınızda yerel olarak çalışır. Görseliniz hiçbir yere yüklenmez.

## Nasıl çalışır?

Renk işlem hattı, [goblinhanyikan/PNGtoSVG-Multicolor-Printing](https://github.com/goblinhanyikan/PNGtoSVG-Multicolor-Printing) (MIT lisansı) projesinin yöntemini izler. Bu proje o yöntemin yeniden yazılmış hâlidir, kodunun kopyası değildir.

1. **Kırpma ve düzleştirme.** Görsel kareye kırpılır (yakınlaştırma ve kaydırma çubukları), seçilen çözünürlüğe getirilir ve siyah zemin üzerine düzleştirilir. Bu yüzden saydam alanlar siyah basılır.
2. **Renk indirgeme.** Renkler, k-means++ kümeleme ile küçük bir palete (2–8 renk) indirgenir.
3. **Maske.** Her piksel en yakın palet rengine atanır ve her renk için bir ikili maske oluşur. Hafif bir çoğunluk filtresi tek başına kalan benekleri temizler.
4. **Ekstrüzyon.** Her maske dikdörtgenlere birleştirilir, kutular hâlinde yükseltilir ve ayrı bir STL olarak yazılır.
5. **Birleştirme.** Tüm parçalar aynı koordinat sistemini kullanır, bu yüzden dilimleyiciye birlikte aktarıldığında üst üste oturur.

### Altlığın yapısı (alttan üste)

| Z aralığı (mm) | İçerik |
|---|---|
| 0 – 0,6 | Ø80 içinde görsel katmanı (renkler), Ø80–Ø100 arası siyah kenar |
| 0,6 – 1,0 | Dolu siyah |
| 1,0 – 2,0 | Siyah duvarın içinde kapalı NFC boşluğu (varsayılan Ø25, 1 mm yükseklik) |
| 2,0 – 2,4 | Dolu siyah tavan |
| 2,4 – 3,0 | Ø80 içinde görsel katmanı (renkler), siyah kenar |

Alt yüzdeki görsel aynalanmıştır; altlık bir sayfa gibi çevrildiğinde doğru okunur.

## Nasıl kullanılır?

1. Uygulamayı açın. Dili değiştirmek için **TR / EN** düğmesini kullanın.
2. Bir görsel yükleyin. Varsayılan olarak örnek bir görsel yüklüdür.
3. Görselin önemli kısmı daire içinde kalacak şekilde yakınlaştırma ve kaydırmayı ayarlayın.
4. Renk sayısını seçin. Az renk daha güvenilir basılır; düz çizimler ve logolar en iyi sonucu verir.
5. İsteğe bağlı: **Kenar boyunca yazı ekle** kutusunu işaretleyin, ardından yazıyı, yazı tipini (veya kendi yazı tipinizi), rengi, boyutu, yerleşimi ve yazının hangi yüzlere basılacağını seçin.
6. **STL setini indir (.zip)** düğmesine tıklayın ve arşivi açın.

### Ayarlar

| Ayar | Varsayılan | Anlamı |
|---|---|---|
| Renk sayısı | 4 | Palet boyutu (2–8) |
| Çözünürlük | 400 px | Görsel boyunca piksel sayısı. Ø80 mm'de 400 px, piksel başına 0,2 mm eder |
| Benek temizleme | 1 | Çoğunluk filtresi geçiş sayısı |
| Görsel çapı | 80 mm | Görsel alanın çapı. Geri kalan siyah kenardır |
| NFC boşluğu çapı | 25 mm | Kapalı cebin çapı |
| Boşluk yüksekliği | 1 mm | Cebin yüksekliği; kalınlığın ortasına yerleştirilir |
| Görsel katman kalınlığı | 0,6 mm | Her yüzdeki renkli katmanın kalınlığı |

### Kenardaki yazı

- Yazı kenar katmanlarına gömülür, bu yüzden altlık 3 mm kalınlıkta kalır.
- Yerleşimler: **üstte ortalı**, **tüm halkaya yay** veya **halka boyunca tekrarla**.
- Yazı, kenarın her iki ucundan 0,8 mm içeride bir bant içinde yer alır. Kenar 4 mm'den darsa yazı devre dışı kalır.
- `text_<renk>.stl` olarak dışa aktarılır ve siyah kenarda ona uyan boşluklar bırakılır.

## Baskı

1. Tüm STL dosyalarını dilimleyiciye **birlikte** aktarın; konumlarını değiştirmeyin ve yeniden ölçeklendirmeyin.
2. Her dosyaya ayrı bir filament atayın: `base_black` siyah, her `color*` dosyası kendi rengi, `text_*` ise yazı rengi olur.
3. **NFC etiketini yerleştirin.** Uygulamada gösterilen Z yüksekliğinde (varsayılan 2,0 mm) bir duraklatma (veya filament değişimi) ekleyin. Burası boşluğun üstü, tavan basılmadan hemen öncesidir. Etiketi cebe düz şekilde yerleştirin ve baskıya devam edin.
4. Tavan 25 mm'lik bir açıklığı köprüler. Bu genellikle destek olmadan basılır, ancak önce bir deneme yapın.

İpuçları: Etiket sıkı oturuyorsa çevresinde 0,2 mm boşluk bırakın ve etiketin boşluk yüksekliğine sığacak kadar ince olduğundan emin olun.

## Renk sistemi

- **Palet:** RGB uzayında k-means++ kümeleme. Başlangıç merkezleri k-means++ ile seçilir, ardından yaklaşık 15.000 örneklenmiş piksel üzerinde 10 iyileştirme turu yapılır. Sonra her piksel, Öklid uzaklığına göre en yakın merkeze atanır. Bu, referans projenin "tam renk" modunda kullandığı yaklaşımın aynısıdır.
- **Renk başına bir STL:** referans projenin "3D baskı dostu" modundaki fikirle aynıdır. Her renk, kendi dosyasında eksiksiz bir katmandır; dosyalar boşluk kalmadan orijinal resmi yeniden oluşturur.
- **Siyah:** kenar ve gövde yapısal siyahtır. Görselin kendisinde siyah varsa palet renklerinden biri olur ve ona aynı filamenti atayabilirsiniz.
- **Uygulama teması:** mor vurgu (açık temada `#6d3fc4`, koyu temada `#8b5cf6`) ve hafif mor tonlu nötr bir arka plan. Sistemin açık veya koyu ayarını izler.

## Sınırlamalar

- Çıktı, referans aracın varsayılan modu gibi piksel basamaklıdır (dik açılı). Fotoğraflar poster gibi (posterize) çıkar.
- Yazı kenarları 0,1 mm piksel basamaklıdır; ince yazı tipleri ince çizgilerini kaybedebilir.
- Araç her dilimleyicide doğrulanmış değildir. Uzun bir baskıdan önce katman önizlemesini kontrol edin.

## Kaynak ve lisans

Renk yöntemi, goblinhanyikan'ın [PNGtoSVG-Multicolor-Printing](https://github.com/goblinhanyikan/PNGtoSVG-Multicolor-Printing) projesine (MIT) dayanır. Bu aracı yeniden dağıtırsanız özgün atıfı koruyun.
