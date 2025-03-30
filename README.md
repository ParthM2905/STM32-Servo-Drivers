# STM32-Servo-Drivers
A lightweight and efficient servo motor driver for STM32 microcontrollers using PWM

## **Features**
✅ Simple API for initializing and controlling servo motors  
✅ Supports **0° to 180°** rotation  
✅ Works with STM32 HAL (Hardware Abstraction Layer)  
✅ Uses TIM PWM for precise angle control  

## **Requirements**
- **Microcontroller:** STM32 (Tested on STM32F7)  
- **Library:** STM32 HAL  
- **Timer:** Any TIMx with PWM capability  
- **Frequency:** Standard 50Hz PWM for servos  

## **Installation**
1. Copy `servo_driver.h` and `servo_driver.c` into your STM32 project.  
2. Include the header in your source file:  
   ```c
   #include "servo_driver.h"
   ```
3. Configure **PWM Timer** using STM32CubeMX.  

---

## **PWM Timer Configuration (Important!)**
Servo motors work with a **50Hz PWM frequency** (20ms period). To achieve this, follow these steps:

### **1. Select a Timer and Pin**
- Go to **STM32CubeMX** → **Timers** → Select a timer (e.g., TIM1).  
- Enable a PWM output channel (e.g., `TIM1_CH1` on `PA8`).  

### **2. Calculate Timer Values**
Set the **PWM period** to 20ms (50Hz frequency). Use the formula:

#### **Prescaler (PSC) Calculation:**
```math
PSC = (SystemCoreClock / (TimerClock))
```
For example, if SystemCoreClock = **180 MHz**, we use a TimerClock of **1 MHz**:  
```c
PSC = (180000000 / 1000000) - 1 = 179
```
(Set PSC = **179** in CubeMX)

#### **Auto-Reload Register (ARR) Calculation:**
```math
ARR = (TimerClock / ServoFrequency) - 1
```
For 50Hz PWM:
```c
ARR = (1000000 / 50) - 1 = 19999
```
(Set ARR = **19999** in CubeMX)

### **3. Calculate PWM Duty Cycle for Servo**
The **Pulse Width** for a servo is typically:
- **0° → 0.5ms pulse** (~2.5% duty cycle)  
- **90° → 1.5ms pulse** (~7.5% duty cycle)  
- **180° → 2.5ms pulse** (~12.5% duty cycle)  

To get the correct **CCR (Compare Register) values**:  
```math
CCR = (PulseWidth / PWM_Period) * ARR
```
For example:
- **0° →** `(0.5ms / 20ms) * 19999 ≈ 250`
- **90° →** `(1.5ms / 20ms) * 19999 ≈ 750`
- **180° →** `(2.5ms / 20ms) * 19999 ≈ 1250`

---

## **Usage**
### **1. Initialize Servo**
```c
Servo_t myServo;
Servo_Init(&myServo, &htim1, TIM_CHANNEL_1); // Using TIM1 Channel 1
```

### **2. Set Servo Angle**
```c
Servo_SetAngle(&myServo, 90);  // Move servo to 90°
```

---

## **How It Works**
The driver maps **angles (0° to 180°) to CCR values** dynamically:
```c
ccr_value = SERVO_MIN_CCR + ((angle * (SERVO_MAX_CCR - SERVO_MIN_CCR)) / SERVO_MAX_ANGLE);
```
This ensures **linear movement** between **0° and 180°**.

---

## **Contributions & Issues**
Feel free to **fork**, **contribute**, or **report issues**! 🚀  

---


