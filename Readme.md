
# NYC Taxi Trip Duration - Random Forest & Lineer Regresyon

Bu proje, **New York City Taxi Trip Duration** veri seti kullanılarak yolculuk süresi tahminleri yapmak ve iki farklı makine öğrenmesi modelinin (Lineer Regresyon ve Random Forest) performanslarını karşılaştırmayı amaçlamaktadır.

## İçindekiler

-   [Proje Amacı](#proje-amac%C4%B1)
-   [Kullanılan Veri Seti](#kullan%C4%B1lan-veri-seti)
-   [Kütüphaneler ve Bağımlılıklar](#k%C3%BCt%C3%BCphaneler-ve-ba%C4%9F%C4%B1ml%C4%B1l%C4%B1klar)
-   [Veri Ön İşleme](#veri-%C3%B6n-i%C5%9Fleme)
-   [Özellik Mühendisliği](#%C3%B6zellik-m%C3%BChendisli%C4%9Fi)
-   [Modellerin Eğitimi ve Performans Değerlendirmesi](#modellerin-e%C4%9Fitimi-ve-performans-de%C4%9Ferlendirmesi)
-   [Sonuçlar](#sonu%C3%A7lar)

## Proje Amacı

Bu projede, New York şehri içerisindeki taksi yolculuklarının sürelerini tahmin etmek için iki farklı makine öğrenmesi modeli kullanılmaktadır: **Lineer Regresyon** ve **Random Forest**. Projenin amacı, bu iki modelin performanslarını karşılaştırarak hangi modelin daha doğru tahminler ürettiğini analiz etmektir.

## Kullanılan Veri Seti

Veri seti, Kaggle tarafından sağlanan **New York City Taxi Trip Duration** veri setidir. Bu veri seti, New York'taki taksi yolculuklarının başlangıç ve bitiş zamanları, yolculuk mesafeleri, coğrafi konumlar ve çeşitli ek bilgileri içerir.

Veri setine buradan ulaşabilirsiniz.

### Veri Setindeki Sütunlar:

-   **id**: Yolculuğun kimliği.
-   **vendor_id**: Taksi şirketi.
-   **pickup_datetime**: Yolculuğun başlangıç zamanı.
-   **dropoff_datetime**: Yolculuğun bitiş zamanı.
-   **passenger_count**: Yolcu sayısı.
-   **pickup_longitude**: Başlangıç konumunun boylamı.
-   **pickup_latitude**: Başlangıç konumunun enlemi.
-   **dropoff_longitude**: Bitiş konumunun boylamı.
-   **dropoff_latitude**: Bitiş konumunun enlemi.
-   **store_and_fwd_flag**: Yolculuk verisinin gerçek zamanlı olarak iletilip iletilmediği.
-   **trip_duration**: Yolculuğun süresi (saniye).

## Kütüphaneler ve Bağımlılıklar

Bu proje aşağıdaki Python kütüphanelerini kullanmaktadır:

-   `pandas`: Veri işleme ve manipülasyon.
-   `numpy`: Sayısal işlemler.
-   `matplotlib`: Veri görselleştirme.
-   `sklearn`: Makine öğrenmesi modelleri ve metrikleri.

## Veri Ön İşleme

Veri setinde, eksik verilerin olup olmadığını kontrol ettikten sonra tarih ve saat bilgilerini kullanarak yeni özellikler oluşturduk. Ayrıca, yolculuk mesafesi gibi özelliklerin eksiksiz olduğundan emin olduk.

### Adımlar:

1.  **Veri Setinin Yüklenmesi:**
    
    -   Veri seti Kaggle platformundan alınmış olup `pandas` ile yüklenmiştir.
-   `import pandas as pd`
    `data = pd.read_csv('nyc_taxi_trip_duration.csv')`    
2.  **Eksik Verilerin Kontrolü:**
    
    -   Veri setindeki eksik veriler kontrol edilmiştir ve eksik değer içermemektedir.
  
    
    `print(data.isnull().sum())` 
    
3.  **Tarih ve Saat Özelliklerinin Ayrıştırılması:**
    -   `pickup_datetime` sütunu saat dilimlerine ayrılarak yolculuğun hangi saatte başladığına dair yeni özellikler oluşturulmuştur.
    
    `data['pickup_datetime'] = pd.to_datetime(data['pickup_datetime'])
    data['pickup_hour'] = data['pickup_datetime'].dt.hour` 
    

## Özellik Mühendisliği

Proje boyunca tahmin gücünü artırmak için aşağıdaki yeni özellikler oluşturulmuştur:
Burada günleri belirledikten sonra, yoğun saatleri belirledim. Sabah ve akşam trafik saatlerini bir zaman aralığı olarak ifade edildi.  Bu veri setinde ayrıca "trip_distance" adlı bir değişken normalde bulunmamaktaydı. Bunu veri setindeki koordinatları bir fonkisyon aracılığıyla mesafeye dönüştürdüm ve bunu veri setine bir sütun olarak ekledim. 

-   **pickup_hour**: Yolculuğun başladığı saat.
-   **pickup_day_of_week**: Yolculuğun başladığı gün (0 = Pazartesi, 6 = Pazar).
-   **is_weekend**: Hafta içi veya hafta sonu olup olmadığı.
-   **is_rush_hour**: Yoğun saat (7-9 ve 16-19 saat dilimleri) olup olmadığı.

### Örnek Kod:
`data['is_rush_hour'] = data['pickup_hour'].apply(lambda x: 1 if (7 <= x <= 9 or 16 <= x <= 19) else 0)`
`data['is_weekend'] = data['pickup_datetime'].dt.weekday.apply(lambda x: 1 if x >= 5 else 0)` 

## Modellerin Eğitimi ve Performans Değerlendirmesi

Projede iki farklı makine öğrenmesi modeli eğitilmiştir:

### 1. Lineer Regresyon

Lineer Regresyon modeli, veriler arasındaki doğrusal ilişkiyi modelleyerek yolculuk süresini tahmin etmeye çalışır. Aşağıda Lineer Regresyon modeli ile ilgili performans metrikleri yer almaktadır.

-   **MAE** (Ortalama Mutlak Hata): 452.76 saniye
-   **RMSE** (Kök Ortalama Kare Hatası): 3190.53 saniye

### 2. Random Forest Regressor

Random Forest, birden fazla karar ağacının ortalamasını alarak daha doğru tahminler yapmaya çalışır. Daha karmaşık ilişkileri yakalayabildiği için bu model genellikle daha iyi sonuçlar verir.

-   **MAE** (Ortalama Mutlak Hata): 320.96 saniye
-   **RMSE** (Kök Ortalama Kare Hatası): 3528.12 saniye

### Çapraz Doğrulama

Normalde öncelikle bir Cross Validation yaptım fakat aşırı uzun sürdüğü için veri setinin %10 ile iş yapmak istedim ve 3 katlı bir işlem yaptım. Ve n_estimators değişkenini 100'den 50'ye indirdim. Normalde önceki yaptığım işlem 5 katlı idi ve veri setinin hepsi ile yaptım. Fakat bu işşlem yaklaşık olarak 30 dkyi geçti ve halen yapmaya devam ediyordu. Onun için bu çapraz sorgulama ve model değerlendirmesini Ver isetinin ufak bir kısmı ile yapmak zorunda kaldım.

## Sonuçlar

Lineer Regresyon modelim için Mean Absolute Error(MAE) kullandım. Bu değer, modelimin tahminlerinin gerçek değerlerden ne kdaar sapma gösterdiğinin ortalmasıdır. Yolculuk süresinin kaç saniye sapabileceğini ortalama olarak göstermeye yarar. Lineer Regresyon için  **452.76 saniye**, Random Forest için  **320.96**  saniye olarak değer aldım.

Buradaki değerlere göre Random Forest modelinin tahminlerinin Lineer Regresyon modelinden daha doğru olduğunu söyleyebilirim çünkü MAE daha düşük gösteriyor.

Random Forest modelim için Root Mean Squared Error(RMSE) kullandım. Bu değer modelin hatalarının karelerinin ortalamasının kareköküdür. RMSE, büyük hatalara daha duyarlı ve daha yüksek bir sapma gösteren tahminlerde ona göre daha fazla ceza verir. Lineer Regrezyon için  **3190.53**  saniye, Random Forest için  **3528.12**  saniye olarak değer aldım.

RMSE, değerinde sise Random Forest Modelinin hata karelerinin ortalaması Lineer Regresyona göre daha yüksek çıkmış, bu da büyük sapmaların daha fazla olduğunu gösteriyor.

Projemde, yoğun saatler ve iş saatlerine bağlı olarak yolculuk süresini tahmin etmeye çalıştım. Random Forest daha iyi sonuç verebileceğini düşündüğüm çünkü saat dilimleri ve trafik yoğunluğu gibi karmaşık ilişkileri daha iyi yakalayabileceğini düşündüğüm için onu ve Lineer Regresyon modelini karşılaştırmak istedim.






# New York City Taxi Trip Duration Analizi - K-Means Kümeleme

Bu proje, **New York City Taxi Trip Duration** veri seti üzerinde **K-Means Kümeleme** algoritması kullanılarak yapılan bir kümeleme analizini kapsamaktadır. Projenin amacı, NYC taksi yolculuklarının özelliklerini analiz ederek, yolculukların yoğun saatlerini ve kümeleme yöntemi ile bu yolculukların örüntülerini belirlemektir.

## İçindekiler

1.  [Projenin Amacı](#projenin-amac%C4%B1)
2.  [Veri Seti Hakkında](#veri-seti-hakk%C4%B1nda)
3.  [Veri Setinin Hazırlanması](#veri-setinin-haz%C4%B1rlanmas%C4%B1)
4.  [Veri Görselleştirme](#veri-g%C3%B6rselle%C5%9Ftirme)
5.  [K-Means Kümeleme](#k-means-k%C3%BCmeleme)
    -   [K-Means Algoritmasının Özeti](#k-means-algoritmas%C4%B1n%C4%B1n-%C3%B6zeti)
    -   [Veri Hazırlığı ve Model Eğitimi](#veri-haz%C4%B1rl%C4%B1%C4%9F%C4%B1-ve-model-e%C4%9Fitimi)
    -   [Kümeleme Sonuçları ve Yorumlama](#k%C3%BCmeleme-sonu%C3%A7lar%C4%B1-ve-yorumlama)
6.  [Sonuçlar ve Çıkarımlar](#sonu%C3%A7lar-ve-%C3%A7%C4%B1kar%C4%B1mlar)
7.  [Gelecekteki İyileştirme Alanları](#gelecekteki-iyile%C5%9Ftirme-alanlar%C4%B1)

## Projenin Amacı

Bu projenin amacı, NYC taksi yolculuk süreleri üzerine yapılan bir K-Means kümeleme analizidir. K-Means algoritması kullanılarak, yolculukların kümelere ayrılması hedeflenmiştir. Bu sayede, belirli yolculuk sürelerine ve zaman dilimlerine göre yoğun saatlerin belirlenmesi ve iş saatleri ile yolculuk süresi arasındaki ilişkilerin keşfedilmesi amaçlanmaktadır.

## Veri Seti Hakkında

**NYC Taxi Trip Duration** veri seti, New York City’de gerçekleşen taksi yolculuklarına ait bilgileri içerir. Veri setinde her yolculuk için şu özellikler yer almaktadır:

-   **id**: Yolculuğun benzersiz kimliği
-   **vendor_id**: Taksi şirketi bilgisi
-   **pickup_datetime**: Yolculuğun başlama zamanı
-   **dropoff_datetime**: Yolculuğun bitiş zamanı
-   **passenger_count**: Yolcu sayısı
-   **pickup_longitude**: Yolculuğun başlangıç noktasının boylamı
-   **pickup_latitude**: Yolculuğun başlangıç noktasının enlemi
-   **dropoff_longitude**: Yolculuğun bitiş noktasının boylamı
-   **dropoff_latitude**: Yolculuğun bitiş noktasının enlemi
-   **trip_duration**: Yolculuğun süresi (saniye cinsinden)

Veri setinin büyüklüğü, 1.4 milyon satır ve 11 sütundan oluşmaktadır.

## Veri Setinin Hazırlanması

Veri seti üzerinde K-Means kümeleme algoritmasını uygulamadan önce veri temizliği ve ön işleme adımları gerçekleştirilmiştir.

### 1. Eksik Veri Kontrolü

Veri setinde eksik veri olup olmadığını kontrol ettik. Veri setinde eksik veriler bulunmadığından, veri temizleme işlemine gerek duyulmadı.

### 2. Aykırı Değerlerin Tespiti ve Çıkarılması

GPT yardımıyla New York City sınırlarının tahmini koordinatlarını aldım. Bu sınırlar dışındaki noktaları bulmak ve anormal koordinatları ayıklamak gerekebilir. Şehir sınırlarına uymayan noktalar hatalı olabilir ya da bazı sürüşler şehir dışına doğru olabilir bu çok normal bir şey ama yine de veri setinden çıkartalım.
Örnek kod: 
    `nyc_min_longitude, nyc_max_longitude = -74.05, -73.75`
`nyc_min_latitude, nyc_max_latitude = 40.63, 40.85`
`anormal_veriler = df[`
  `  (df['pickup_longitude'] < nyc_min_longitude) | `
  `  (df['pickup_longitude'] > nyc_max_longitude) |`
  `  (df['pickup_latitude'] < nyc_min_latitude) | `
  `  (df['pickup_latitude'] > nyc_max_latitude)`
`]`
`print(f"Anormal koordinatlara sahip satır sayısı: {anormal_veriler.shape[0]}")`

` Temiz veriyi filtreleme`
`df_cleaned = df[`
  `  (df['pickup_longitude'] >= nyc_min_longitude) & `
 `   (df['pickup_longitude'] <= nyc_max_longitude) &`
  `  (df['pickup_latitude'] >= nyc_min_latitude) & `
   ` (df['pickup_latitude'] <= nyc_max_latitude)`
`]`



### 3. Coğrafi Konum Verilerinin Hazırlanması

Veri setindeki enlem ve boylam bilgilerini kullanarak, yolculukların başladığı ve bittiği noktalar arasındaki mesafe hesaplandı. Bu mesafe, kümeleme analizinde önemli bir değişken olarak kullanıldı.

## Veri Görselleştirme

Pickup ve dropoff koordinatlarını harita üzerinde incelemek istedim. Fakat buradaki veriler koordinat olduğu için hepsinin birbirine yakın olması ve grafikte bir yerde toplanmaları aşırı normal. Onun için değer aralıklarını küçülttüm. 

## K-Means Kümeleme

### K-Means Algoritmasının Özeti

K-Means algoritması, verileri önceden belirlenmiş sayıda kümeye ayıran bir kümeleme algoritmasıdır. Bu projede, K-Means algoritması kullanılarak benzer yolculukların kümelenmesi hedeflenmiştir. Yolculuk süreleri, başlangıç ve bitiş noktaları, yolculuk zamanı gibi değişkenler göz önünde bulundurularak veriler analiz edilmiştir.

### Veri Hazırlığı ve Model Eğitimi

Verilerimi görselleştirdiğime göre K-Means için verilerimi hazırlıyorum. K-Means için sadece pickup_longitude, pickup_latitude, dropoff_longitude, ve dropoff_latitude sütunlarını kullanacağım. Bunun dışındaki verilere şimdilik ihtiyacım yok.

Küme sayısını belirlemek için Elbow yöntemini kullanacağım. Ondan sonra uygun küme sayısı ile K-Means kümeleme işlemini yapacağım.
-   **Trip Duration**: Yolculuk süresi
-   **Pickup Latitude/Longitude**: Yolculuğun başladığı coğrafi koordinatlar
-   **Dropoff Latitude/Longitude**: Yolculuğun bittiği coğrafi koordinatlar
-   **Pickup Hour**: Yolculuğun başladığı saat dilimi

**Elbow yönteminde**, her küme sayısına karşılık gelen toplam hata kareler toplamı hesaplanır. Bu, her bir veri noktasının kendi küme merkezine olan uzaklıklarının karelerinin toplamını ifade eder. Küme sayısı arttıkça, bu değer azalma eğilimindedir.

Dirsek noktasının seçimi: Elbow yönteminde dikkat edilmesi gereken en önemli şey, grafik üzerinde belirgin bir dirsek noktasını bulmaktır. Bu nokta, küme sayısının arttırılmasının ek fayda sağlamadığı, yani SSE'nin çok az bir şekilde azaldığı noktadır. Bu noktadan sonra daha fazla küme eklemek, modelin karmaşıklığını arttırsa da anlamlı bir iyileşme sağlamaz. Çok fazla küme seçilirse, veri aşırı kümelenebilir ve gereksiz bölümlenme olur . Az sayıda küme seçilirse ise verinin doğal yapısı yeterince yakalanmamış olabilir. Dirsek noktası bu iki durumu dengelemeye çalışır.



### Kümeleme Sonuçları ve Yorumlama

Model eğitildikten sonra, her bir yolculuk için hangi kümeye ait olduğu belirlendi. Sonuçlar incelendiğinde:

-   Belirli kümelerde yoğunlaşan yolculuk süreleri ve başlangıç saatlerinin diğer kümelerden anlamlı şekilde farklılaştığı gözlemlendi.
-   Belirli coğrafi bölgelerde (örneğin, Manhattan’ın merkezi) yolculukların daha kısa sürdüğü, diğer bölgelerde ise daha uzun yolculukların olduğu tespit edildi.
-   Sabah ve akşam yoğun saatlerdeki yolculuklar ayrı kümelerde toplanırken, gece yarısı yapılan yolculukların başka bir kümede yer aldığı gözlemlendi.

Bu sonuçlar, taksi hizmetlerinin özellikle hangi saatlerde ve bölgelerde yoğunlaştığını, hangi yolculukların daha uzun sürdüğünü ortaya koydu.

## Sonuçlar ve Çıkarımlar


Bu projede, New York City Taxi Trip Duration veri setini kullanarak K-Means algoritması ile yolculukların pickup (başlangıç) ve dropoff (bitiş) lokasyonlarına göre kümelenmesi amaçlandı. Bu kümeleme işlemi ile şehirdeki yolculukların farklı bölgelerden nasıl organize olduğunu ve hangi bölgelerin yoğun trafik akışına sahip olduğunu analiz etmeye çalıştık.

#### 1. **EDA (Keşifsel Veri Analizi) Sonuçları**

Öncelikle veri setini inceledik ve New York City'deki yolculukların koordinatlarını haritalandırdık. Verilerin çoğunluğu New York City ve çevresinde yoğunlaşmışken, bazı uç değerler ve şehir sınırlarının çok dışındaki koordinatlar veri temizleme işlemi gerektirdi. Bu aşamada, ekstrem noktaları filtreleyerek veriyi daha güvenilir hale getirdik. Ayrıca, veri setindeki eksik değerler, anormal koordinatlar ve veri dağılımını inceleyerek bir veri temizliği süreci uyguladık.

#### 2. **Kümeleme İşlemi ve Parametreler**

EDA sonrası K-Means algoritmasını kullanarak yolculukları benzerliklerine göre kümelere ayırdık. Pickup ve dropoff noktaları için longitude ve latitude verilerini kullanarak K-Means algoritmasını çalıştırdık. Küme sayısını belirlemek için **Elbow yöntemi** kullandık ve 5 kümenin optimal olduğunu tespit ettik. Ardından, her yolculuk için bu kümelere atama yaptık ve kümeleme sonuçlarını harita üzerinde görselleştirdik.

#### 3. **Sonuçların Analizi**

K-Means algoritması sonucunda her bir yolculuk, koordinatlara göre bir kümeye atandı. Her kümenin özelliklerini analiz ederek şu sonuçlara ulaştık:

-   **Küme 0**: Bu küme, şehir merkezinde (Manhattan) başlayan ve biten yolculukları içeriyor. Küme 0, yoğun trafik akışına sahip olan bölgeleri temsil ediyor. Bu bölgedeki yolculuklar genellikle kısa mesafeli ve ortalama sürelere sahip.
    
-   **Küme 1**: Şehir merkezine daha uzak olan yerlerden (Brooklyn, Queens) başlayan yolculuklar bu kümeye dahil oldu. Yolculuk süreleri ve mesafeleri diğer kümelere göre daha uzun. Bu bölge, genellikle banliyöler ve daha uzak yerler için kullanılıyor.
    
-   **Küme 2**: Havalimanı gibi stratejik noktalarda başlayan ve biten yolculuklar bu kümeye ait. Bu kümeler, özellikle havalimanı taşımacılığı ile ilgili olup, genellikle şehir dışına veya merkeze olan uzun mesafeli yolculukları kapsar.
    
-   **Küme 3**: Şehrin batı tarafında (Hudson Nehri yakınlarında) başlayan yolculukları içeriyor. Yolculuk süresi bakımından daha kısa ama belirli bölgelere odaklanmış bir küme.
    
-   **Küme 4**: Kuzey bölgesinde (Bronx ve Upper Manhattan) yoğunlaşan bir küme. Bu küme, şehir merkezine kıyasla daha sakin bölgelerdeki yolculukları kapsıyor.
    

#### 4. **Kümeleme Kalitesi ve Değerlendirme**

Kümeleme sonuçlarını değerlendirirken ve **Davies-Bouldin İndeksi** gibi metrikler kullandık:

-
    
-   **Davies-Bouldin İndeksi**: Bu metrik, küme içi benzerliklerin ne kadar iyi olduğunu ve kümelerin birbirlerinden ne kadar farklı olduğunu ölçer. Skor ne kadar düşükse, model o kadar başarılıdır. Bu projede elde edilen Davies-Bouldin skoru **1.08** civarındaydı. Bu da kümelerin makul bir ayrışma seviyesine sahip olduğunu, ancak bazı kümelerin birbiriyle benzer olduğunu gösteriyor.
    

#### 5. **Küme Boyutları ve Yoğunluk Analizi**

Her bir kümenin boyutlarına baktığımızda:

-   **Küme 0 (Manhattan)** en büyük kümeydi ve en yoğun trafik bu kümede gözlemlendi.
-   **Küme 1 (Brooklyn/Queens)** ise daha geniş bir alana yayılmış, ama daha az sayıda yolculuğa sahipti.

Bu sonuçlar, New York City'deki yolculukların büyük çoğunluğunun şehir merkezinde yoğunlaştığını, şehir dışına doğru gidildikçe yolculuk sayısının azaldığını gösterdi.

#### 6. **K-Means’in Bu Veri Seti İçin Uygunluğu**

K-Means algoritması, bu proje için verimli bir yöntem olarak kullanıldı. Özellikle yolculukların coğrafi konumlarına göre kümelenmesi için uygun bir algoritma oldu çünkü:

-   **Koordinat Verileri**: Longitude ve latitude gibi sürekli değişkenlerle çalışmak K-Means için idealdir. Algoritma, yolculukların coğrafi dağılımlarına göre anlamlı kümeler oluşturdu.

Ancak bazı zorluklar da gözlemlendi:

-   **Küme Sayısı**: Küme sayısı veri yapısına bağlı olarak seçildi, ancak farklı kümeleme metrikleri farklı sonuçlar verebilir. Elbow yöntemi ile belirlenen 5 küme, veri için makul bir seçimdi, fakat daha detaylı analiz için başka küme sayıları da denenebilirdi.
    
-   **Homojen Olmayan Kümeler**: Bazı kümeler, birbirine coğrafi olarak yakın bölgeleri içerebilir. Bu durumda, K-Means’in belirlediği kümeler yeterince farklı olmayabilir. Özellikle büyük bir şehirdeki heterojen yapı, daha sofistike kümeleme yöntemleri gerektirebilir.
    

### Sonuç:

-   **K-Means**, New York City gibi coğrafi verilerle çalışmak için uygun bir algoritma oldu. Özellikle şehir içi yolculukların lokasyon bazlı gruplandırılmasında başarılı sonuçlar verdi.
-   **Eksiklikler**: Ancak, sadece koordinat verilerine dayalı olduğu için trafik durumu, yolculuk zamanı gibi diğer değişkenler dikkate alınmadı. Bu tür ek bilgiler dahil edilseydi, daha zengin ve anlamlı kümeler elde edilebilirdi.

Genel olarak, K-Means bu veri seti için uygundur, ancak yolculuk süreleri, yoğun saatler ve trafik durumu gibi diğer faktörlerle desteklendiğinde daha anlamlı sonuçlar elde edilebilir.e toplandığı gözlemlendi.
