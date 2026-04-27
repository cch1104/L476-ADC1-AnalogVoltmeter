# L476-ADC1-AnalogVoltmeter

# STM32 LCD Voltmeter Project (ADC + 1602A LCD)

## Project Overview
This project uses STM32 internal 12-bit ADC to measure analog voltage and display the result on a 1602A LCD.

The system continuously:
1. Reads analog voltage from ADC Channel 3
2. Converts ADC raw value into voltage
3. Displays voltage on LCD

---

## Hardware Used

- STM32 MCU (STM32L4 Series)
- 1602A LCD Module
- Potentiometer / Analog Source
- Jumper Wires
- Breadboard
- 3.3V Supply

---

## LCD Pin Connection Example

| LCD Pin | STM32 Pin |
|--------|----------|
| RS | PA6 |
| EN | PA7 |
| D4 | PA8 |
| D5 | PA9 |
| D6 | PA10 |
| D7 | PA11 |

---

## ADC Configuration

| Setting | Value |
|--------|------|
| ADC | ADC1 |
| Resolution | 12-bit |
| Scan Mode | Disable |
| Continuous Mode | Enable |
| Data Align | Right |
| Channel | ADC_CHANNEL_3 |
| Sample Time | 2.5 Cycles |
| Trigger | Software Start |

---

## Voltage Formula

```c
mv = adcResult * 3300.0 / 4095.0;

## Program Flow

Power ON
 ↓
HAL_Init()
 ↓
Clock Config
 ↓
GPIO Init
 ↓
ADC Init
 ↓
LCD Init
 ↓
HAL_ADC_Start()
 ↓
Loop Forever
   Read ADC
   Convert Voltage
   Show LCD


Full Main Loop
float mv;
char buff[16];

HAL_ADC_Start(&hadc1);

while(1)
{
    HAL_ADC_PollForConversion(&hadc1,100);
    adcResult = HAL_ADC_GetValue(&hadc1);

    mv = ((float)adcResult) * 3300.0 / 4095.0;

    lcd_Clear();

    lcd_Goto(0,0);
    lcd_Puts("Voltmeter");

    lcd_Goto(0,1);

    int mv_int = (int)(mv * 100);

    sprintf(buff,"%d.%02dV", mv_int/100, mv_int%100);

    lcd_Puts(buff);

    HAL_Delay(2000);
}
LCD Output Example
Voltmeter
2.56V
Better UI Version
Voltage Meter
V=2.56V
Recommended Improvements
Faster Refresh
HAL_Delay(300);
Prevent LCD Flicker

Do not use:

lcd_Clear();

every loop.

Stable Reading

Average 10 ADC samples.

Add TMP36 Sensor
Volt:2.56V
Temp:27.4C
Common Problems
Random Voltage

ADC pin floating.

Fix:

Connect potentiometer
Use resistor to GND
Wrong Voltage

Check:

VREF = 3.3V
Analog mode enabled
GND shared
LCD Blank

Check:

Contrast pin
Wiring
lcd.c pin config
Final Better Code
while(1)
{
    HAL_ADC_PollForConversion(&hadc1,100);
    adcResult = HAL_ADC_GetValue(&hadc1);

    mv = adcResult * 3300.0 / 4095.0;

    int v = (int)(mv * 100);

    lcd_Goto(0,0);
    lcd_Puts("Voltage Meter ");

    lcd_Goto(0,1);

    sprintf(buff,"V=%d.%02dV ", v/100, v%100);
    lcd_Puts(buff);

    HAL_Delay(300);
}
