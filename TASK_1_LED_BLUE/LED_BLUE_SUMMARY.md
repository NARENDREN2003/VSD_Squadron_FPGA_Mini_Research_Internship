# **SUMMARY**
## TASK-1 LED BLUE TESTING

This task gives the idea of using gpio from the FPGA Board

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
       .RGB0PWM (1'b0), // Red light is turned OFF by assiging logic 0 ie 1'b0 
       .RGB1PWM (1'b0), // Green light  is turned OFF by assiging logic 0 ie 1'b0
       .RGB2PWM (1'b1), // Blue light is turned ON by assigning logic 1 ie 1'b1 
       .CURREN  (1'b1 ),
       .RGB0    (led_red                                       ), //Actual Hardware connection
       .RGB1    (led_green                                       ),
       .RGB2    (led_blue                                        )
    );
    defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
    defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
    defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";




