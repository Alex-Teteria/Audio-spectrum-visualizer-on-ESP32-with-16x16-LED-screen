# Audio spectrum visualizer on ESP32 with 16x16 LED screen
Calculation of the audio signal spectrum and its display on the WS2812B RGB LED panel using MicroPython on the ESP32 microcontroller.

## Зміст  

1. [Огляд](./README.md#1-огляд)
2. [Схема складання аналізатора](./README.md#2-Схема-складання-аналізатора)
3. [Приклад відображення аудіосигналу](./README.md#3-Приклад-відображення-аудіосигналу)
4. [Install](./README.md#4-install)
   
## 1. Огляд

Акустичні сигнали в діапазоні частот 20Гц - 19кГц у їх спектральному вигляді відображаються на LED-екрані розміром 16х16.  
Характеристики відображення:
 - 16 частотних смуг розділені по діапазонам:  
     (20-80)Гц; (80-140)Гц; (140-200)Гц; (200-320)Гц; (320-480)Гц; (480-700)Гц; (700-970)Гц; (970-1300)Гц; (1,3-1,7)кГц; (1,7-2,2)кГц; (2,2-3,0)кГц; (3,0-3,8)кГц; (3,8-5,0)кГц; (5,0-6,5)кГц; (6,5-9)кГц; (9-19)кГц.
 - 16 рівнів у логарифмічному співвідношенні амплітуд.
 
Спектральні коефіцієнти обчислюються через FFT. Величина масива вхідних відліків - 512.  
Для розрахунку FFT використано алгоритм Кулі — Тьюкі, зокрема його реалізацію на MicroPython [algorithms](https://github.com/peterhinch/micropython-fourier). 

## 2. Схема складання аналізатора

![Microphone_to_fft](https://github.com/Alex-Teteria/Audio-spectrum-analyzer-on-ESP32-with-16x16-LED-screen/assets/94607514/c6ebc0d4-dcde-469f-9ded-90e17a0b98d9)  
Основні модулі:
- мікрофон з підсилювачем на мікросхемі MAX4466 (частотний діапазон 20Гц - 20кГц; коефіцієнт підсилення від 25 до 125);
- WS2816 RGB LED панель, 16х16 світлодіодів;
- модуль мікроконтролера ESP32, а саме ESP-WROOM-32.
 
Для кращого візуального сприйняття спектру реалізовано автоматичну зміну чутливості аналізатора та систему АРУ пікових рівнів. Тому спектр завжди відображається на весь екран при зміні рівнів акустичних сигналів і не потребує налаштувань підсилення-затухання при зміні джерела акустичного сигналу. Модуль мікрофонного підсилювача має на виході постійну складову VCC / 2, тому сигнал на вхід АЦП подається через конденсатор. Резистори 110к та 20к забезпечують режим роботи АЦП по постійному струму, а саме зміщення вхідного сигналу в середину діапазону роботи АЦП (0,5В при діапазоні перетворень від 0 до 1В).  
Частота дискретизації АЦП становить приблизно 28,4кГц. Тому чатота Найквіста - 14,2кГц. Аліаси (діапазон 14,2кГц - 20кГц) будуть попадати в діапазон від 8,4кГц до 14,2 кГц. Чатоти близькі до 20кГц будуть мати аліас частотою 28,4-20=8,4кГц і попадуть в передостанній частотний діапазон, а решта - аліаси в діапазоні від 14,2кГц до 19кГц попадають в останній частотний діапазон. Тому фактично робочий частотний діапазон візуалізатора за рахунок аліасинга розширюється до 19 кГц. Частоти близькі до 20кГц давимо антіаліасинговим RC-фільтром.  
При відображенні реалізовано логарифмічне співвідношення рівнів спектральних складових.

## 3. Приклад відображення аудіосигналу  

[Візуалізатор аудіо спектру](https://www.youtube.com/watch?v=adPKXyhDuic)  

## 4. Install

Зібрати схему та завантажити файли в мікроконтролер. Схема не потребує додаткових налаштувань.  




