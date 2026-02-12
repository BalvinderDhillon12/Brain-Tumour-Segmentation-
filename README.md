# EMS700U_Code
This code was made for a brain subregion segmentation pipeline with integrated spatial analysis.


# Control Architecture

```mermaid
flowchart TD
    subgraph HW_IN["HARDWARE INPUTS"]
        LDR["LDR (RA3, ADC Ch3)"]
        BTN["RF2 Button"]
        TMR["Timer0 (16-bit)"]
    end

    subgraph DRIVERS["PERIPHERAL DRIVERS"]
        ADC["ADC Driver (ADC.c)"]
        BUTTON["Button Driver (Buttons.c)"]
        TIMER["Timer ISR (Timer.c)"]
    end

    LDR -->|analog| ADC
    BTN -->|digital| BUTTON
    TMR -->|overflow IRQ| TIMER

    subgraph MAIN["MAIN CONTROL LOOP (Main.c)"]
        direction TB
        CAL["1. CALIBRATION PHASE\nDark sample → Light sample\n→ Compute threshold & polarity"]
        subgraph LOOP["2. SUPER-LOOP"]
            A["a) TIMEKEEPING — Advance HH:MM:SS"]
            B["b) DST CHECK — Spring fwd / fall back"]
            C["c) LDR POLL — 32-avg → isDark"]
            D["d) LIGHT DECISION — dark AND NOT 1am–5am"]
            E["e) OUTPUT UPDATE — LEDs + LCD"]
            F["f) HEARTBEAT — Toggle every 2s"]
            A --> B --> C --> D --> E --> F
        end
        CAL --> LOOP
        SUP1["Calendar.c — dates, leap year, UK BST"]
        SUP2["Config.h — timers, energy-save, mode"]
    end

    ADC --> MAIN
    BUTTON --> MAIN
    TIMER --> MAIN

    subgraph HW_OUT["HARDWARE OUTPUTS"]
        subgraph LEDS["LED Driver (LEDS.c)"]
            L1["LEDs 1–5 : Binary hour clock"]
            L2["LED 8 : Heartbeat"]
            L3["LED 9 : Main streetlight"]
        end
        subgraph LCD["LCD Driver (LCD.c)"]
            R0["Row 0 : HH:MM AM/PM BST/GMT"]
            R1["Row 1 : DD/MM/YYYY"]
        end
    end

    MAIN --> HW_OUT
```
