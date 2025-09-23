# RISC-V-Reference-SoC-Tapeout-Program-week1

## Day 1

### Introduction to iverilog Design and Test Bench

Simulator is tool to check my design for adherence to the specification by simulating the design .
Example :-iverilog . When the input changes output is also changes.

Design is the verilog code that has the intended to meet the required functionality and the specification.one thing to note that the design may have one or more primary input and output.

Test bench is also a verilog code that is stimulus to the design to check the fuctionality.Now the test bench consists of three parts stimulus generator(input),design,stimulus observer(output).Test bench does not have any primary input and output.

Now Design and test bench is fed into the iverilog simulator that generates a .vcd file(value change dump) to check the functionality of the design wavefrom we use gtkwave.

### Labs using iverilog and gtkwave

First in ubuntu terminal do as follows

### (i) Setup

```
mkdir VLSI
cd VLSI
sudo apt-get install git
git clone https://github.com/kunalg123/vsdflow.git
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

Now inside the VLSI diectory there are two files one is vsdflow and the another one is sky130RTLDesignAndSynthesisWorkshop inside this dir there are three files which are lib(which include the standared cell libery),my_lib(standared cell model),verilog_files(.v files along with its test bench)

### (ii) To run the iverilog and the Gtkwave
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
iverilog good_mux.v tb_good_mux.v // a.out is created
./a.out //execute this and the vcd file is generated
gtkwave tb_good_mux.vcd
gvim tb_good_mux.v -o good_mux.v // to see the file
```

### Introduction to yosys

