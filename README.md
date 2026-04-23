# 🔧 STM32 Embedded Systems — Learning Journey

A hands-on collection of STM32 embedded systems projects built using **STM32CubeIDE** and **HAL drivers**, documenting my learning journey through peripherals, communication protocols, and real-world embedded concepts.

> 📌 Each project is self-contained with its own source code and documentation. New projects are added regularly as I progress.

---

## 🛠️ Development Setup

| Tool | Details |
|---|---|
| **IDE** | STM32CubeIDE 1.18.1 |
| **Library** | STM32 HAL (Hardware Abstraction Layer) |
| **Target Board** | STM32F407VG (Discovery) |
| **Language** | Embedded C |
| **Debugger** | ST-Link / SWV (Serial Wire Viewer) |

---

## 📁 Projects

### 💡 GPIO & LED Control

| Project | Description |
|---|---|
| [LEDblink](./LEDblink) | Basic LED blink using HAL_Delay — the classic first embedded project |
| [LED](./LED) | GPIO output control with structured code and theory |
| [Swtch_LED](./Swtch_LED) | Toggle LED using a push button (polling) |
| [LED_interrupt](./LED_interrupt) | Button-triggered LED using external interrupts (EXTI) |
| [LED_Timer_polling](./LED_Timer_polling) | LED blink using hardware timer in polling mode |
| [Potentiometer_multi_led](./Potentiometer_multi_led) | Control multiple LEDs based on ADC potentiometer value |

---

### ⏱️ Timers & PWM

| Project | Description |
|---|---|
| [Counters](./Counters) | Timer counter modes — up, down, center-aligned |
| [PWM](./PWM) | PWM signal generation using TIM peripheral |
| [Freq_Counter](./Freq_Counter) | Measure input signal frequency using timer input capture |

---

### 📡 ADC & DAC

| Project | Description |
|---|---|
| [ADC_Potentiometer](./ADC_Potentiometer) | Read analog voltage from a potentiometer using ADC polling |
| [DAC_Square](./DAC_Square) | Generate square wave using DAC output |
| [DAC_TRIANGLE](./DAC_TRIANGLE) | Generate triangle wave using DAC output |
| [k_thermocouple](./k_thermocouple) | Read temperature from a K-type thermocouple via SPI |

---

### 🔁 DMA

| Project | Description |
|---|---|
| [DMA](./DMA) | Memory-to-memory and peripheral DMA transfers |

---

### 🔗 Communication Protocols

| Project | Description |
|---|---|
| [UART](./UART) | UART transmit — sending data to serial terminal |
| [UART_Reception](./UART_Reception) | UART receive — reading incoming serial data |
| [Printf_SWV](./Printf_SWV) | Redirect printf output to SWV Debug Console (ITM) |
| [I2C_COMM](./I2C_COMM) | I2C communication between master and slave |
| [I2C_Tx_Rx](./I2C_Tx_Rx) | I2C transmit and receive with interrupt handling |
| [CAN_Comm](./CAN_Comm) | CAN bus communication — transmit and receive frames |

---

### 🔍 Sensors & Interfaces

| Project | Description |
|---|---|
| [IR_SENSOR](./IR_SENSOR) | Read IR sensor output using GPIO interrupt |

---

### 🛡️ System & Reliability

| Project | Description |
|---|---|
| [IWDT](./IWDT) | Independent Watchdog Timer — system reset on hang detection |

---

## 📈 Progress

- [x] GPIO & LED basics  
- [x] Timers (polling, interrupt, PWM, input capture)  
- [x] ADC & DAC  
- [x] DMA transfers  
- [x] UART communication  
- [x] SWV / Printf debugging  
- [x] I2C protocol  
- [x] CAN bus  
- [x] Watchdog timer  
- [ ] SPI (in progress)  
- [ ] RTC (upcoming)  
- [ ] FreeRTOS (upcoming)  

---

## 🚀 How to Use Any Project

1. Clone the repository:
   ```bash
   git clone https://github.com/Amrutkumarbh/STM32-PROJECTS.git
   ```
2. Open **STM32CubeIDE** → `File → Open Projects from File System`
3. Navigate to the desired project folder
4. Build (`Ctrl + B`) and flash to your STM32 board

---

## 🤝 Connect

If you're also learning embedded systems or want to discuss any of these projects, feel free to open an issue or reach out!

---

*This repository is actively maintained as part of my embedded systems learning journey. ⚡*
