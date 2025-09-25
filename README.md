# RISC-V-Reference-SoC-Tapeout-Program-week1

## Day 1

### Introduction to iverilog Design and Test Bench

Simulator is tool to check my design for adherence to the specification by simulating the design .
Example :-iverilog . When the input changes output is also changes.

Design is the verilog code that has the intended to meet the required functionality and the specification.one thing to note that the design may have one or more primary input and output.

Test bench is also a verilog code that is stimulus to the design to check the fuctionality.Now the test bench consists of three parts stimulus generator(input),design,stimulus observer(output).Test bench does not have any primary input and output.

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Test%20Bench.png)

Now Design and test bench is fed into the iverilog simulator that generates a .vcd file(value change dump) to check the functionality of the design wavefrom we use gtkwave.

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Simulation%20flow.png)

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

### Output 

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/good_mux.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/good_mux_tb.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/GTKWave.png)

### Introduction to yosys and logic synthesis

Synthesizer is a tool converting the RTL to netlist(consists of gates,standared cell) Example Yosys.We fed the Design and the .lib file to the yosys it will generate a netlist file. To verify my synthesis we fed the the netlist(have the same primary i/p and o/p as the design ) and the test bench(Which is same as the previous one) to the simulator that will generate a .vcd file that is fed into the gtkwave then match the bothoutput wavefrom.

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Yosys%20setup.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Verify%20synthesis.png)

RTL design is the behavioral representation of the spec.synthesis is the RTL to gate level transition(gates and the connection between the gates) this file is called netlist.

.lib is the collection of gates (and ,or,not,2 i/p,3 i/p,4i/p,slow ,medium, fast)

Now suppose I have this setup so the min of T_clk>=T_A+T_Comb+T_B .so in order to get the faster response(required performance) T should be small (set up time) => max frequency so we need faster cell.To check that there is no hold issues we need the slower cell(T_hold<T_A+T_comb).

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Set%20up%20and%20hold.png)

In digital circuit load can be treated as capacitance. faster the charging discharging lesser the delay so the tr is capable of sourcing more current => Wide transistor.
Wide tr=> less delay,more area and power consuption.
Narrow tr=> More delay,less area and power consuption.so it should be optimum.
So there is some constraints.

### lab using Yosys

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux //module name
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib //generate the netlist
show
write_verilog -noattr good_mux_netlist.v
!gvim good_mux_netlist.v
```

### Output

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Netlist.png)


![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day1/images/Netlist%20code.png)

## Day 2

### Introduction  to timing .lib

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Lib.png)
sky130_fd_sc_hd__tt_025C_1v80 :- 130 refers to the nm tech, tt stand for typical, 025C is the temerature and 1v80 is the voltage. there is a term called PVT(process-variation due to fabrication,voltage,temperature)

.lib  file contain all the information like the technology(cmos),delay model(look up table), unit of power,current,resistence,capacitance,operating condition. there are different types gates in this file.

a2111o=> x=((A1 & A2) | B1 | C1 | D1) for all 32 bit combination what is the power required is already given.

The area of and_2_0 < and_2_2 < and_2_4 ;
The power of and_2_0 < and_2_2 < and_2_4 ;
The delay of and_2_0 > and_2_2 > and_2_4 ;

### Hierarchical synthesis
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim multiple_modules.v
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules_hire.v
!gvim multiple_modules_hire.v
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Multiple%20Modules.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Hire_Synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Hire_Netlist.png)
when a top level module inside this module there are some other module when we synthesis the top level module it is called hierarchical design (hierarchi are preserved) In nand design we ovserved stacked nmos but in the or design we ovserved stacked pmos which is bad as it have the poor mobility.

### Flat Synthesis
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim multiple_modules.v
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show
write_verilog -noattr multiple_modules_flat.v
!gvim multiple_modules_flat.v
exit
```
### Output 
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Flat_synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Flat_Net.png)

If we use the flatten command then do sunthesize the module we get a single netlist there is no hierarchi
### Sub module level sysnthesis
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1 //***
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/Sub%20Module%20Synth.png)
the need of sub module synthesis is that suppose there are multiple instantiation of that same module so we synthesis one time and copy that, and the another reason is that if tha design have very massive size so we synthesize part by part to get the clean netlist(D & C)

### FLIP FLOP
In cobinational ckt due to propagation delay there is some glitch. so we want a element to store that(flip flop). so to overcome the glitch or to settle down the value we use flops. To initialize the flop we use reset/set(sync,async)

Asynchronous reset mens irrspective of the clock we the async_reset goes high o/p is 0 and the poseedge come q<=d. And Asynchronous set mens irrspective of the clock we the async_set goes high o/p is 1 and the posedge come q<=d. Synchronous reset is when posegde of the clk come and sync_rest goes high then q= 0 else q follows d. 
### Flip flop using iverilog and Gtkwave
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave  tb_dff_asyncres.vcd
iverilog dff_async_set.v tb_dff_async_set.v
./a.out
gtkwave tb_dff_async_set.vcd
iverilog dff_syncres.v tb_dff_syncres.v
./a.out
 gtkwave tb_dff_syncres.vcd
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/async_reset.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/async_set.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/sync_reset.png)
### Using Yosys

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
read_verilog dff_async_set.v
synth -top dff_async_set
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
read_verilog dff_syncres.v
synth -top dff_syncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/async_reset_synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/async_set_synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/sync_reset_synth.png)

### optimization

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim mult_*.v -o
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/mult.png)
if a is 3 bit sequence and if we do a*2 => at 4 bit o/p y we get {a,0}
a*4={a,00}

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_2.v
synth -top mul2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mul2_net.v
!gvim mul2_net.v
```
there is no hardware required

### Output

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/a_2_synth.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/a_2_net.png)

if a is 3 bit sequence and if we do a*9 => at 5 bit o/p y we get {a,a}

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_8.v
synth -top mult8
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mult8_net.v
!gvim mult8_net.v
```
### Output

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/a_3_synth.png)

![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day2/Images/a_3_net.png)

## Day3
### Introduction to logic optimization
combinatonal logic optimization is the squeez the logic to get the optimized design. Now this are of two types one is contant propagation or direct optimization(given some contant input) and the another one is boolean logic optimization using k-map or quine Mckluskey(minimization).

In sequential Logic optimization they are of two types one is basic(sequential contant propagation:- q pin should give contant value) and the another one is advenced(state optimization:-optimize the unused state, retiming:-transfer the delay to improve the performance, sequential logic cloning(floorplan aware synthesis):-cloning a block to reduce the time)
### Combinational logic Optimizatio lab
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/opt_check_synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/opt_check2_synth.png)

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/opt_check3_synth.png)


### Sequential Logic optimization
### similator
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim dff_const1.v -o dff_const2.v
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
iverilog dff_const2.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const1.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const2.png)


### optimization
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
read_verilog dff_const2.v
synth -top dff_const2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const1_synth.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const2_synth.png)

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
iverilog dff_const3.v tb_dff_const3.v
./a.out
gtkwave tb_dff_const3.vcd
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const3.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/dff_const3_synth.png)


### Seq opt unused output
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/counter_opt_synth.png)

It is just toggling
```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
cp counter_opt.v counter_opt2.v
gedit counter_opt2.v
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt2.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week1/blob/main/Day3/Images/counter_opt2_synth.png)

## Day4

### Gate level simulation & synthesis
to verify and also the timing requirement is met(delay annotation) of the netlist we invoke the netlist and the test bench to the simulator.
Now in case of GLS using iverilog we fed the netlist,gate level verilog model(what is the meaning of and or that we tell to the simulator),test bench to the simulator. it will generate a .vcd file then we move to the gtkwave.
Gate level model have two type one is timing aware(functional+time) and the another one is functonal.
There are some reason at which synthesis simulation mismatch occur there are missing sensitivity list(use always@(*)),blk and non blk assignment(= <=),non standared verilog coding

```
cd VLSI
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files
gvim ternary_operator_mux.v -o bad_mux.v -o good_mux.v
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_net.v
show
iverilog ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

```

```



















