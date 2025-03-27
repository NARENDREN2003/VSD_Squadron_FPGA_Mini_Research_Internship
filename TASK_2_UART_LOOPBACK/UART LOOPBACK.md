# **VSDQUADRON_FPGA_MINI_UART_LOOPBACK**
## **UART LOOP BACK**
  UART (Universal Asynchronous Receiver/Transmitter) loopback is a method to test the UART communication by sending data from the TX (transmit) pin back to the RX (receive) pin within the same system.
## **Port Analysis**
The first section of the code specifies the **ports** of the FPGA board.
| **Port Name**   | **Type**  | **Width** | **Description** |
|---------------|---------|---------|----------------|
| `led_red`    | Output  | 1-bit   | Controls **Red LED** (Active High). |
| `led_blue`   | Output  | 1-bit   | Controls **Blue LED** (Active High). |
| `led_green`  | Output  | 1-bit   | Controls **Green LED** (Active High). |
| `uarttx`     | Output  | 1-bit   | **UART TX Line** (Transmits serial data). |
| `uartrx`     | Input   | 1-bit   | **UART RX Line** (Receives serial data). |
| `hw_clk`     | Input   | 1-bit   | **FPGA system clock input**. |

---
## **Internal Component Analysis**
The module consists of **three main internal components**:
---
### **Internal Oscillator (`SB_HFOSC`)**
    SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC (
    .CLKHFPU(1'b1),
    .CLKHFEN(1'b1),
    .CLKHF(int_osc));
- **Purpose**: Generates a stable internal clock signal.
- **Configuration**: Uses `CLKHF_DIV = "0b10"` (binary 2) for clock division.
- **Control Signals**:
  - `CLKHFPU = 1'b1`: Enables **power-up**.
  - `CLKHFEN = 1'b1`: Enables **oscillator**.
  - `CLKHF`: Output connected to the **`int_osc`** signal.
---
### **Frequency Counter Logic**
    reg [27:0] frequency_counter_i;
    always @(posedge int_osc) 
      begin
        frequency_counter_i <= frequency_counter_i + 1'b1;
      end
| **Component** | **Functionality** |
|-------------|----------------|
| `frequency_counter_i` (28-bit) | **Counter for timing operations**. |
| Increments on `posedge int_osc` | Used for **time-based events** in FPGA. |
| `uartrx` signal used as input | Affects LED behavior. |

- This **counter** is driven by the **internal oscillator (`int_osc`)**.
- The counter value can be used for **timing adjustments** in the design.
---
### **UART TX 8N1 (Transmitter)**
    module uart_tx_8n1 (
          input wire clk,        // System clock
          input wire [7:0] txbyte, // 8-bit data input
          input wire senddata,   // Transmission trigger
          output reg txdone,     // Transmission completion flag
          output wire tx         // UART TX output);
          
| **Component** | **Functionality** |
|-------------|----------------|
| `txbit` | Stores the **current bit being transmitted**. |
| `buf_tx` | **Shift register** that holds UART data. |
| `bits_sent` | **Tracks how many bits have been transmitted**. |
#### **FSM State Definitions**
```verilog
parameter STATE_IDLE    = 8'd0;  // The UART transmitter is waiting for data (senddata signal) before starting transmission.
parameter STATE_STARTTX = 8'd1;  // Sends a Start Bit (0) to indicate the beginning of transmission.
parameter STATE_TXING   = 8'd2;  // Transmits the 8-bit data, one bit per clock cycle.
parameter STATE_TXDONE  = 8'd3;  // Sends a Stop Bit (1) to signal the end of the frame.
```
#### **UART Transmission Logic**
    always @(posedge clk) 
    begin
           if (senddata == 1 && state == STATE_IDLE)
              begin
                 state <= STATE_STARTTX;
                 buf_tx <= txbyte;
                 txdone <= 1'b0;
              end
           else if (state == STATE_IDLE)
              begin
                 txbit <= 1'b1;
                 txdone <= 1'b0;
              end
           if (state == STATE_STARTTX) 
              begin
                 txbit <= 1'b0; // Start bit
                 state <= STATE_TXING;
              end
           if (state == STATE_TXING && bits_sent < 8'd8)
              begin
                 txbit <= buf_tx[0]; // Send LSB first
                 buf_tx <= buf_tx >> 1;
                 bits_sent <= bits_sent + 1;
              end 
           else if (state == STATE_TXING) 
              begin
                 txbit <= 1'b1; // Stop bit
                 bits_sent <= 8'b0;
                 state <= STATE_TXDONE;
              end

           if (state == STATE_TXDONE)
              begin
                 txdone <= 1'b1;
                 state <= STATE_IDLE;
              end
    end
---
### *What This Does*
- **`STATE_IDLE`** → Waits for `senddata`.  
- **`STATE_STARTTX`** → Sends start bit (`0`).  
- **`STATE_TXING`** → Sends 8-bit data (`LSB first`).  
- **`STATE_TXDONE`** → Sends stop bit (`1`), marks completion.  
---
# **STEPS TO IMPLEMENT UART LOOPBACK USING MINICOM IN LINUX TERMINAL**
## 1. Install Minicom (If Not Installed)
Run below commands:

        sudo apt update && sudo apt install minicom -y  # Debian/Ubuntu 
        sudo yum install minicom -y                     # CentOS/RHEL
        sudo pacman -S minicom                          # Arch
        
## 2. Identify the FPGA Serial Port
Before connecting the FPGA, list existing serial ports:

      ls /dev/ttyUSB* /dev/ttyS* 2>/dev/null
Then, plug in the FPGA and run the command again:

      ls /dev/ttyUSB* /dev/ttyS* 2>/dev/null
Then new entry (e.g., /dev/ttyUSB0) is your FPGA's serial port.

To confirm:

      dmesg | grep tty
      
## 3. Launch Minicom
Run:

    sudo minicom -s
(The -s flag opens the setup menu)

## 4. Configure Minicom
In the setup menu:
- Select "Serial port setup".
- Configure:
  - A – Serial Device: /dev/ttyUSB0 (or /dev/ttySx if using a hardware serial port)
  - E – Bps/Par/Bits: 9600 8N1
  - F – Hardware Flow Control: No
  - G – Software Flow Control: No
- Press Enter to go back.
- Select "Save setup as dfl" (to keep settings for next time).
- Select "Exit".
## 5.Send and Receive Data
Once Minicom starts:
- Type any characters—they should echo back if your FPGA loopback works.
- If no data is displayed, check FPGA wiring and settings.
## 6.Exit Minicom
To exit:
- Press Ctrl + A, then X
- Confirm by pressing Enter
--------
### To confirm that the FPGA is correctly echoing back the data in minicom, follow these steps:
1. Enable Local Echo (Optional)
   - By default, minicom does not show what you type. If your loopback isn't working, it might appear as if nothing is happening.
   - To enable local echo:
     - Press Ctrl + A, then E
   - This will toggle local echo ON, allowing you to see what you type.
   - However, if your loopback works correctly, you should see each character appear twice (once from your typing and once from the FPGA).

2. Check for Received Data
   - If loopback is working, when you type Hello, you should see:
         HelloHello
(Because the FPGA sends the same data back.)

   - If loopback is NOT working, you will only see:
  Hello
(Only what you typed, no response from FPGA.)

3. Debugging If No Data is Echoed Back
   - Check the Serial Port ```ls /dev/ttyUSB* /dev/ttyS* ```
   - Ensure you selected the correct device (/dev/ttyUSB0 or /dev/ttySx) in minicom.
   - Check Minicom Settings
     - In minicom (Ctrl + A, then O → "Serial port setup"):
   - Serial Device: ```/dev/ttyUSB0``` (or correct port)
   - Baud Rate: 9600
   - Data Bits: 8
   - Parity: None
   - Stop Bits: 1
   - Flow Control: None
   - Check FPGA Wiring
     - Ensure FPGA TX is connected to RX and RX is connected to TX.
   - If using an external USB-to-serial adapter, verify the connections.
     - Test with Another Terminal
   - If the loopback works, you should see Test printed back.
4. Using Minicom's Capture Log (Optional)
   - If you want to log incoming data:Press Ctrl + A, then L
   - Choose a file to save the log
   - Stop logging with Ctrl + A, then L again

------
### **UART LOOPBACK Implementation**
#### **How It Works**
- Instead of using a **separate UART receiver (`uart_rx_8n1`)**, the design **directly loops back the received signal (`uartrx`) to `uarttx`**.
- This acts as a **wire-based echo loopback**, useful for **testing UART communication**.

#### **Loopback Code in Verilog**
```
assign uarttx = uartrx;
```

## **RGB LED Driver (`SB_RGBA_DRV`)**
```
SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1),
    .RGB0PWM (uartrx),
    .RGB1PWM (uartrx),
    .RGB2PWM (uartrx),
    .CURREN  (1'b1),
    .RGB0    (led_green),
    .RGB1    (led_blue),
    .RGB2    (led_red)
);
```
- **Controls RGB LEDs using UART signals**.
- **Fixed brightness configuration**:
  - `RGB0_CURRENT = "0b000001"`
  - `RGB1_CURRENT = "0b000001"`
  - `RGB2_CURRENT = "0b000001"`

---

## **Module Documentation (Summary)**

### **Purpose**
This module **implements a UART transmitter** and **RGB LED controller** using:
1. **Internal Oscillator (`SB_HFOSC`)** for clocking.
2. **Finite State Machine (FSM)** for UART TX control.
3. **Shift Register (`buf_tx`)** for serial transmission.
4. **Counter Logic (`frequency_counter_i`)** for timing.

### **UART TX Working**
- Transmits **8-bit data (LSB first)**.
- Uses **start (0) and stop (1) bits**.
- Sends **serial data on `uarttx` pin**.

### **RGB LED Control**
- **Uses UART RX (`uartrx`) to drive LEDs**.
- **Changes LED color based on UART activity**.

---
# **UART LOOPBACK Block Diagram**

## **What is UART?**
**UART (Universal Asynchronous Receiver-Transmitter)** is a serial communication protocol used for **asynchronous data transfer** between devices. It does not require a separate clock signal; instead, it uses a **baud rate** to synchronize communication.

## **What is UART Loopback Mode?**
**UART loopback** is a special mode where the **transmitted (`TX`) data is directly routed to the receiver (`RX`)**, allowing self-testing without external connections.

### **Why Use Loopback Mode?**
 **Debugging UART Transmission** → Ensures data is sent and received correctly.  
 **No External Hardware Required** → TX is internally connected to RX.  
 **Self-Testing** → Helps verify UART functionality in an FPGA or microcontroller.

## **UART LOOPBACK Block Diagram**
![UART Loopback](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/6f9a53abb44dcfa19269b258651a8765581b1433/TASK_2_UART_LOOPBACK/DIRECT%20CONNECTION.jpg)

## **Explanation of Each Block**

### **1. UART Transmitter**
- This block is responsible for **sending serial data**.
- It converts **parallel 8-bit data** into a **serial bitstream**.
- Generates **Start Bit (`0`), Data Bits (LSB First), and Stop Bit (`1`)**.

### **2. Direct Connection (Loopback)**
- Instead of sending data to an external device, **TX (`uarttx`) is directly connected to RX (`uartrx`)**.
- Implements a **hardware loopback**, allowing data sent from TX to be immediately received by RX.

### **3. UART Receiver**
- Reads the **serial data from TX** via the direct connection.
- Reconstructs the original **8-bit parallel data**.
- This allows **testing UART transmission without an external receiver**.
---
## **Loopback Implementation in Verilog**
### **Direct Connection Logic**
```
assign uarttx = uartrx;
```
- Any data sent on uarttx is instantly received on uartrx.
 - This eliminates the need for external connections during testing.
 - Helps debug UART transmission in an FPGA-based system.

# **UART Loopback Circuit Diagram**
![Circuit Diagram](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/86b958cdadc723d7590eef75a0361e684ee1a21f/TASK_2_UART_LOOPBACK/CIRCUIT%20DIAGRAM.jpg)

## **🔹 Key Components**
- **FPGA (VSD Squadron Mini):** Handles UART communication & LED control.
- **FTDI Cable:** Provides USB-to-UART communication.
- **RGB LED Driver (`SB_RGBA_DRV`):** Controls LEDs based on UART input.
- **High-Frequency Oscillator (`SB_HFOSC`):** Generates clock signals.
- **Frequency Counter:** Derives a **9600 Hz baud rate clock**.

## **🔹 Circuit Connections**
| **Component**  | **FPGA Pin** | **Function** |
|--------------|------------|----------------|
| **UART TX**  | Pin 14     | Sends data (Connected to RX) |
# Testing and Output 

## Clone & Setup Repository
```bash
git clone 
cd "VsdSquadron_mini_fpga_uart_loopback"
```
Build & Flash FPGA Bitstream
###  Build the Bitstream
```bash
make build
```
 Generates top.bin for the FPGA.

###  **Flash to FPGA**
```bash
sudo make flash
Uploads the bitstream to the FPGA.
```
### **UART Loopback Testing**
Open Serial Terminal
```bash
sudo picocom -b 9600 /dev/ttyUSB0 --echo
```
 Send Data & Verify Output

# Expected Output:

Sent Data (TX)   | Received Data (RX)
-----------------|------------------
A               | AA
hello           | hheelllloo

Exit Terminal
CTRL + A, then CTRL + X
### Troubleshooting

1. Check FPGA pin connections.

2. Ensure baud rate is set to 9600.

3. Verify FTDI cable is properly connected.

|               |              |                                  |
| ------------- | ------------ | -------------------------------- |
| **Component** | **FPGA Pin** | **Function**                     |
| **UART RX**   | Pin 15       | Receives data (Loopback from TX) |
| **FTDI TX**   | Pin 14       | Sends data to FPGA RX            |
| **FTDI RX**   | Pin 13       | Receives data from FPGA TX       |
| **RGB LEDs**  | PWM Output   | Controlled via UART signals      |
## 5.Demo video
[Demo Video](https://github.com/user-attachments/assets/60dddb70-100b-40ed-82e3-6ac9a7fb49c8)
