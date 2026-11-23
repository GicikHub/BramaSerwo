

# 🚪 Automatyczna brama sterowana pilotem IR – Arduino

## 📌 Opis projektu

Projekt przedstawia system automatycznego sterowania bramą z wykorzystaniem mikrokontrolera Arduino, dwóch serwomechanizmów, pilota na podczerwień (IR), czujnika ruchu oraz diody LED.

Układ umożliwia otwieranie, zamykanie i uchylanie bramy za pomocą pilota. Dodatkowo zastosowany czujnik ruchu odpowiada za zatrzymanie bramy w przypadku wykrycia przeszkody podczas jej przemieszczania.

Dioda LED pełni funkcję sygnalizacyjną i miga podczas ruchu bramy.

## ⚙️ Wykorzystane komponenty

- Arduino (np. Arduino Uno)
- 2 × serwomechanizm
- Odbiornik podczerwieni IR
- Pilot IR
- Czujnik ruchu
- Dioda LED
- Przewody połączeniowe
- Płytka stykowa
- Rezystor do diody LED (jeśli jest podłączona bezpośrednio)

## 🔌 Podłączenie komponentów

| Komponent | Pin Arduino |
| --- | --- |
| Serwomechanizm 1 | 9 |
| Serwomechanizm 2 | 11 |
| Dioda LED | 7 |
| Odbiornik IR | 4 |
| Czujnik ruchu | 5 |

**Uwaga:** Oba serwomechanizmy powinny mieć odpowiednie zasilanie. Przy większym poborze prądu zalecane jest zastosowanie zewnętrznego zasilacza oraz wspólnej masy (GND) z Arduino.

## 🎮 Sterowanie pilotem

Brama jest sterowana za pomocą czterech przycisków pilota IR.

| Funkcja | Kod IR | Działanie |
| --- | --- | --- |
| Otwórz | `0x10` | Całkowite otwarcie bramy |
| Zamknij | `0x11` | Całkowite zamknięcie bramy |
| Uchylanie przy otwieraniu | `0x12` | Ustawienie bramy w pozycji uchylonej od strony otwierania |
| Uchylanie przy zamykaniu | `0x14` | Ustawienie bramy w pozycji uchylonej od strony zamykania |

## 🔄 Pozycje bramy

W programie zdefiniowano trzy podstawowe pozycje serwomechanizmów:

| Pozycja | Kąt |
| --- | --- |
| Zamknięta | 0° |
| Uchylona | 45° |
| Otwarta | 90° |

Drugi serwomechanizm porusza się w przeciwnym kierunku względem pierwszego. Dzięki temu oba mechanizmy mogą symetrycznie sterować skrzydłami bramy.

## 🧠 Logika działania programu

Program wykorzystuje system stanów (maszynę stanów), który pozwala określić aktualną pozycję oraz sposób działania bramy.

Zdefiniowane stany:

- `ZAMKNIETA` – brama jest całkowicie zamknięta.
- `OTWARTA` – brama jest całkowicie otwarta.
- `UCHYLANA_PRZY_OTWIERANIU` – brama znajduje się w pozycji uchylonej podczas otwierania.
- `UCHYLONA_PRZY_ZAMYKANIU` – brama znajduje się w pozycji uchylonej podczas zamykania.
- `RUCH_OTWARCIA` – brama jest w trakcie otwierania.
- `RUCH_ZAMKNIECIA` – brama jest w trakcie zamykania.
- `ZATRZYMANA` – ruch bramy został zatrzymany.

### Działanie krok po kroku

1. Po uruchomieniu Arduino serwomechanizmy ustawiają bramę w pozycji zamkniętej.
2. Odbiornik IR oczekuje na sygnał z pilota.
3. Po odebraniu odpowiedniego kodu program ustala docelową pozycję bramy.
4. Serwomechanizmy zmieniają swoje położenie stopniowo, o jeden stopień w określonym odstępie czasu.
5. Podczas ruchu bramy dioda LED zaczyna migać.
6. Po osiągnięciu pozycji docelowej brama zatrzymuje się, a dioda LED gaśnie.
7. Jeżeli czujnik ruchu wykryje ruch podczas przemieszczania bramy, program zatrzymuje jej ruch.

## 🚨 Zabezpieczenie – czujnik ruchu

Czujnik ruchu jest jednym z najważniejszych elementów projektu.

Jego zadaniem jest wykrywanie ruchu podczas pracy bramy.

Po wykryciu ruchu:

- aktualny kąt serwomechanizmów zostaje zachowany,
- docelowy kąt zostaje ustawiony na aktualną pozycję,
- brama przechodzi w stan `ZATRZYMANA`,
- dioda LED zostaje wyłączona,
- w monitorze portu szeregowego pojawia się komunikat o wykryciu ruchu.

Dodatkowo sterowanie pilotem jest blokowane, gdy czujnik pozostaje aktywny.

**Uwaga:** Zastosowany czujnik ruchu nie jest pełnoprawnym czujnikiem bezpieczeństwa bramy. W rzeczywistym urządzeniu należałoby zastosować odpowiednie czujniki obecności lub fotokomórki.

## 💡 Sygnalizacja LED

Dioda LED informuje o aktualnej pracy bramy:

- LED wyłączona – brama stoi lub osiągnęła pozycję docelową.
- LED miga – brama jest w trakcie otwierania lub zamykania.
- LED wyłącza się po wykryciu ruchu przez czujnik.

Częstotliwość migania jest określona przez zmienną `CZAS_MIGANIA`.

## 📚 Wykorzystane biblioteki

Projekt wykorzystuje dwie biblioteki Arduino:

- **Servo.h** – umożliwia sterowanie serwomechanizmami.
- **IRremote.h** – odpowiada za odbieranie i dekodowanie sygnałów z pilota IR.

Biblioteki należy zainstalować w środowisku Arduino IDE przed kompilacją programu.

## 🖥️ Monitor portu szeregowego

Program wykorzystuje komunikację szeregową z prędkością 9600 baud.

W monitorze portu szeregowego wyświetlane są komunikaty informujące o aktualnym działaniu układu, np.:

- `BRAMA ZAMKNIETA`
- `OTWIERANIE BRAMY`
- `ZAMYKANIE BRAMY`
- `BRAMA OTWARTA`
- `WYKRYTO RUCH!`
- `BRAMA ZATRZYMANA`
- `CZUJNIK AKTYWNY - BRAK RUCHU`

Dzięki temu możliwe jest monitorowanie działania systemu i łatwiejsze wykrywanie ewentualnych błędów.

## 🚀 Uruchomienie projektu

1. Podłącz wszystkie komponenty zgodnie z tabelą pinów.
2. Zainstaluj biblioteki `Servo` oraz `IRremote`.
3. Otwórz kod programu w Arduino IDE.
4. Wybierz odpowiednią płytkę Arduino i port COM.
5. Wgraj program do mikrokontrolera.
6. Uruchom monitor portu szeregowego z prędkością 9600 baud.
7. Steruj bramą za pomocą pilota IR.

## 🔧 Możliwości rozbudowy

Projekt można w przyszłości rozbudować o:

- automatyczne zamykanie bramy po określonym czasie,
- dodatkowe czujniki bezpieczeństwa,
- sterowanie za pomocą aplikacji mobilnej,
- obsługę modułu Bluetooth lub Wi-Fi,
- zapamiętywanie ostatniej pozycji bramy,
- sygnalizację dźwiękową podczas ruchu.

## 👨‍💻 Podsumowanie


Mała uwaga techniczna: w Twoim kodzie zatrzymanie bramy po wykryciu ruchu działa tylko wtedy, gdy `kat != cel`. Jeśli czujnik ma pełnić funkcję zabezpieczenia, warto dodatkowo przemyśleć sposób wznowienia ruchu po ustaniu przeszkody. W obecnej wersji brama pozostaje zatrzymana do otrzymania kolejnego polecenia z pilota.
