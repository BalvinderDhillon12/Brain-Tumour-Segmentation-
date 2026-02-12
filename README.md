# EMS700U_Code
This code was made for a brain subregion segmentation pipeline with integrated spatial analysis.


# Control Architecture

# Control Architecture

flowchart TB

%% =============================
%% LAYER 1 — HARDWARE INPUTS
%% =============================

subgraph HW["Hardware Layer"]
direction LR
LDR([LDR — RA3])
BTN([Button — RF2])
TMR([Timer0 Peripheral])
end

%% =============================
%% LAYER 2 — DRIVERS / ISR
%% =============================

subgraph DRV["Driver / ISR Layer"]
direction LR
ADC[ADC Driver<br/>ADC.c]
BUTTON[Button Driver<br/>Buttons.c]
TIMER[Timer ISR<br/>Timer.c]
end

LDR -->|Analog| ADC
BTN -->|Digital| BUTTON
TMR -->|Interrupt| TIMER

%% =============================
%% LAYER 3 — MAIN APPLICATION
%% =============================

subgraph MAIN["Main Application — Superloop (Main.c)"]
direction TB

CAL[Calibration<br/>Light/Dark Threshold]

subgraph LOOP["Main Control Flow"]
direction TB
TIME[Timekeeping<br/>HH:MM:SS]
DST[DST & Calendar Check]
SENSE[LDR Sampling<br/>32-sample average]
DECIDE{Dark AND<br/>NOT 1–5am?}
OUT[Update Outputs<br/>LEDs + LCD]
end

CAL --> TIME
TIME --> DST --> SENSE --> DECIDE
DECIDE -->|Yes| OUT
DECIDE -->|No| OUT
end

%% =============================
%% SUPPORT MODULES
%% =============================

subgraph SUPPORT["Supporting Modules"]
direction LR
CALMOD[Calendar.c<br/>Leap year · UK BST]
CFG[Config.h<br/>Energy-save · Timers]
end

DST -.-> CALMOD
TIME -.-> CFG

%% =============================
%% OUTPUT LAYER
%% =============================

subgraph OUTPUT["Hardware Outputs"]
direction LR

subgraph LEDS_DRV["LED Driver — LEDS.c"]
direction TB
L1[Binary Clock LEDs]
L2[Heartbeat LED]
L3[Streetlight LED]
end

subgraph LCD_DRV["LCD Driver — LCD.c"]
direction TB
R0[Time + BST/GMT]
R1[Date DD/MM/YYYY]
end

end

OUT --> LEDS_DRV
OUT --> LCD_DRV

%% =============================
%% STYLING
%% =============================

style HW fill:#f8f9fa,stroke:#333
style DRV fill:#e3f2fd,stroke:#1565c0
style MAIN fill:#e8f5e9,stroke:#2e7d32
style SUPPORT fill:#fff3e0,stroke:#ef6c00
style OUTPUT fill:#f3e5f5,stroke:#6a1b9a

