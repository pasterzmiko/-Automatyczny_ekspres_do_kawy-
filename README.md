# 1. Tytuł projektu
## Automatyczny ekspres do kawy - model AADL
# 2. Dane studenta
 - Imię i nazwisko: Mikołaj Pasterz
 - email: Pasterz@student.agh.edu.pl

# 3. Opis modelowanego systemu 
## 1. Opis ogólny
Projekt przedstawia model architektury systemu wbudowanego automatycznego ekspresu do kawy, skupiający się na krytycznych zasobach fizycznych i logice sterowania procesem zaparzania. Model został stworzony w języku AADL (Architecture Analysis and Design Language) w celu umożliwienia walidacji architektury pod kątem obciążeń jeszcze przed etapem implementacji.

System opiera się na:

- Centralnym Kontrolerze (Processor), na którym działają procesy decyzyjne (GlownyProcesZaparzania).
- Aktuatorach (Pompy, Grzałka, Młynek), które są największymi konsumentami energii.
- Magistrali Energetycznej zdefiniowanej jako system transmisji mocy.
- Analizach obciążeniowych, które uwzględniają budżet wagowy (SEI::WeightLimit) oraz budżet energetyczny (SEI::PowerBudget, SEI::PowerSupply), co pozwala na weryfikację, czy dostarczana moc (1500 W) i pojemność magistrali (1400 W) są wystarczające dla wszystkich podzespołów.
    
## 2. Opis dla użytkownika
Urządzenie jest w pełni automatycznym ekspresem do kawy, który wykonuje szereg skomplikowanych sekwencji w odpowiedzi na wybór użytkownika.

Po wybraniu napoju na Wyświetlaczu LCD (interfejs użytkownika), system sterowania automatycznie uruchamia i koordynuje następujące zadania:

- Przygotowanie surowców: Na podstawie wybranego napoju, system aktywuje Młynek Kawy do zmielenia ziaren i używa Sensora Ilości do dozowania odpowiedniej porcji.
- Kontrola termiczna: Grzałka Wody podnosi temperaturę, a Sensor Temperatury monitoruje, czy osiągnięto optymalny poziom dla ekstrakcji kawy.
- Dozowanie płynów: Pompa Wody i Pompa Mleka dozują płyny w określonych proporcjach do stworzenia wybranego napoju (np. Espresso, Latte).
- Obróbka mleka: Jeśli to konieczne, Spieniacz Mleka jest aktywowany.
- Sterowanie i komunikacja: Cała logika sterowania jest realizowana przez procesy uruchomione na Kontrolerze Głównym, które komunikują się ze wszystkimi urządzeniami fizycznymi poprzez Magistralę Danych.
  
# 4. Spis komponentów AADL

# Dane Systemu (System Data)

## Definicje Typów Danych (Data Type Definitions)

| Typ Danych | Opis | Wewnętrzna Reprezentacja (Data_Model::Data_Representation) |
| :--- | :--- | :--- |
| `KonfiguracjaNapoju` | Typ dla wyboru kawy przez użytkownika. | `Enum` (Wyliczenie) |
| `MieszankaPlynow` | Typ danych dla parametrów przygotowania napoju. | `Struct` (Struktura) |
| `LicznikSurowca` | Typ dla odczytów sensorów (ilość/temperatura). | `Integer` (Liczba Całkowita) |

---

## Pakiet (Package)

| Nazwa Pakietu | Opis |
| :--- | :--- |
| `ekspres_kompakt_analiza_wagi` | Główny pakiet architektoniczny ekspresu do kawy. |

## Systemy Główne (Main Systems)

| Nazwa Systemu | Opis |
| :--- | :--- |
| `UkladSterowania` | System najwyższego poziomu. Agreguje wszystkie podzespoły fizyczne i logiczne; definiuje globalny budżet wagowy. |

## Procesory (Processors)

| Nazwa Procesora | Opis |
| :--- | :--- |
| `KontrolerGlowny` | Główna jednostka obliczeniowa (CPU), na której wykonywana jest cała logika sterowania (Procesy). |

## Magistrale (Buses)

| Nazwa | Opis |
| :--- | :--- |
| `MagistralaDanych` | Magistrala komunikacyjna, łącząca procesor i sensory. |


## Pamięć (Memory)

| Nazwa Pamięci | Opis |
| :--- | :--- |
| `PamiecSystemowa` | Pamięć wykorzystywana przez procesor do obsługi procesów. |

## Urządzenia (Devices)

| Nazwa Urządzenia | Opis |
| :--- | :--- |
| `SensorIlosci` | Sensor mierzący ilość dozowanych surowców. |
| `SensorTemperatury` | Sensor mierzący temperaturę. |
| `PompaWody` | Aktuator do przemieszczania wody. |
| `PompaMleka` | Aktuator do przemieszczania mleka. |
| `DozownikKawy` | Aktuator do dozowania kawy. |
| `MlynekKawy` | Aktuator do mielenia ziaren. |
| `SpieniaczMleka` | Aktuator do spieniania. |
| `GrzalkaWody` | Aktuator do podgrzewania wody (duży pobór mocy). |
| `WyswietlaczLCD` | Interfejs użytkownika. |
| `ZasilanieGlowne` | Urządzenie dostarczające moc elektryczną (`PowerSupplier`) do systemu. |
| `Obudowa` | Komponent pasywny, definiujący wagę obudowy. |

## Wątki (Threads)

| Nazwa Wątku | Opis |
| :--- | :--- |
| `LogikaSkladnikow` | Główny wątek decyzyjny sterowania aktuatorami. |
| `PrzekaznikDanych` | Wątek pomocniczy do agregacji lub przekazywania danych sensorów. |

## Procesy (Processes)

| Nazwa Procesu | Rola |
| :--- | :--- |
| `GlownyProcesZaparzania` | Proces wykonujący główną logikę sterowania i wysyłający komendy do aktuatorów. |
| `ProcesObslugiIO` | Proces odpowiedzialny za zbieranie i wstępną obróbkę danych z sensorów i interfejsu. |

# 5. Model - rysunek

<img width="1580" height="568" alt="obraz" src="https://github.com/user-attachments/assets/e6199011-1714-4fb1-8897-05d9c02ab897" />



# 6. Proponowane metody analizy modelu, dostępne w Osate. Wyniki przeprowadzonych analiz

Poniżej przedstawiono wyniki analiz przeprowadzonych przy użyciu wtyczek analitycznych środowiska **OSATE** na podstawie zdefiniowanych właściwości zasobów.

## A. Analiza Wagowa (Weight / Mass Analysis) 

Analiza ta weryfikuje, czy fizyczna masa wszystkich komponentów systemu nie przekracza założonego limitu projektowego.

| Parametr | Wartość | Uwagi |
| :--- | :--- | :--- |
| **Założony limit** (`UkladSterowania::WeightLimit`) | $\text{11.0 kg}$ | Całkowity budżet wagowy dla urządzenia. |
| **Całkowita masa systemu** (Suma `GrossWeight`) | $\text{10.675 kg}$ | Suma wag wszystkich subkomponentów fizycznych. |
| **Wniosek** | **Wymagania Spełnione** | Urządzenie spełnia wymagania wagowe z zapasem  $\text{0.325 kg}$. |

## B. Analiza Energetyczna (Electrical Power Analysis) 

Analiza weryfikuje bilans energetyczny (podaż vs. popyt) oraz wytrzymałość magistrali w trybie pełnego obciążenia.

### B1. Bilans Popytu i Podaży

| Parametr | Wartość | Uwagi |
| :--- | :--- | :--- |
| **Maksymalna moc dostarczana** (`ZasilanieGlowne::PowerSupply`) | $\text{1500.0 W}$ | Maksymalna moc, jaką może zapewnić zasilacz. |
| **Sumaryczny pobór mocy** (Suma `PowerBudget`) | $\text{1092.0 W}$ | Całkowite, jednoczesne zapotrzebowanie wszystkich konsumentów. |
| **Wniosek Popytu** | **Wymagania Spełnione** | Podaż ($\text{1500 W}$) przewyższa pobór ($\text{1092.0 W}$). |

### B2. Obciążenie Magistrali

| Parametr | Wartość | Uwagi |
| :--- | :--- | :--- |
| **Maksymalna pojemność magistrali** (`MagistralaEnergetyczna::PowerCapacity`) | $\text{1400.0 W}$ | Maksymalna moc, jaką może bezpiecznie obsłużyć okablowanie. |
| **Moc dostarczana do magistrali** | $\text{1500.0 W}$ | Moc wejściowa z zasilacza. |
| **Wniosek Obciążenia** | **Przekroczenie Limitu (Alarm)** | Moc dostarczana ($\text{1500.0 W}$) **przekracza** pojemność magistrali ($\text{1400.0 W}$). Wymagana jest wymiana magistrali na taką, która wytrzyma co najmniej $\text{1500 W}$. |

# 7. Inne informacje zależne od tematu

Projekt kładzie szczególny nacisk na **walidację architektury** pod kątem zasobów fizycznych, co jest kluczowe dla optymalizacji kosztów produkcji i bezpieczeństwa użytkowania.

* **Optymalizacja zasobów (Waga i Moc):** Integracja analiz **`SEI::GrossWeight`** (waga) i **`SEI::PowerBudget`** (pobór mocy) umożliwia wczesne wykrywanie wąskich gardeł. Jest to niezbędne, aby zapewnić, że całkowita waga urządzenia jest akceptowalna, a system zasilania jest bezpieczny i wystarczający (np. uniknięcie przeciążenia **Magistrali Energetycznej**).
* **Wielkość Poboru Mocy:** Krytycznym elementem jest **Grzałka Wody** (`GrzalkaWody`), która stanowi zdecydowaną większość ($\text{1000 W}$) całkowitego zapotrzebowania na moc. Analiza musi uwzględniać ten wysoki, krótkotrwały pobór przy doborze **Zasilania Głównego** i **Magistrali Energetycznej**.
* **Separacja Logiki:** Wyodrębnienie **Procesu Obsługi I/O** (`ProcesObslugiIO`) od **Głównego Procesu Zaparzania** (`GlownyProcesZaparzania`) zapewnia modularną budowę, ułatwiając separację zadań:
    * **I/O:** zbieranie i wstępna obróbka danych z sensorów i interfejsu.
    * **Sterowanie:** podejmowanie decyzji i wydawanie komend do aktuatorów.
    * Taka modularność ułatwia **skalowalność** oraz modyfikację algorytmów sterujących bez wpływu na warstwę sprzętową.
# 8. Literatura
- [1] OSATE 2 - Open Source AADL Tool Environment - https://osate.org/
- [2] AADL Community Resources and Examples - https://github.com/osate
- [3] SAE International, "AS5506C: Architecture Analysis & Design Language (AADL)," SAE International Standard, 2017 - https://www.sae.org/standards/content/as5506c/

