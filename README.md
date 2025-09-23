# RISC-V-Reference-SoC-Tapeout-Program-week1

## Day 1

### Introduction to iverilog Design and Test Bench

Simulator is tool to check my design for adherence to the specification by simulating the design .
Example :-iverilog . When the input changes output is also changes.

Design is the verilog code that has the intended to meet the required functionality and the specification.one thing to note that the design may have one or more primary input and output.

Test bench is also a verilog code that is stimulus to the design to check the fuctionality.Now the test bench consists of three parts stimulus generator(input),design,stimulus observer(output).Test bench does not have any primary input and output.

Now Design and test bench is fed into the iverilog simulator that generates a .vcd file(value change dump) to check the functionality of the design wavefrom we use gtkwave.
