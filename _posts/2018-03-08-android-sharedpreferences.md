---
title: Android i SharedPreferences
layout: post
categories:
- Android
---

Android SDK dostarcza przydatną i bardzo lekką w użyciu klasę SharedPreferences. Służy ona do przechowywania danych jak w mapie czyli klucz-wartość. Wszystkie wartości są zapisywane w pliku, który znajduje się w wewnętrznym katalogu aplikacji (Internal Storage). Klasa ta ogranicza wpisywanie danych do typów prostych (boolean, float, int, long, String).

Dużą zaletą mechanizmu SharedPreferences jest możliwość używania wpisanych wartości po ponownym uruchomieniu aplikacji.

{% highlight java %}
SharedPreferences sharedPreferences = context.getSharedPreferences(PREFERENCES_NAME, MODE);
{% endhighlight %}

Pobrany z konteksu objekt SharedPreferences wymaga podania dwóch parametrów gdzie:
* PREFERENCES_NAME - jest to nazwa pliku, w którym będą przechowywane wartości
* MODE - jest to tryb.

Klasa SharedPreferences udostępnia kilka trybów pracy jak pokazuje to [dokumentacja](https://developer.android.com/reference/android/content/Context.html#getSharedPreferences(java.lang.String,%20int)). Używany jest w zasadzie tylko jeden tryb Context.MODE_PRIVATE. W tym trybie aplikacja tworzy plik gdzie są zapisywane wprowadzone wartości oraz ma dostęp do tego pliku tylko aplikacja, która go stworzyła.

## Tworzenie SharedPreferences

Do stworzenia obiektu SharedPreferences można wykorzystać dwóch metod:

#### Metoda 1
{% highlight java %}
SharedPreferences  settings = PreferenceManager.getDefaultSharedPreferences(context);
{% endhighlight %}

#### Metoda 2
{% highlight java %}
String PREFERENCES_NAME = "nazwaPliku";
SharedPreferences sharedPreferences = context.getSharedPreferences(PREFERENCES_NAME, Context.MODE_PRIVATE);
{% endhighlight %}

W zależności od wywołanej metody w katalogu aplikacji pojawi się jeden z plików, a w nim wartości, które będą wpisywane. Przykład na poniższym screenie:

![](/assets/sharedPreferencesFiles.png)

Nie przekreśla to możliwości stworzenia kilka plików z SharedPreferences w zależności od potrzeb. Wystarczy podać inną nazwę pliku i do niego wpisywać kolejne wartości.

## Zapisywanie wartości w SharedPreferences

Z obiektu SharedPreferences trzeba pobrać obiekt Editor. W obiekcie wywołać metodę put z konkretnym kluczem i wartością, a następnie to zatwierdzić. Schemat jest identyczny dla każdego typu wartości zapisywanej.

{% highlight java %}
SharedPreferences sharedPreferences =
	context.getSharedPreferences(PREFERENCES_NAME, Context.MODE_PRIVATE); // 1
Editor editor = sharedPreferences.edit(); // 2
editor.putString(KEY_CURRENT_LOGIN_EMAIL, "wartosc"); // 3
editor.apply(); // 4
{% endhighlight %}

Kolejne kroku:
1. Pobranie obiektu SharedPreferences
2. Pobranie obiektu Editor
3. Wpisanie wartości dla podanego klucza
4. Zatwierdzenie

## Pobieranie wartości z SharedPreferences

Pobieranie danych z SharedPreferences jest równie proste jak ich zapisywanie. Wystarczy pobrać z obiektu konkretny typ wartości oraz podać klucz dla wartości.

{% highlight java %}
SharedPreferences sharedPreferences =
	context.getSharedPreferences(PREFERENCES_NAME, Context.MODE_PRIVATE); // 1
String value =
	sharedPreferences.getString(KEY_CURRENT_LOGIN_EMAIL, "wartosc_domyslna"); // 2
{% endhighlight %}

Kolejne kroki:
1. Pobranie obiektu
2. Pobranie wartości dla klucza oraz podanie wartości domyślnej jeżeli taka jeszcze nie istnieje


## Przykład klasy do zarządzania SharedPreferences

<script src="https://gist.github.com/mgrabarski/c260438f78fb95abc9c2d2c64778ff84.js"></script>

## Nasłuchiwanie zmian w SharedPreferences

Aby zacząć nasłuchiwać zmian w trzeba podpiąć się jako listener wywołując metodę registerOnSharedPreferenceChangeListener.

{% highlight java %}
sharedPreferences.registerOnSharedPreferenceChangeListener(new SharedPreferences.OnSharedPreferenceChangeListener() {
            @Override
            public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
                // TODO: do something
            }
        });
{% endhighlight %}

Callback zostanie wywołany tylko jeżeli wartość dla klucza zostanie dodane lub zmieniona. Jeżeli dla klucza będzie wpisywana ta sama wartość to callback nie zostanie wywołany. Ważne jest aby usunąć listenera z tego callbacku wołając metodę unregisterOnSharedPreferenceChangeListener.

## Podsumowanie

Jeżeli w aplikacji pojawia się potrzeba przechowywania wartości tak aby były nie zmienione po ponownym uruchomieniu aplikacji warto wziąć pod uwagę SharedPreferences. Wyżej opisany mechanizm można wykorzystać w wielu sytuacja np. ustawienia aplikacji. Implementacja jest szybka i daje spore możliwości.