# Kairos Kullanımı

## Kütüphanenin projeye dahil edilmesi

Projenin build.gradle dosyasında aşağıdaki maven reposu eklenmeli

```gradle
allprojects {
   repositories {
       google()
       jcenter()
       maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/kairos-android-sdk/master/' }
  }
}
```

Uygulamanın build.gradle dosyasında aşağıdaki bağımlılık eklenmeli

```gradle
implementation 'kairos:kairos-core:1.0.1’
```

## Kütüphanenin kullanımı
Uygulamanızın açılış activity sınıfının onCreate methodu içerisinde Kairos kütüphanesi başlatılır.

```kotlin
Kairos.initKairosWith(
   application = application,
   activity = this,
   apiKey = "KAIROS_API_KEY",
   environment = KairosEnvironment.SANDBOX,
   deviceId = androidId,
   countryCode = KairosCountry.SAUDI_ARABIA,
   languageCode = KairosLanguage.ARABIC,
   onKairosReadyFunc: () -> Unit,
   onKairosErrorFunc: (error: String) -> Unit
)
```



| Parametreler | |
| ----- | ----- |
| application | Uygulammanın application instance’ı |
| activity | Kairosun başlatıldığı activity’nin instance’ı |
| apiKey | Kairos’a ait api key |
| environment | Kairos’un çalıştığı environment. Test ortamında SANDBOX, canlı ortamda PRODUCTION kullanılmalıdır |
| countryCode | Kullanıcının ülkesine ait 2 karakterli ISO kodu,  KairosCountry sınıfı altında değerlere ulaşılabilir |
| languageCode | Kullanıcının dil bilgisine  ait 2 karaterli ISO kodu, KairosLanguage sınıfı altında değerlere ulaşılaiblir. |
| onKairosReadyFunc | Sayfa gönderilmeye hazır olduğunda çağrılacak lambda fonksiyonu |
| onKairosErrorFunc | Hata ile karşılaşıldığında çağrılacak lambda fonksiyonu |


Kairos gerekli ayarları sunucudan indirdikten sonra KairosEventBus aracılığı ile uygulama katmanına dönüş sağlar.

```kotlin
KairosEventBus.subscribe { event->
   when(event.type){
       event.KAIROS_READY -> {
           // KAIROS HAZIR
       }
   }
}
```

KAIROS_READY eventi geldiğinde, Kairos sayfa göstermeye hazırdır. Sayfa göstermek için;

```kotlin
Kairos.showLanding(
   activity = this,
   requestCode = 1000,
   actionKey = ActionKey.SETTINGS,
   errorFunc = { errorMessage ->  }
)
```


| Parametreler | |
| ------ | ------ |
| activity | showLanding methodunun çağrıldığı activity’nin instance’ı |
| requestCode | Kairos tarafından activity açılırken kullanılan requestCode’u |
| actionKeys | Sayfa gösterimi için kullanılacak action, APPLAUNCH, PREMIUM ve SETTINGS değerleri tanımlı gelmektedir. |
| errorFunc | Hata ile karşılaşıldığında çağrılacak lambda fonksiyonu |

## Kairos üzerinden satınalma işlemlerinin dinlenmesi

```kotlin
KairosEventBus.subscribe { event->
   when(event.type){
       event.SUBSCRIPTION_VALIDATION -> {
           // Satın alma doğrulaması yapıldı
           Kairos.subscriptionValidation.validationRequestSuccessful // Boolean, satın alma doğrulama isteği yapılmış ise true değil ise false
           Kairos.subscriptionValidation.purchaseValidated // Boolean, satın alma doğrulanmış ise true, değil ise false
           Kairos.subscriptionValidation.expirationDate // String, Abonelik bitiş tarihi
       }
       KairosEvent.DO_NOT_SHOW.value -> {
           // Kairos ayarları sayfanın açılmasına izin vermedi
       }
       KairosEvent.CANCELED.value -> {
           // Kullanıcı platforma ait satın alma ekranını açtıktan sonra sayfayı kapadı
       }
       KairosEvent.CLOSED.value -> {
           // Kullanıcı kairos ekranını satın almayı denemeden kapadı
       }
   }
}
```


## Kullanıcının sayfada tanımlanmış abonelik paketine erişimi olmamasının tespit edilmesi

Kairos.skuErrorListener fonksiyonu, Play Store istenilen abonelik bilgisini veremediği durumlarda çağrılacaktır. Bu fonksiyon ile kullanıcıya bir mesaj gönderilebilir.

## Restore edilecek abonelik bilgisinin bulunmamasının tespit edilmesi
Kairos.onNothingToRestore fonksiyonu, kullanıcının Play Store üzerinde aktif bir aboneliği bulunmaması ve restore işlemi yapılamayacağı durumda çağrılacaktır. Bu fonksiyon ile restore denemesi yapan kullanıcıya ilgili uyarı mesajı gösterilebilir
