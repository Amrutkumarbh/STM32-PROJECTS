# ⚡ LED Interrupt — Theory & Project Explanation

## 1. What is an Interrupt?

In a normal program, the CPU executes instructions **sequentially** — line by line. But sometimes an external event (like a button press) needs **immediate attention**, regardless of what the CPU is currently doing.

An **Interrupt** is a signal that tells the CPU:
> *"Stop what you're doing, handle this urgent event, then come back."*

### Polling vs Interrupts

| Method | How it works | Drawback |
|---|---|---|
| **Polling** | CPU keeps checking a pin in a loop | Wastes CPU cycles constantly checking |
| **Interrupt** | CPU is notified only when event occurs | Efficient — CPU is free to do other work |

Interrupts are always preferred in **real-time embedded systems** for event-driven responses.

---

## 2. How STM32 Handles External Interrupts (EXTI)

The STM32F407 uses the **EXTI (External Interrupt/Event Controller)** peripheral to detect events on GPIO pins.

### EXTI Line Mapping

Each GPIO pin is mapped to an EXTI line:

| GPIO Pin | EXTI Line |
|----------|-----------|
| Px**0**  | EXTI**0** |
| Px**1**  | EXTI**1** |
| Px**13** | EXTI**13** |
| Px**n**  | EXTI**n** |

> ⚠️ Only **one GPIO port** per EXTI line can be active at a time.  
> e.g., PA0 and PB0 **cannot both** use EXTI0 simultaneously.

### Trigger Types

| Trigger | When interrupt fires |
|---|---|
| **Rising Edge** | Signal goes LOW → HIGH (button pressed, active HIGH) |
| **Falling Edge** | Signal goes HIGH → LOW (button released, active LOW) |
| **Both Edges** | Any change in signal state |

---

## 3. STM32F407G-DISC1 — User Button & LEDs

The DISC1 board has a built-in **User Button (Blue)** connected to **PA0**, which is ideal for testing external interrupts.

| Resource | GPIO Pin | Notes |
|---|---|---|
| 🔵 User Button | **PA0** | Active HIGH — reads HIGH when pressed |
| 🟢 Green LED | **PD12** | Active HIGH output |
| 🟠 Orange LED | **PD13** | Active HIGH output |
| 🔴 Red LED | **PD14** | Active HIGH output |
| 🔵 Blue LED | **PD15** | Active HIGH output |

---

## 4. Interrupt Flow on STM32

When a button press is detected on PA0, here's what happens step by step:

```
Button Pressed (PA0 goes HIGH)
         │
         ▼
   EXTI Line 0 detects Rising Edge
         │
         ▼
   NVIC (Nested Vector Interrupt Controller)
   checks priority and forwards interrupt
         │
         ▼
   CPU suspends current task
         │
         ▼
   Jumps to ISR: EXTI0_IRQHandler()
         │
         ▼
   HAL calls: HAL_GPIO_EXTI_Callback(GPIO_PIN_0)
         │
         ▼
   Your code runs (toggle LED, set flag, etc.)
         │
         ▼
   ISR returns → CPU resumes previous task
```

---

## 5. Key Hardware Block: NVIC

The **NVIC (Nested Vector Interrupt Controller)** is the STM32's interrupt manager. It:

- Decides **which interrupt to service** when multiple occur simultaneously (based on priority)
- Allows **interrupt nesting** — a higher priority interrupt can interrupt a lower priority ISR
- Each interrupt has a configurable **preemption priority** and **sub-priority**

In CubeMX/HAL, NVIC priority is set as:

```c
HAL_NVIC_SetPriority(EXTI0_IRQn, 0, 0);  // Highest priority
HAL_NVIC_EnableIRQ(EXTI0_IRQn);           // Enable the interrupt
```

---

## 6. Code Walkthrough

### Step 1 — Configure PA0 as Interrupt Input

```c
GPIO_InitStruct.Pin  = GPIO_PIN_0;
GPIO_InitStruct.Mode = GPIO_MODE_IT_RISING;  // Trigger on rising edge
GPIO_InitStruct.Pull = GPIO_NOPULL;
HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);
```

### Step 2 — Enable NVIC for EXTI Line 0

```c
HAL_NVIC_SetPriority(EXTI0_IRQn, 0, 0);
HAL_NVIC_EnableIRQ(EXTI0_IRQn);
```

### Step 3 — Configure PD12 as Output (LED)

```c
GPIO_InitStruct.Pin   = GPIO_PIN_12;
GPIO_InitStruct.Mode  = GPIO_MODE_OUTPUT_PP;
GPIO_InitStruct.Pull  = GPIO_NOPULL;
GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);
```

### Step 4 — Main Loop (does other work)

```c
while (1) {
    // CPU free to do other tasks
    // LED toggling is handled entirely by the interrupt
}
```

### Step 5 — Interrupt Callback (fires on button press)

```c
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin) {
    if (GPIO_Pin == GPIO_PIN_0) {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);  // Toggle Green LED
    }
}
```

> `HAL_GPIO_EXTI_Callback()` is a **weak function** defined in the HAL library. You **override** it in your code to define what happens when the interrupt fires.

---

## 7. What is a Weak Function?

In STM32 HAL, many callback functions are declared as `__weak`:

```c
__weak void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin) {
    // Default: does nothing
}
```

- If you **don't define it** → the weak (empty) version runs — nothing happens
- If you **define it** in your `main.c` → your version overrides the weak one
- This is a clean way for HAL to provide optional hooks without forcing you to implement them

---

## 8. Interrupt vs Polling — Side-by-Side

```
POLLING (inefficient)              INTERRUPT (efficient)
─────────────────────              ──────────────────────
while(1) {                         while(1) {
  if(button pressed) {               // CPU does useful work here
    toggle LED;                      // LED toggled automatically
  }                                  // when button is pressed
}                                  }
// CPU stuck checking              // CPU free, ISR handles event
```

---

## 9. Block Diagram

```
  STM32F407G-DISC1
  ┌──────────────────────────────────────┐
  │                                      │
  │  PA0 ◄── [User Button]               │
  │   │                                  │
  │  EXTI0 (Rising Edge Trigger)         │
  │   │                                  │
  │  NVIC ──► CPU (suspends main)        │
  │   │                                  │
  │  HAL_GPIO_EXTI_Callback()            │
  │   │                                  │
  │  PD12 ──► [Green LED]  Toggle        │
  │                                      │
  └──────────────────────────────────────┘
```

---

## 10. Key Concepts Summary

| Concept | Description |
|---|---|
| **Interrupt** | Signal that pauses the CPU to handle an urgent event |
| **EXTI** | STM32 peripheral that detects GPIO edge events |
| **NVIC** | Manages interrupt priorities and dispatching |
| **ISR** | Interrupt Service Routine — the function that runs on interrupt |
| **HAL_GPIO_EXTI_Callback** | HAL hook called inside the ISR — override to write your logic |
| **Weak Function** | Default empty implementation that you can override |
| **Rising Edge** | LOW → HIGH transition — used for active HIGH button (PA0) |
| **Polling** | Continuously checking a pin — inefficient compared to interrupts |

---

*Board: STM32F407G-DISC1 | IDE: STM32CubeIDE | Library: STM32 HAL*
