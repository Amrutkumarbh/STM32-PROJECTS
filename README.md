# 🔧 STM32F407 Projects

![STM32](https://img.shields.io/badge/MCU-STM32F407-blue?style=for-the-badge&logo=stmicroelectronics)
![Language](https://img.shields.io/badge/Language-C-orange?style=for-the-badge&logo=c)
![IDE](https://img.shields.io/badge/IDE-STM32CubeIDE-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active%20Daily-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

> A daily log of embedded systems projects built on the **STM32F407 Discovery Board**, covering bare-metal programming, HAL-based drivers, peripherals, RTOS, and more — one project at a time.

---

## 👤 Author

**Amrutkumarbh**  
📅 Started: April 2025  
🔗 GitHub: [@Amrutkumarbh](https://github.com/Amrutkumarbh)

---

## 🧠 About This Repository

This repository is a hands-on learning journal for mastering the **STM32F407VGT6** microcontroller. A new project is added every day, progressing from simple GPIO control to complex real-time systems.

Each project folder is self-contained and includes:
- Source code (`.c` / `.h`)
- Project configuration files
- A dedicated `README.md` explaining the concept

---

| # | Project | Peripheral / Concept | Status |
|---|---|---|---|
| 01 | [LED](./LED) | GPIO Output – Bare Metal | ✅ Done |
| 02 | [LEDblink](./LEDblink) | GPIO + SysTick / HAL Delay | ✅ Done |
| 03 | [LED_interrupt](./LED_interrupt) | GPIO + EXTI / NVIC Interrupt | ✅ Done |
| 04 | [LED_Timer_polling](./LED_Timer_polling) | TIM Base + UIF Flag Polling | ✅ Done |
| 05 | *(Coming soon)* | — | 🔄 In Progress |

> 📌 This table is updated daily as new projects are added.

---

## 🛠️ Hardware & Tools

| Component | Details |
|-----------|---------|
| **Board** | STM32F407G-DISC1 (Discovery Kit) |
| **MCU** | STM32F407VGT6 – ARM Cortex-M4 @ 168 MHz |
| **IDE** | STM32CubeIDE |
| **HAL** | STM32 HAL / LL Drivers |
| **Debugger** | ST-LINK/V2 (onboard) |
| **Language** | C (C11) |

---

## 🚀 Getting Started

### Prerequisites
- [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html) installed
- STM32F407 Discovery Board
- USB Mini-B cable

### Clone the Repository
```bash
git clone https://github.com/Amrutkumarbh/STM32-PROJECTS.git
cd STM32-PROJECTS
```

### Running a Project
1. Open **STM32CubeIDE**
2. Go to `File → Open Projects from File System`
3. Navigate to the desired project folder (e.g., `LEDblink/`)
4. Click **Finish** to import
5. Build with `Ctrl+B` and flash using `Run → Debug`

---

## 📚 Topics Covered (Roadmap)

- [x] GPIO – Digital Output / Blink
- [ ] GPIO – Button Input & Interrupt (EXTI)
- [ ] Timers – Basic, PWM, Input Capture
- [ ] UART – Serial Communication
- [ ] SPI – Sensor Interfacing
- [ ] I2C – OLED / Sensor Driver
- [ ] ADC – Analog Read
- [ ] DMA – Direct Memory Access
- [ ] FreeRTOS – Multitasking Basics
- [ ] CAN Bus
- [ ] USB CDC (Virtual COM Port)

---

## 📁 Repository Structure

```
STM32-PROJECTS/
│
├── LED/                    # 01 - GPIO Output, bare metal LED control
├── LEDblink/               # 02 - GPIO + HAL_Delay() SysTick blink
├── LED_interrupt/          # 03 - GPIO + EXTI + NVIC interrupt-driven LED
├── LED_Timer_polling/      # 04 - TIM Base + UIF flag polling LED toggle
│
├── .gitignore
└── README.md
```

## 🤝 Contributing

This is a personal learning repo, but suggestions and improvements are always welcome!  
Feel free to open an **Issue** or submit a **Pull Request**.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](./LICENSE) file for details.

---
<p align="center">
  ⭐ If you find this helpful, consider giving the repo a star!
</p>
