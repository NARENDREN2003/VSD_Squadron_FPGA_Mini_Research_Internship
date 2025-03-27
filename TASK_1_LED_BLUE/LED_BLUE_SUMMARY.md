# **SUMMARY**
## TASK-1 LED BLUE TESTING

This task gives the idea of using LED and RGB Driver in the FPGA Board

![image](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/5887bcd90075abacac00760fd1bd77572cdda0c0/TASK_1_LED_BLUE/VSD%20FM%20Board.png)
## Steps to Implement Task-1
| Steps| Commands|
|------|---------|
|Clone this repository using below command| ``` git clone https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship ```|
|Navigate into the repository|```cd VSD_Squadron_FPGA_Mini_Research_Internship```|
|Navigate into the folders in the repo|```cd TASK_1_LED_BLUE```|
|Navigate to Led Blue folder|```cd LED_BLUE```| 
|Clean Previous Builds|```sudo make clean```|
|Execute below command to build the FPGA|```sudo make build```|
|Flash the FPGA|```sudo make flash```|

## PCF File Mapping with FPGA Board
### PCF File
    set_io  led_red	39
    set_io  led_blue 40
    set_io  led_green 41
    set_io  hw_clk 20
    set_io  testwire 17
Referring to VSD Squadron FPGA Mini Datasheet.We could understand the PCF File.

![image](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/7757736328a0c7f9e4fef4faee4fef58f1e9202b/TASK_1_LED_BLUE/GPIO%20PIN%20ASSIGNMENT.png)

From the above image, we could map the LED Fuction RGB0,RGB1,RGB2 to pins 39,40,41  respectively.
## Understanding the RGB Driver in Verilog Code
    SB_RGBA_DRV RGB_DRIVER
    (
       .RGBLEDEN(1'b1), 
       .RGB0PWM (1'b0), 
       .RGB1PWM (1'b0),
       .RGB2PWM (1'b1), 
       .CURREN  (1'b1 ),
       //Actual Hardware connection (Refer to below table ))
       .RGB0    (led_red), 
       .RGB1    (led_green),
       .RGB2    (led_blue)
    );
    defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
    defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
    defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";
![image](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/722a9db7c2c57aefb30bfd592527f0a3a09b8e9a/TASK_1_LED_BLUE/LED%20COLOUR%20ASSIGNMENT.png)
 ### Code Mapping RGB_DRIVER
 |Code                 | Description          |
 |---------------------|-----------------------|
 |```.RGBLEDEN(1'b1),```| Input to the RGB Driver, Enable Control for RGB LED, Active HIGH|
 |```.RGB0PWM (1'b0),```|Input to the RGB Driver, pulse width modulated control signal for controlling RGB0 output.Connects to Embedded PWM IP or FPGA logic, Active LOW .|
 |```.RGB1PWM (1'b0),```|Input to the RGB Driver, pulse width modulated control signal for controlling RGB1 output. Connects to Embedded  PWM IP or FPGA logic, Active LOW |
 |```.RGB2PWM (1'b1),```|Input to the RGB Driver, pulse width modulated control signal for controlling RGB2 output. Connects to Embedded PWM IP or FPGA logic, Active HIGH|
 |```.CURREN  (1'b1 ),```|Input enabling mixed signal control block to supply reference current to RGB driver. Enabling the mixed signal control block takes 100 μs to reach a stable reference current value.|
 |```.RGB0    (led_red),```|Open-drain output of the RGB Driver connected to the device pin for RED LED|
 |```.RGB1    (led_green),```|Open-drain output of the RGB Driver connected to the device pin for GREEN LED|
 |```.RGB2    (led_blue)```|Open-drain output of the RGB Driver connected to the device pin for BLUE LED |
 |```defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";```|Led red of Current 4 mA in Full Mode|
 |```defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";```|Led green of Current 4 mA in Full Mode|
 |```defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";```|Led blue of Current 4mA in Full Mode |
 
Refer [iCE40 UltraPlus RGB Driver.pdf](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/f3ae70937e72c97f05478789337766fa98b225af/TASK_1_LED_BLUE/iCE40%20UltraPlus%20RGB%20Driver.pdf) 
## SB_RGBA_DRV Attribute Description 
    The SB_RGBA_DRV primitive contains the following parameter and their default values: 
    Parameter CURRENT_MODE = “0b0”; 
    Parameter RGB0_CURRENT = “0b000000”; 
    Parameter RGB1_CURRENT = “0b000000”; 
    Parameter RGB2_CURRENT = “0b000000”; 
    Parameter values: 
    “0b0” = Full Current Mode 
    “0b1” = Half Current Mode 
    “0b000000” = 0mA. // Set this value to use the associated SB_IO_OD instance at RGB LED 
    location. 
    “0b000001” = 4 mA for Full Mode; 2 mA for Half Mode 
    “0b000011” = 8 mA for Full Mode; 4 mA for Half Mode 
    “0b000111” = 12 mA for Full Mode; 6mA for Half Mode 
    “0b001111” = 16 mA for Full Mode; 8 mA for Half Mode 
    “0b011111” = 20 mA for Full Mode; 10 mA for Half Mode 
    “0b111111” = 24 mA for Full Mode; 12 mA for Half Mode 
 In the Verilog Code [led_blue](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/blob/main/TASK_1_LED_BLUE/LED_BLUE/top.v) ,4mA Full Current Mode is used.
 ## Understanding Internal Oscillator Code
    ```SB_HFOSC #(.CLKHF_DIV ("0b10"))````
        u_SB_HFOSC (
                    .CLKHFPU(1'b1),
                    .CLKHFEN(1'b1),
                    .CLKHF(int_osc)
                    );```
The above line of code describes the High Frequency  Internal Oscillator is Enabled 
|Parameter|	Value|Description|
|---------|------|-----------|
|CLKHF_DIV|	"0b10"|	Sets output frequency to 12 MHz|
|CLKHFPU|	1'b1	|Enables the oscillator power-up|
|CLKHFEN|   1'b1	|Enables the oscillator output|
|CLKHF  |	int_osc |	Internal clock signal|

 



