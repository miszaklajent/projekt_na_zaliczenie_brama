# Projekt Bramy 1 – STM32 Gate Controller

*Semestral university project developed in collaboration with a fellow student.*

---

## Ogólny obraz

W tym repozytorium znajduje się dokumentacja projektu studenckiego na zaliczenie przedmiotu *techniki mikroprocesorowe* 
Przedstawia model bramy mogącej być użytą podczas partii gier planszowych i nie planszowych
### projekt obejmuje
- idea systemu
- projekt części elektronicznej systemu
- oprogramowanie systemu
- modyfikacja modelu 3d zawierająca rozszerzenie obecnego modelu o elementy ruchome
- projekt wizualny w stylu *Warehamëra*

![zdjęcie modelu od przodu](asets/model_przód.JPG)
*Zdjęcie projektu od przodu*


---

## Cechy

- **STM32G0** (STM32G031) MCU z driverami HAL
- **Użyty enkoder** (TIM2) dokładne pozycjonowanie bramy podczas ruchu
- **Bang-Bang motor control** (TIM3) posiada możliwość sterowania prędkością poprzez użycie PWM
- **Timer‑based control loop** (TIM14) odświeżanie ~1 kHz
- **Czujnik IR** wykrywający konieczność otwarcia zapory
- **Elastyczność** możliwość ustawienia dowolnego zakresu otwarcia, prędkości i opóźnięń

---

## Hardware

| Komponent | Opis |
|-----------|-------------|
| MCU | **STM32G031** – Arm Cortex‑M0+, 64 KB flash, 16 KB RAM |
| Encoder | Enkoder zamontowany na wale silnika z hardwarowym pomiarem pozycji przez TIM2 |
| Motor driver | DRV8833 sterowany przez PWM na TIM3 dwa kanały użyte do sterowania - H‑bridge |
| silnik | GA12 N20 50RPM z wbudowanym enkoderem magnetycznym |
| moc | zasilanie 5v przez port USB-C |

![mikro](asets/mikrokontroler.JPG)
*Elektronika modelu*

![przekładnia](asets/rack_n_pinion.JPG)
*Mechanika przesuwania bramy*

---

## Oprogramowanie

- Oprogramowanie zostało napisane w języku C
- Konfiguracja mikrokontrolera wygenerowana przez STM32_cube_MX
- Środowisko programistyczne - VScode + stm32 addon + cmake
- Debugger zarządzany przez cortex debugger
- debugowanie przez dedykowany programator ST_link v2.1

Oprogramowanie używa standardowych bibliotek HAL (Hardware Abstraction Layer):
1. Konfiguracja zegarów systemu (`SystemClock_Config`)
2. Inicjalizacja peryferiów (`MX_TIM2_Init`, `MX_TIM3_Init`, `MX_TIM14_Init`, `MX_GPIO_Init`)
3. Odczyt enkodera i zmiana czasów PWM `HAL_TIM_PeriodElapsedCallback`
4. Główna pętla asynchronicznie ustawia pozycje do której ma dążyć silnik


---

## Działanie

- Po podłączeniu zasilania, następuje zerowanie układu kinematycznego (homing).
- Następnie za każdym razem, kiedy czujnik IR wykryje przeszkodę, otwiera bramę na kilka sekund, następnie brama się zamyka.
- Homing wykopywany jest tylko raz po restarcie, w dalszym działaniu nie jest potrzebny ponieważ enkoder liczy pozycje silnika.
