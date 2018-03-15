---
title: BuildConfig dodanie własnych stałych w konfiguracji Gradle
layout: post
comments: true
categories:
- gradle
- tip
---

Podczas budowania aplikacji za pomocą Gradla tworzona jest klasa pt. BuildConfig. Domyślnie zawiera ona kilka statycznych pół, których można użyć w kodzie.

#### Przykład klasy:

{% highlight java %}
public final class BuildConfig {
  public static final boolean DEBUG = Boolean.parseBoolean("true");
  public static final String APPLICATION_ID = "com.mateusz.grabarski.myshoppinglist";
  public static final String BUILD_TYPE = "debug";
  public static final String FLAVOR = "";
  public static final int VERSION_CODE = 1;
  public static final String VERSION_NAME = "1.0";
}
{% endhighlight %}

Klasa ta jest dostępna dla nas cały czas i można to wykorzystać dodając swoje własne wartości, które będą wpływać na działanie aplikacji. Może to np. URL do api, klucz do api itd. Ogranicza nas jedynie fakt, że muszą to być typy proste.

### Przykład

Załóżmy, że chcemy w naszej aplikacji zahardkodować datę kiedy aplikacja została wygenerowana. Oczywiście możemy sobie takie wartości zapisać w klasie pt. Constants i w niej trzymać takie wartości, ale możemy zaciągnąć do działania system budowania Gradle.

W defaultConfig  gdzie są zawarte podstawowe informacje odnośnie aplikacji stworzę własną stałą, która będzie do klasy BuildConfig dodawać pole z datą budowania.

Składania stałej:
buildConfigField "typ", "NAZWA_STAŁEJ", "WARTOŚĆ"

{% highlight java %}
defaultConfig {
        applicationId "com.mateusz.grabarski.myshoppinglist"
        minSdkVersion 21
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"

        // pole typu long, które będzie generować datę budowania
        buildConfigField "long", "BUILD_DATE", System.currentTimeMillis() + "L"
    }
{% endhighlight %}

Po ponownym zbudowaniu aplikacji w klasie BuildConfig zostanie dodana nowa stała.

{% highlight java %}
public final class BuildConfig {
  public static final boolean DEBUG = Boolean.parseBoolean("true");
  public static final String APPLICATION_ID = "com.mateusz.grabarski.myshoppinglist";
  public static final String BUILD_TYPE = "debug";
  public static final String FLAVOR = "";
  public static final int VERSION_CODE = 1;
  public static final String VERSION_NAME = "1.0";

  // Fields from default config.
  public static final long BUILD_DATE = 1519828511598L;
}
{% endhighlight %}

To pozwala już dowolnie używać tej wartości. Należy pamiętać, że stałe definiowane w defaultConfig są niezmienne dla wszystkich rodzajów generowanych aplikacji. Używając tego samego mechanizmy mogę manipulować stałymi wartościami w zależności od rodzaju np. płatna, darmowa.

### productFlavors

Za nim aplikacja zostanie udostępniona użytkownikom końcowym należy ją przetestować i sprawdzić czy wszystko działa. Najlepiej gdyby aplikacja była testowana w zamkniętym środowisku.
Sporo aplikacji podczas działania komunikuje się z zewnętrznym api i najczęściej jest ono podzielone na wersje debugową oraz produkcyjną aby nie mieszać danych.

Przykład naszych URLi do api:
* "produkcyjne api"
* "debug api"

Używając productFlavors będzie to wyglądało w następujący sposób:

{% highlight java %}
productFlavors {
        dev {
            buildConfigField "String", "API_URL", "\"debug api\""
        }

        live {
            buildConfigField "String", "API_URL", "\"produkcyjne api\""
        }
    }
{% endhighlight %}

![](/assets/buildVariant.png)

Teraz w zależności od wybranego builda wartość stałej pt. "API_URL" będzie generowana z uwzględnieniem productFlavors. Klasa BuildConfig po przebudowaniu projektu będzie wyglądać następująco:

{% highlight java %}
public final class BuildConfig {
  public static final boolean DEBUG = Boolean.parseBoolean("true");
  public static final String APPLICATION_ID = "com.mateusz.grabarski.myshoppinglist";
  public static final String BUILD_TYPE = "debug";
  public static final String FLAVOR = "dev";
  public static final int VERSION_CODE = 1;
  public static final String VERSION_NAME = "1.0";
  // Fields from product flavor: dev
  public static final String API_URL = "debug api";
  // Fields from default config.
  public static final long BUILD_DATE = 1519997220702L;
}
{% endhighlight %}

### Problem

Stałe definiowane po przez buildConfigField nie są dostępne z poziomu plików xml.
Tutaj jest podobnie jak z buildConfigField tylko, że słowo kluczowe to "resValue".

Składnia jest identyczna:

resValue "typ", "nazwa", "wartość"

![](/assets/resValue.png)

Dla przykładu użyję nazwy aplikacji tak aby dla wersji debug była dopisana nazwa " - dev". Pozwala to na odróżnienie wersji z jaką ma się doczynienia.

Dopiszmy resValue dla nazwy aplikacji:

{% highlight java %}
productFlavors {
        dev {
            buildConfigField "String", "API_URL", "\"debug api\""
            resValue 'string', 'app_name', 'Moja aplikacja - dev'
        }

        live {
            buildConfigField "String", "API_URL", "\"produkcyjne api\""
            resValue 'string', 'app_name', 'Moja aplikacja'
        }
    }
{% endhighlight %}

Po przebudowaniu aplikacji mogę używać nowego zasobu dowolnie w kodzie jak i w plikach xml ("@string/app_name");


### Podsumowanie

Moim zdaniem warto korzystać z wyżej opisanych stałych. Daje nam to gwaracji, że w zależności od rodzaju wersji aplikacji nasze url, nazwy itd. będą odpowiednio wygenerowane i używane.

{% if page.comments %}

{% endif %}
