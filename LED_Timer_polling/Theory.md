# ⏱️ LED Timer Polling — Theory & Project Explanation

## 1. What is a Timer?

A **Timer** is a hardware counter inside the microcontroller that counts clock pulses independently of the CPU. Once it reaches a set value, it can:

- Set a **flag** (which the CPU can check)
- Trigger an **interrupt**
- Generate a **PWM signal**
- **Capture** an external signal's timing

Timers allow **precise time-based operations** without using software delays like `HAL_Delay()`, which block the CPU entirely.

---

## 2. HAL_Delay() vs Timer Polling vs Timer Interrupt

| Method | How it works | CPU during wait | Use case |
|---|---|---|---|
| `HAL_Delay()` | SysTick-based blocking delay | **Blocked** — doing nothing | Simple, quick prototyping |
| **Timer Polling** | CPU checks timer flag in a loop | **Busy** — checking flag | Timed tasks without interrupts |
| **Timer Interrupt** | Timer fires ISR automatically | **Free** — doing other work | Real-time, efficient systems |

> In this project, **Timer Polling** is used — the CPU actively watches the timer's overflow flag and toggles the LED when it fires.

---

## 3. STM32F407 Timer Overview

The STM32F407 has multiple general-purpose and advanced timers:

| Timer | Type | Max Resolution | Notes |
|---|---|---|---|
| TIM1, TIM8 | Advanced | 16-bit | PWM, motor control |
| TIM2, TIM5 | General | **32-bit** | Long duration timing |
| TIM3, TIM4 | General | 16-bit | Most common for LED/PWM |
| TIM6, TIM7 | Basic | 16-bit | Simple time base only |

In this project, a **general-purpose timer** (commonly TIM2 or TIM3) is used as a time base to blink an LED.

---

## 4. How a Timer Works — Key Registers

A STM32 timer works by counting up from **0** to a reload value called **ARR (Auto-Reload Register)**:

```
Counter:   0 → 1 → 2 → ... → ARR → 0 (overflow/update event) → repeat
```

Two values control the blink period:

### Prescaler (PSC)
Divides the input clock to slow it down:

```
Timer Clock = APB Clock / (PSC + 1)
```

### Auto-Reload Register (ARR)
The counter resets and triggers an **Update Event (UEV)** when it reaches ARR:

```
Update Event Period = (PSC + 1) × (ARR + 1) / APB_Clock
```

### Example — 1 second delay (APB1 = 84 MHz for TIM3):

```
PSC = 8399  →  Timer Clock = 84,000,000 / (8399+1) = 10,000 Hz (10 kHz)
ARR = 9999  →  Period = 10,000 / (9999+1) = 1 second
```

---

## 5. What is Polling?

**Polling** means the CPU repeatedly checks the status of a flag in a loop:

```c
while (1) {
    if (flag is set) {
        do the task;
        clear the flag;
    }
}
```

In **Timer Polling**, the CPU keeps checking the timer's **Update Flag (UIF)** inside the SR (Status Register):

```c
while (1) {
    if (__HAL_TIM_GET_FLAG(&htim3, TIM_FLAG_UPDATE)) {        // Is timer done?
        __HAL_TIM_CLEAR_FLAG(&htim3, TIM_FLAG_UPDATE);        // Clear the flag
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);               // Toggle LED
    }
}
```

> ⚠️ The flag **must be cleared manually** after reading — otherwise it stays set and triggers again immediately.

---

## 6. Code Walkthrough

### Step 1 — Configure the Timer in CubeMX / HAL Init

```c
htim3.Instance               = TIM3;
htim3.Init.Prescaler         = 8399;       // 84MHz / 8400 = 10kHz
htim3.Init.CounterMode       = TIM_COUNTERMODE_UP;
htim3.Init.Period             = 9999;      // 10kHz / 10000 = 1Hz → 1 sec
htim3.Init.ClockDivision      = TIM_CLOCKDIVISION_DIV1;
htim3.Init.AutoReloadPreload  = TIM_AUTORELOAD_PRELOAD_DISABLE;
HAL_TIM_Base_Init(&htim3);
```

### Step 2 — Start the Timer

```c
HAL_TIM_Base_Start(&htim3);   // Start timer WITHOUT interrupt
```

> Note: `HAL_TIM_Base_Start()` starts the timer in **polling mode**.  
> `HAL_TIM_Base_Start_IT()` would start it in **interrupt mode** instead.

### Step 3 — Poll the Timer Flag in Main Loop

```c
while (1) {
    // Wait until timer update event fires (every 1 second)
    if (__HAL_TIM_GET_FLAG(&htim3, TIM_FLAG_UPDATE) != RESET) {
        __HAL_TIM_CLEAR_FLAG(&htim3, TIM_FLAG_UPDATE);       // Clear flag
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);              // Toggle LED
    }
    // Other tasks can go here (non-blocking)
}
```

---

## 7. Timer Polling vs HAL_Delay — Why Bother?

```
HAL_Delay(1000):                   Timer Polling:
──────────────────                 ──────────────────────
while(1) {                         while(1) {
  HAL_GPIO_Toggle(...);              if(timer_flag_set) {
  HAL_Delay(1000);  ← CPU STUCK       toggle LED;
  // Nothing runs                      clear flag;
  // during 1 second                 }
}                                    // Other code CAN run here
                                   }
```

With Timer Polling, the `while(1)` loop keeps running — other tasks (reading sensors, updating a display) can execute alongside the LED toggle logic. The CPU is **not blocked**.

---

## 8. Timer Clock Source — Where Does the Clock Come From?

```
HSE (8 MHz crystal on DISC1)
        │
        ▼
    PLL Multiplier
        │
        ▼
  SYSCLK = 168 MHz
        │
        ▼
  AHB Prescaler (/1) → HCLK = 168 MHz
        │
        ▼
  APB1 Prescaler (/4) → PCLK1 = 42 MHz
        │
        ▼
  TIM2–TIM7 Clock = PCLK1 × 2 = 84 MHz  ← Timer input clock
```

> Always verify your timer's APB bus in CubeMX. TIM3/TIM4 are on **APB1 (×2 = 84 MHz)**, while TIM1/TIM8 are on **APB2 (×2 = 168 MHz)**.

---

## 9. Block Diagram

```
  STM32F407G-DISC1
  ┌─────────────────────────────────────────┐
  │                                         │
  │  APB1 Clock (84 MHz)                    │
  │       │                                 │
  │  TIM3 PSC=8399 → 10 kHz                 │
  │       │                                 │
  │  TIM3 ARR=9999 → UEV every 1 sec        │
  │       │                                 │
  │  UIF Flag SET ◄─────────────────┐       │
  │       │                         │       │
  │  CPU polls flag in while(1)      │       │
  │       │                         │       │
  │  Flag detected → Clear UIF ─────┘       │
  │       │                                 │
  │  PD12 Toggle → [Green LED] ON/OFF       │
  │                                         │
  └─────────────────────────────────────────┘
```

---

## 10. Key Concepts Summary

| Concept | Description |
|---|---|
| **Timer** | Hardware counter that runs independently of the CPU |
| **PSC (Prescaler)** | Divides input clock to set timer tick frequency |
| **ARR (Auto-Reload)** | Counter limit — triggers Update Event on overflow |
| **UIF (Update Interrupt Flag)** | Set by hardware when counter overflows ARR |
| **Polling** | CPU actively checks the UIF flag in a loop |
| **HAL_TIM_Base_Start()** | Starts timer in polling mode (no interrupt) |
| **__HAL_TIM_GET_FLAG()** | Reads the timer's status flag |
| **__HAL_TIM_CLEAR_FLAG()** | Clears the flag — must be done manually after detection |
| **Non-blocking** | Unlike `HAL_Delay()`, timer polling allows other code to run |

---

*Board: STM32F407G-DISC1 | IDE: STM32CubeIDE | Library: STM32 HAL*
