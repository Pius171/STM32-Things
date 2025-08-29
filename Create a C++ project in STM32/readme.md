# Enable C++ in Your Project

ArduinoJson is a **C++ library**, so make sure your STM32 project supports C++:

1. When creating a new stm32 project select C++
2. After the project has been created right-click your project → **Properties → C/C++ Build → Settings**.
3. In **Tool Settings**, under **MCU GCC Compiler**, make sure you’re compiling as C++.
4. Create two files app.cpp and app.h


By default, STM32CubeMX (inside CubeIDE or standalone) **always generates `main.c`**, because it assumes you’re writing in C. Even if you rename it to `main.cpp`, the next time you click **"Generate Code"** after changing configurations in CubeMX, it will recreate a fresh `main.c`.

---

# ⚡ How to keep using C++ (`main.cpp`) safely


Here’s the **recommended structure**:

---

## 🔹 Project File Layout

```
LORA_NODE_CPP/
 ├─ Core/
 │   ├─ Inc/
 │   │   ├─ main.h              <- CubeMX generates this
 │   │   ├─ app.h               <- you create this
 │   │   └─ ...
 │   └─ Src/
 │       ├─ main.c              <- CubeMX generates this
 │       ├─ app.cpp             <- your application logic
 │       └─ ...
```

---

## 🔹 Step 1. Create `app.h`

Put this in **Core/Inc/app.h**:

```c
#ifndef APP_H
#define APP_H

#ifdef __cplusplus
extern "C" {
#endif

void MyCppApp_Run(void);

#ifdef __cplusplus
}
#endif

#endif // APP_H
```

This ensures your C `main.c` can call into your C++ function without name mangling issues.

---

## 🔹 Step 2. Create `app.cpp`

Put this in **Core/Src/app.cpp**:

```cpp
#include "app.h"
#include "main.h"
#include "stm32l4xx_hal.h"
#include <ArduinoJson.h>
#include <string.h>

// Example: Use HAL + ArduinoJson together
void MyCppApp_Run(void) {
    // Toggle a pin
    HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_0);

    // Build JSON
    StaticJsonDocument<200> doc;
    doc["status"] = "running";
    doc["temperature"] = 25.6;

    char buffer[64];
    serializeJson(doc, buffer);

    // Send over UART2
    HAL_UART_Transmit(&huart2, (uint8_t*)buffer, strlen(buffer), HAL_MAX_DELAY);
}
```

---

## 🔹 Step 3. Call it from `main.c`

In **Core/Src/main.c** (CubeMX generated), add:

```c
#include "app.h"

/* USER CODE BEGIN 2 */
MyCppApp_Run();
/* USER CODE END 2 */
```

This means after CubeMX initializes everything, your C++ app takes over.


