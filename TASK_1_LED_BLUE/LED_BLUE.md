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
|To view available files|```ls```|
|Clean Previous Builds|```sudo make clean```|
|Execute below command to build the FPGA|```sudo make build```
|Flash the FPGA|```sudo make flash```|
## PCF File Mapping with FPGA Board
### PCF File
    set_io  led_red	39
    set_io  led_blue 40
    set_io  led_green 41
    set_io  hw_clk 20
    set_io  testwire 17
Referring to VSD Squadron FPGA Mini Datasheet.We could understand the PCF File.
![image]()



