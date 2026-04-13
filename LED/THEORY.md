# 💡 LED Blink — Theory & Project Explanation

## 1. What is an LED?

An **LED (Light Emitting Diode)** is a semiconductor device that emits light when current flows through it in the forward direction.

- It has two terminals: **Anode (+)** and **Cathode (−)**
- Current must flow from Anode → Cathode for it to emit light
- It emits light due to **electroluminescence** — when electrons recombine with holes in the semiconductor, energy is released as photons
- Unlike a bulb, it produces **no heat** and has a **very long lifespan**

### Basic LED symbol:
```
    Anode (+)          Cathode (−)
       |                   |
       ▶|——————————————————
     (longer leg)       (shorter leg)
```

---

## 2. LED on STM32F407G-DISC1

The **STM32F407G-DISC1** discovery board has **4 onboard LEDs**, all connected to **Port D**:

| LED Color | GPIO Pin | Description          |
|-----------|----------|----------------------|
| 🟢 Green  | PD12     | User LED (LD4)       |
| 🟠 Orange | PD13     | User LED (LD3)       |
| 🔴 Red    | PD14     | User LED (LD5)       |
| 🔵 Blue   | PD15     | User LED (LD6)       |

These LEDs are **active HIGH** — the LED turns ON when the GPIO pin outputs a **logic HIGH (3.3V)** and turns OFF at **logic LOW (0V)**.

---

## 3. What is a GPIO?

**GPIO (General Purpose Input/Output)** is a digital pin on the microcontroller that can be configured as either:

- **Input** — to read an external signal (button, sensor, etc.)
- **Output** — to drive an external device (LED, relay, buzzer, etc.)

In this project, the GPIO pin is configured as **Output Push-Pull**, which means it can actively drive the pin HIGH (source current) or LOW (sink current).

---

## 4. How the STM32 Controls an LED

The STM32 controls the LED through its **GPIO peripheral**. The process involves:

### Step 1 — Enable the GPIO Clock
The STM32 uses a clock-gating system. Before using any peripheral, its clock must be enabled in the **RCC (Reset and Clock Control)** register.

```c
__HAL_RCC_GPIOD_CLK_ENABLE();  // Enable clock for Port D
```

### Step 2 — Configure the GPIO Pin
The pin is initialized as a **push-pull output** with no pull-up/pull-down resistors.

```c
GPIO_InitStruct.Pin   = GPIO_PIN_12;        // PD12 (Green LED)
GPIO_InitStruct.Mode  = GPIO_MODE_OUTPUT_PP; // Push-Pull output
GPIO_InitStruct.Pull  = GPIO_NOPULL;
GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);
```

### Step 3 — Toggle the Pin in a Loop
The LED blinks by repeatedly setting the pin HIGH, waiting, then setting it LOW.

```c
while (1) {
    HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12, GPIO_PIN_SET);   // LED ON
    HAL_Delay(500);                                         // Wait 500ms
    HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12, GPIO_PIN_RESET); // LED OFF
    HAL_Delay(500);                                         // Wait 500ms
}
```

Or more concisely using toggle:

```c
while (1) {
    HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12); // Toggle LED state
    HAL_Delay(500);                          // Wait 500ms
}
```

---

## 5. What is HAL_Delay()?

`HAL_Delay(ms)` is a **blocking delay** function provided by STM32's HAL (Hardware Abstraction Layer). It uses the **SysTick timer** (a 1ms system tick) to count milliseconds.

- `HAL_Delay(500)` → pauses execution for **500 milliseconds**
- During this time, the CPU does nothing else (blocking)
- For more complex projects, non-blocking delays using **TIM timers** or **RTOS** are preferred

---

## 6. What is HAL_GPIO_TogglePin()?

This HAL function reads the current state of the GPIO output register and **inverts it**:

- If the pin was HIGH → it becomes LOW (LED turns OFF)
- If the pin was LOW → it becomes HIGH (LED turns ON)

Internally it writes to the **GPIOx_BSRR** (Bit Set/Reset Register) of the STM32, which ensures atomic (single-cycle) write operations.

---

## 7. Block Diagram

```
  STM32F407
  ┌─────────────────────┐
  │                     │
  │  RCC  ──► GPIOD CLK │
  │                     │         330Ω (onboard)
  │  GPIOD Pin 12 ──────┼────────[R]────► LED ──► GND
  │  (Push-Pull Output) │
  │                     │
  │  SysTick ──► HAL_   │
  │             Delay() │
  └─────────────────────┘
```

> On the DISC1 board, the current-limiting resistor and LED are already integrated — no external components needed.

---

## 8. Key Concepts Summary

| Concept | Description |
|---|---|
| **GPIO** | Digital I/O pin on the MCU |
| **Push-Pull Output** | Can drive pin HIGH or LOW actively |
| **RCC Clock Enable** | Must be done before using any STM32 peripheral |
| **HAL_GPIO_TogglePin** | Flips the current output state of a pin |
| **HAL_Delay** | Blocking millisecond delay via SysTick |
| **Active HIGH LED** | LED turns ON when pin = 3.3V (logic HIGH) |

---

*Board: STM32F407G-DISC1 | IDE: STM32CubeIDE | Library: STM32 HAL*
