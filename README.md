# Kairos Android SDK

Kairos is a microservice that makes it easy to manage landing page screens of your application.

## Requirements

- Min Sdk Level 21+
- Kotlin 1.3


## Installation

Add maven repo to project level gradle file

```gradle
allprojects {
   repositories {
       google()
       jcenter()
       maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/kairos-android-sdk/master/' }
  }
}
```
Add dependency to module level gradle file

```gradle
implementation 'kairos:kairos-core:1.0.1’
```

## Usage
Add following code on onCreate method of your starting activity

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



| Parameters | |
| ----- | ----- |
| application | Instance of application class |
| activity | Starting activity instancce |
| apiKey | Kairos Api Key |
| environment | Kairos environment. You should use SANDBOX for test, you should use PRODUCTION for prod |
| countryCode | 2-letter ISO Code for user’s country, KairosCountry class contains these codes. |
| languageCode | 2-letter ISO Code for user’s language. KairosLanguage class contains these codes. |
| onKairosReadyFunc | Lambda method called when paage ready to show |
| onKairosErrorFunc | Lambda method when an error occured |


After downloading the settings from the server, Kairos returns to the application layer via KairosEventBus.

```kotlin
KairosEventBus.subscribe { event->
   when(event.type){
       event.KAIROS_READY -> {
           // KAIROS HAZIR
       }
   }
}
```

When the KAIROS_READY event arrives, Kairos is ready to show the page.

```kotlin
Kairos.showLanding(
   activity = this,
   requestCode = 1000,
   actionKey = ActionKey.SETTINGS,
   errorFunc = { errorMessage ->  }
)
```


| Parameters | |
| ------ | ------ |
| activity | Instance of the activity where the showLanding method is called |
| requestCode | Request code used by Kairos when opening activity |
| actionKeys | Action to be used for page display, APPLAUNCH, PREMIUM and SETTINGS values are defined |
| errorFunc | Lambda method when an error occured  |

## Listen purchase processes from Kairos

```kotlin
KairosEventBus.subscribe { event->
   when(event.type){
       event.SUBSCRIPTION_VALIDATION -> {
           // Purchase verification done
           Kairos.subscriptionValidation.validationRequestSuccessful // Boolean It is true If purchase request has been completed otherwise false
           Kairos.subscriptionValidation.purchaseValidated //  Boolean, It is true if purchase has been approved
           Kairos.subscriptionValidation.expirationDate // String, Subscription expiration date
       }
       KairosEvent.DO_NOT_SHOW.value -> {
           // Kairos settings did not allow the page to open
       }
       KairosEvent.CANCELED.value -> {
           // User closed the page after opening the purchase screen
       }
       KairosEvent.CLOSED.value -> {
           // User closed the kairos screen without trying to buy it
       }
   }
}
```


## Detect if the subscription package not available to user

Kairos.skuErrorListener function is called when subscription info not found on Play Store. A warning message can be shown to user with this function.

## Detect if there is no subscription to restore

Kairos.onNothingToRestore function is called when there is no active subscription belogn to user on Play Store. A warning message can be shown to users who try to restore their subscriptions.gösterilebilir
