# VSD_Squadron_FPGA_Mini_Research_Internship_2025
## 
<img src="https://github.com/user-attachments/assets/a8bb3868-4454-48b9-9a43-8db6f9467c5c" alt="VSDSquadron mini fpga" width="400"/>
This repo display projects of the VSD Squadron Mini FGPA board.   


##  About the Board
The **VSD Squadron Mini FPGA** is a beginner-friendly **open-source FPGA board** designed for rapid prototyping and digital system implementation.It is designed completely by VLSI System Design (VSD),an educational organization in the field of semiconductors and VLSI.

### **Board Specifications**
🔹 **FPGA Chip**: Lattice UltraPlus ICE40UP5K (5.3K LUTs)  
🔹 **Memory**: 1MB SPRAM, 120Kb DPRAM, 4MB SPI Flash  
🔹 **General Purpose I/O (GPIO)**: 32 accessible pins  
🔹 **Connectivity**: FTDI FT232H USB-to-SPI for programming & debugging  
🔹 **Multipliers**: 8 DSP multipliers for arithmetic operations  
**[View the Datasheet](https://www.vlsisystemdesign.com/wp-content/uploads/2025/01/VSDSquadronFMDatasheet.pdf)** 
**For more details, visit the official website:** [VSD Official Page](https://www.vlsisystemdesign.com/vsdsquadronfm/)  

---

## 🔹 Projects Overview
## 🔹 FPGA Projects Overview

This repository contains **five FPGA-based projects**, each focusing on different aspects of **digital design, communication, and real-time processing** using the **VSD Squadron Mini FPGA**.

|  | Project Name                                         | Description |
|----|------------------------------------------------------|-------------|
| 1 | [RGB CONTROL](https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship/tree/main/TASK_1_RGB_CONTROL)                  | FPGA RGB CONTROL TEST |


---

##  Setup & Installation
### **Clone the Repository**

After installing the required tools, **clone this repository**:

    git clone https://github.com/NARENDREN2003/VSD_Squadron_FPGA_Mini_Research_Internship.git

Navigate into the repository:

    cd VSD_Squadron_FPGA_Mini_Research_Internship

***


### **Navigate to the Specific Project**

Each project has its own directory. Navigate to the project you want to work on:

Example:

    
    cd TASK_1_RGB_CONTROL

To view available files:

    
    ls
    

***


### **Clean Previous Builds**

Before starting a new build, it preferred to clean old files using the below command in the terminal:

```sh
sudo make clean
```

The above command removes any previous bitstream files.

***


### **Build the FPGA Bitstream**

To ** BUILD AN FPGA **, run:

    sudo make build

***


### **Flash the FPGA**

Once the bitstream is built, upload it to the FPGA using by the below command:

```sh
    sudo make flash
```
This command programs the **VSD Squadron Mini FPGA board**.

***
