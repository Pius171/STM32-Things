# Sample using Arduino JSON
* In any directory in your computer clone the arduino json library. I just used the folder of my stm32 project
* In STM32Cube IDE navigate to

---

### 🔹 Step 1. Download ArduinoJson

1. Go to the official repo: [https://github.com/bblanchon/ArduinoJson](https://github.com/bblanchon/ArduinoJson)
2. Download the ZIP of the library (or clone it).
3. Extract it somewhere on your computer. I just used my stm32 project directory

---

### 🔹 Step 2. Add ArduinoJson to Your Project

1. In **STM32CubeIDE**, right-click your project in the **Project Explorer**.
2. Select **Properties → C/C++ General → Paths and Symbols**.
3. Under the **Includes** tab, add the path to the `ArduinoJson/src` folder.

   For example, if you placed the library in:

   ```
   C:\STM32Libs\ArduinoJson
   ```

   then add:

   ```
   C:\STM32Libs\ArduinoJson\src
   ```

   as an include path.

---

### 🔹 Step 3. Enable C++ in Your Project

ArduinoJson is a **C++ library**, so make sure your STM32 project supports C++:

1. Right-click your project → **Properties → C/C++ Build → Settings**.
2. In **Tool Settings**, under **MCU GCC Compiler**, make sure you’re compiling as C++.
3. If your project was created as "C", you can enable C++ by:

   * Renaming `main.c` to `main.cpp`.
   * Adjusting CubeIDE to compile with `g++` for that file.

---

### 🔹 Step 4. Include ArduinoJson in Code

Now you can use it like normal:

```cpp
#include <ArduinoJson.h>

// Example: serialize to JSON
void sendData() {
    StaticJsonDocument<200> doc;
    doc["temperature"] = 25.3;
    doc["status"] = "ok";

    char buffer[200];
    serializeJson(doc, buffer);
    printf("%s\n", buffer);
}
```

---

### 🔹 Step 5. Handle `printf` / Serial

Since STM32 doesn’t have Arduino’s `Serial`, you’ll probably:

* Use `printf` (redirected to UART, SWO, or USB CDC).
* Or use `HAL_UART_Transmit()` to send the JSON buffer.

---

✅ That’s it! Now ArduinoJson should compile and run fine inside STM32CubeIDE.

