# 🧠 STM32 Embedded Systems — Learning Journey

> This repository documents my hands-on learning path through embedded systems programming on STM32 microcontrollers.
> Each project is a focused exercise on a specific peripheral or concept — built progressively from bare-metal GPIO
> all the way to communication protocols and waveform generation — using **STM32CubeIDE** and the **HAL library**.

---

## 👨‍💻 About

**Amrut Kumar**  
Embedded Systems Learner & Developer  
[![GitHub](https://img.shields.io/badge/GitHub-Amrutkumarbh-181717?logo=github)](https://github.com/Amrutkumarbh)

---

## 🛠️ Development Environment

| Tool | Details |
|------|---------|
| **IDE** | STM32CubeIDE |
| **Framework** | STM32 HAL (Hardware Abstraction Layer) |
| **Language** | Embedded C |
| **Programmer** | ST-LINK V2 |
| **Target MCU** | STM32F407 (Black Pill / Discovery) |

---

## 📘 Learning Path & Projects

Projects follow a natural learning progression — from the simplest GPIO output up through timers, interrupts, and analog peripherals.

| # | Project | Peripheral / Concept | What I Learned | Status |
|---|---------|----------------------|----------------|--------|
| 01 | [`LED`](./LED) | GPIO Output — Bare Metal | Register-level GPIO setup; understanding RCC clock enable and ODR | ✅ Done |
| 02 | [`LEDblink`](./LEDblink) | GPIO + SysTick / HAL Delay | HAL_Delay internals, SysTick timer, the embedded "Hello World" | ✅ Done |
| 03 | [`LED_interrupt`](./LED_interrupt) | GPIO + EXTI / NVIC Interrupt | External interrupt lines, NVIC priority, event-driven vs polling | ✅ Done |
| 04 | [`LED_Timer_polling`](./LED_Timer_polling) | TIM Base + UIF Flag Polling | Timer base unit, prescaler & ARR calculation, polling UIF flag | ✅ Done |
| 05 | [`Swtch_LED`](./Swtch_LED) | GPIO Input + Output | Debounce logic, reading IDR, linking input state to output | ✅ Done |
| 06 | [`DAC_Square`](./DAC_Square) | DAC — Square Wave | DAC initialization, output voltage steps, waveform via HAL | ✅ Done |
| 07 | [`DAC_TRIANGLE`](./DAC_TRIANGLE) | DAC — Triangle Wave | Iterative DAC value ramping, waveform shaping fundamentals | ✅ Done |
---

## 🚀 How to Run a Project

### 1. Clone the repository

```bash
git clone https://github.com/Amrutkumarbh/STM32-PROJECTS.git
cd STM32-PROJECTS
```

### 2. Open in STM32CubeIDE

1. Launch **STM32CubeIDE**
2. Go to **File → Open Projects from File System**
3. Browse into the project folder (e.g., `./LED_interrupt`)
4. Click **Finish**
5. **Build:** `Ctrl + B`  →  **Flash:** click **Run** ▶️

> Each project's `.ioc` file can be opened in **STM32CubeMX** to inspect pin/peripheral configuration visually.

---

## 📂 Project Structure

Every project follows the standard STM32CubeIDE layout:

```
ProjectName/
├── Core/
│   ├── Inc/            # Header files (.h)
│   └── Src/            # Source files — main.c + peripheral init
├── Drivers/            # STM32 HAL & CMSIS (auto-generated)
├── .ioc                # CubeMX hardware configuration
└── Theory.md           # Concept notes & learning summary (where available)
```

---

## 📌 Concepts Covered So Far

- ✅ GPIO — Bare-metal output, HAL output, digital input
- ✅ Interrupts — EXTI lines, NVIC configuration, ISR handling
- ✅ Timers — TIM Base unit, prescaler/ARR, UIF polling
- ✅ DAC — Square wave and triangle wave generation
- 🔄 PWM — Timer PWM mode *(in progress)*
- 📋 ADC, UART, I2C, SPI, DMA — *(coming up)*

---

## 📝 Notes

- Each project includes or will include a `Theory.md` file explaining the concept, register-level background, and key takeaways.
- This repo is actively updated as I progress through the learning path.
- Mistakes and iterations are part of the process — commit history reflects real learning, not polished end results.

---

> 💡 *Learning embedded systems one peripheral at a time — from blink to protocol stack.*
