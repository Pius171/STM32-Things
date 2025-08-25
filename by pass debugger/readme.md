Perfect ✅ That’s actually a very common workflow when people just want to **flash code** without touching the debugger.
Here’s how to set it up in **STM32CubeIDE**:

---

# 🔹 Step 1: Make sure you can generate a `.bin` file

1. In CubeIDE → Right-click your project → **Properties**.
2. Go to:
   `C/C++ Build → Settings → MCU Post build outputs`
3. Tick **Convert to binary file**.
   (You can also tick “hex” if you prefer, both work with CubeProgrammer.)
4. Click **Apply and Close**.
5. Build your project (`Ctrl+B`).
   → You’ll now find your binary at:

   ```
   <your_project>\Debug\<project_name>.bin
   ```

---

# 🔹 Step 2: Find CubeProgrammer CLI

CubeProgrammer installs a command-line tool at:

```
C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe
```

That’s what we’ll use to upload with ST-LINK.

---

# 🔹 Step 3: Create a “Run Configuration” that uses CLI

Instead of fighting the GDB server, we’ll make CubeIDE call CubeProgrammer CLI directly after build.

1. Right-click your project → **Properties**.
2. Go to:
   `C/C++ Build → Settings → Build Steps`.
3. In **Post-build steps**, add this command (edit path + project name):

```bat
"C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe" -c port=SWD -d "${BuildArtifactFileBaseName}.bin" 0x08000000 -v -rst
```

### Explanation:

* `-c port=SWD` → tells it to use ST-LINK SWD interface.
* `-d file.bin 0x08000000` → download binary to Flash start.
* `-v` → verify.
* `-rst` → reset the MCU after programming.

CubeIDE expands `${BuildArtifactFileBaseName}.bin` automatically (e.g. `MyProject.bin`).

---

# 🔹 Step 4: Build & Flash

* Now, when you **Build Project** (`Ctrl+B`), CubeIDE will compile and then immediately call STM32CubeProgrammer CLI to flash the binary via ST-LINK.
* You don’t need the GDB server at all.
* No more TCP port conflicts 🎉

---
