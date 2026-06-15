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

![zdjęcie modelu od przodu](https://via.placeholder.com/600x200?text=Gate+schematic)

*tu trzeba podmienić to zdjęcie.*

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

## Usage

After flashing, the firmware runs an infinite loop that:
- Reads the encoder value from TIM2
- Moves the gate to a **target position** (currently 0 → 1 → 0) with a 5 second pause between moves
- Controls motor speed and direction via PWM on TIM3

To modify behavior, edit the target position logic in `main.c` and, if needed, enable and tune the PID block (lines 99‑115).

---

## License

Distributed under the **MIT License** – see the `LICENSE` file for details.
