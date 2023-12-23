# Audio spectrum visualizer on MicroPython with 16x16 LED screen
Calculation of the audio signal spectrum and its display on the WS2812B RGB LED panel using MicroPython on the microcontroller.

## Зміст  

1. [Огляд](./README.md#1-огляд)
2. [Схема складання](./README.md#2-Схема-складання)
3. [Реалізація на Pi Pico with RP2040](./README.md#3-Реалізація-на-Pi-Pico-with-RP2040)
4. [Приклад відображення аудіосигналу](./README.md#4-Приклад-відображення-аудіосигналу)
5. [Install](./README.md#5-install)
   
## 1. Огляд

Акустичні сигнали в діапазоні частот 20Гц - 20кГц у їх спектральному вигляді відображаються на LED-екрані розміром 16х16.  
Характеристики відображення:
 - 16 частотних смуг розділені по діапазонам:
    реалізація на Pi Pico with RP2040  
   (40-110)Гц; (110-190)Гц; (190-270)Гц; (270-350)Гц; (350-500)Гц; (500-660)Гц; (660-890)Гц; (890-1280)Гц;  
    (1,28-1,82)кГц; (1,82-2,6)кГц; (2,6-3,6)кГц; (3,6-5,1)кГц; (5,1-7,1)кГц; (7,1-10,0)кГц; (10,0-14,0)кГц; (14,0-20,0)кГц.  
  
   реалізація на ESP-WROOM-32
    (20-80)Гц; (80-140)Гц; (140-200)Гц; (200-320)Гц; (320-480)Гц; (480-700)Гц; (700-970)Гц; (970-1300)Гц;  
    (1,3-1,7)кГц; (1,7-2,2)кГц; (2,2-3,0)кГц; (3,0-3,8)кГц; (3,8-5,0)кГц; (5,0-6,5)кГц; (6,5-9)кГц; (9-19)кГц.
   
 - 16 рівнів у логарифмічному співвідношенні амплітуд.
 
Спектральні коефіцієнти обчислюються через FFT. Величина масива вхідних відліків - 512.  
Для Pi Pico використано бібліотеку [ulab](https://github.com/v923z/micropython-ulab).  
При реалізації на ESP32 використано алгоритм Кулі — Тьюкі, зокрема [algorithms](https://github.com/peterhinch/micropython-fourier). 

## 2. Схема складання  

![Microphone_to_fft](https://github.com/Alex-Teteria/Audio-spectrum-analyzer-on-ESP32-with-16x16-LED-screen/assets/94607514/c6ebc0d4-dcde-469f-9ded-90e17a0b98d9)  
Основні модулі:
- мікрофон з підсилювачем на мікросхемі MAX4466 (частотний діапазон 20Гц - 20кГц; коефіцієнт підсилення від 25 до 125);
- WS2816 RGB LED панель, 16х16 світлодіодів;
- модуль мікроконтролера ESP32, а саме ESP-WROOM-32.
 
Для кращого візуального сприйняття спектру реалізовано автоматичну зміну чутливості аналізатора та систему АРУ пікових рівнів. Тому спектр відображається на весь екран при зміні рівнів акустичних сигналів і не потребує налаштувань підсилення-затухання при зміні рівня та джерела акустичного сигналу.  
Резистори 110к та 20к забезпечують режим роботи АЦП по постійному струму (0,5В при діапазоні перетворень від 0 до 1В).  
Частота дискретизації АЦП становить приблизно 28,4кГц, відповідно чатота Найквіста - 14,2кГц. Аліаси (діапазон 14,2кГц - 20кГц) будуть попадати в діапазон від 8,4кГц до 14,2 кГц. Чатоти близькі до 20кГц будуть мати аліас частотою 28,4-20=8,4кГц і попадуть в передостанній частотний діапазон, а решта - аліаси в діапазоні від 14,2кГц до 19кГц попадають в останній частотний діапазон. Тому фактично робочий частотний діапазон візуалізатора за рахунок аліасинга розширюється до 19 кГц.  
При відображенні реалізовано логарифмічне співвідношення рівнів спектральних складових.

## 3. Реалізація на Pi Pico with RP2040  

Діапазон роботи АЦП становить 0-3.3В, тому з'єднувати модуль мікрофонного підсилювача з входом АЦП можна безпосередньо (використано pin 26). Частоту дискретизації АЦП підібрано приблизно 39,6кГц, відповідно чатота Найквіста - 19,8кГц, тому аліаси відсутні. Частотний діапазон роботи буде визначатись частотним діапазоном модуля мікрофонного підсилювача (20Гц - 20кГц). Для розрахунку fft використано модуль [ulab](https://github.com/v923z/micropython-ulab) який відсутній у бібліотеках офіційної прошивки [firmware](https://micropython.org/download/RPI_PICO/). Тому слід викорисати прошивку [micropython firmware with ulab included]https://github.com/v923z/micropython-builder.

## 4. Приклад відображення аудіосигналу  

[Візуалізатор аудіо спектру](https://www.youtube.com/watch?v=adPKXyhDuic)  

## 5. Install

Зібрати схему та завантажити файли в мікроконтролер. Схема не потребує додаткових налаштувань.  

| Файл | Призначення |
| --- | --- |
| spectrum_to_neo_512_16_v2.py | Основний код, для автоматичного виконання під час кожного включення слід перейменувати в main.py |
| spectrum_to_neo_256_16_v4.py | Основний код для реалізації на Pi Pico with RP2040 |
| neo_matrix.py | Модуль для роботи з WS2816 LED панелями |
| algorithms_fft.py | Модуль з функціями для FFT [algorithms](https://github.com/peterhinch/micropython-fourier)  |

###### [Top](./README.md#зміст)  




