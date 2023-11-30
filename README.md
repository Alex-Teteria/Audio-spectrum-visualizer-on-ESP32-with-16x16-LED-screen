# Audio-spectrum-analyzer-on-ESP32-with-16x16-LED-screen
Calculation of the audio signal spectrum and its display on the WS2812B RGB LED panel using the ESP32 microcontroller under MicroPython.

## Зміст  

1. [Огляд](./README.md#1-огляд)
2. [Схема складання аналізатора](./README.md#2-Схема-складання-аналізатора)
3. [Приклад відображення аудіосигналу](./README.md#3-Приклад-відображення-аудіосигналу)
4. [Install](./README.md#4-install)
   
## 1. Огляд

Акустичні сигнали в діапазоні частот 20Гц - 20кГц у їх спектральному вигляді відображаються на LED-екрані розміром 16х16.  
Характеристики відображення:
 - 16 частотних смуг розділені по діапазонам:  
     (20-80)Гц; (80-120)Гц; (120-240)Гц; (240-480)Гц; (480-1000)Гц; (1-2)кГц; (2-4)кГц; (4-6)кГц; (6-8)кГц; (8-10)кГц; (10-12)кГц; (12-14)кГц; (14-16)кГц; (16-18)кГц; (18-20)кГц.
 - 16 рівнів у логарифмічному співвідношенні амплітуд
Для розрахунку FFT використано алгоритм Кулі — Тьюкі, зокрема його реалізацію на MicroPython [algorithms](https://github.com/peterhinch/micropython-fourier). 


## 2. Схема складання аналізатора

![Microphone_to_fft](https://github.com/Alex-Teteria/Audio-spectrum-analyzer-on-ESP32-with-16x16-LED-screen/assets/94607514/c6ebc0d4-dcde-469f-9ded-90e17a0b98d9)  
Для кращого візуального сприйняття спектру реалізовано автоматичну зміну чутливості аналізатора та систему АРУ пікових рівнів. Тому спектр завжвди відображається на весь екран при зміні рівнів акустичних сигналів і не потребує налаштувань підсилення-затухання при зміні джерела акустичного сигналу. Модуль мікрофонного підсилювача має на виході постійну складову VCC / 2, тому сигнал на вхід АЦП подається через конденсатор. Резистори 110к та 20к забезпечують режим роботи АЦП по постійному струму, а саме зміщення вхідного сигналу в середину діапазону роботи АЦП (0,5В при діапазоні перетворень від 0 до 1В).
При відображенні реалізовано логарифмічне співвідношення рівнів спектральних складових.

## 3. Приклад відображення аудіосигналу  

[Аналізатор аудіо спектру](https://www.youtube.com/watch?v=adPKXyhDuic)  

## 4. Install

Зібрати схему та завантажити файли в мікроконтролер. Схема не потребує додаткових налаштувань.  




