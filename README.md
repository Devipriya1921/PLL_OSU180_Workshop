# VSD Open On-Chip Clock Multiplier (PLL) on OSU180

![vsdopentutorial](https://user-images.githubusercontent.com/80625515/137896513-0cc9dace-0454-43a7-be28-a6ce883cf631.png)


## Table of Contents

- [Introduction](#introduction)
- [Tools Required](#tools-required)
- [Spice Netlist Generated](#spice-netlist-generated)
- [Pre Layout Simulation](#pre-layout-simulation)
- [Magic Layout](#magic-layout)
- [Post Layout Simulation](#post-layout-simulation)
- [References](#references)
- [Author](#author)
- [Acknowledgement](#acknowledgement)

## Introduction

This repository focuses on workshop on PLL (Phase Locked Loop) also known as On-Chip Clock Multiplier. In this workshop we are going to cover a brief description on what is PLL and it's each block. We will also see pre-layout and post-layout simulations of each block of a PLL.

A phase-locked loop (PLL) is an electronic circuit with a voltage or voltage-driven oscillator that constantly adjusts to match the frequency of an input signal. PLLs are used to generate, stabilize, modulate, demodulate, filter or recover a signal from a "noisy" communications channel where data has been interrupted.

The main purpose of a PLL circuit is to synchronize an output oscillator signal with a reference signal. When the phase difference between the two signals is zero, the system is “locked.” A PLL is a closed-loop system with a control mechanism to reduce any phase error that may occur.

The clock generator is one of the most crucial part in synchronous processor & probably most susceptible after power lines which can cause failure of entire circuitry if not designed properly.
PLL is found in radio and general electronic items from mobile phones to broadcast radios, televisions to Wi-Fi routers, walkie talkie radios to professional communications systems, etc.

Block diagram of PLL.

![blockdiagram](https://user-images.githubusercontent.com/80625515/137904145-fcaccd2a-aef9-4177-a52c-9dea5fee8af9.jpg)

![Block  diag-2](https://user-images.githubusercontent.com/83152452/137978963-8b6cadfa-104a-49bd-af9c-30fe6c76c942.jpeg)

<picture-block-diagram>

## Tools Required

- [Ngspice](http://ngspice.sourceforge.net/download.html)
- [eSim EDA tool](https://github.com/FOSSEE/eSim.git)
- [Magic](http://opencircuitdesign.com/magic/)

## SPICE Netlist Generated

- Inverter 

  
```
****************************
*Inverter
***************************

.include osu018.lib

M1 out in GND GND nfet l=180n w=180n
M2 VDD in out VDD pfet l=180n w=360n

V1 in 0 PULSE 0 1.8 10p 50p 50p 100n 200n
v2 VDD 0 1.8


.control
tran 0.01ns 400ns
plot v(in)+2 v(out)
.endc

.end

```
  
  
- NAND Gate
  

```
****************************
*2 input nand
***************************

.include osu018.lib

M1 out in1 N001 N001 nfet l=180n w=180n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 GND GND nfet l=180n w=360n


V1 in1 0 PULSE 0 1.8 10p 50p 50p 100n 200n
V2 in2 0 PULSE 0 1.8 10p 50p 50p 50n 100n
V3 VDD 0 1.8


.control
tran .1ns 200n
plot V(in1)+4 V(in2)+2 V(out)
.endc


.end

```

- 3-input NAND Gate

```
****************************
*3 input nand
***************************


.include osu018.lib

M1 out in1 N001 N001 nfet l=180n w=540n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=540n
M5 N002 in3 GND GND nfet l=180n w=540n
M6 VDD in3 out VDD pfet l=180n w=360n



V1 in1 0 PULSE 0 1.8 10p 50p 50p 100n 200n
V2 in2 0 PULSE 0 1.8 10p 50p 50p 50n 100n
V3 in3 0 PULSE 0 1.8 10p 50p 50p 25n 50n
V4 VDD 0 1.8


.control
tran .1ns 200n
plot V(in1)+6 V(in2)+4 V(in3)+2 V(out)
.endc


.end

```

  
- 4-input NAND Gate
  
```
****************************
*4 input nand
***************************

.include osu018.lib

M1 out in1 N001 N001 nfet l=180n w=720n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=720n
M5 N002 in3 N003 N003 nfet l=180n w=720n
M6 VDD in3 out VDD pfet l=180n w=360n
M7 N003 in4 GND GND nfet l=180n w=720n
M8 VDD in4 out VDD pfet l=180n w=360n

V1 in1 0 PULSE 0 1.8 10p 50p 50p 100n 200n
V2 in2 0 PULSE 0 1.8 10p 50p 50p 50n 100n
V3 in3 0 PULSE 0 1.8 10p 50p 50p 25n 50n
V4 in4 0 PULSE 0 1.8 10p 50p 50p 12.5n 25n
V5 VDD 0 1.8


.control
tran .1ns 200n
plot V(in1)+8 V(in2)+6 V(in3)+4 V(in4)+2 V(out)
.endc


.end

```


- Phase Frequency Detector

```

****************************
*PFD
***************************

.include osu018.lib


*______________________________________________
XX1 N001 N005 N002 VDD 0 nand101
XX2 N002 N008 N006 VDD 0 nand101
XX3 N006 N007 N008 VDD 0 nand101
XX4 N007 N010 N011 VDD 0 nand101
XX5 N011 N009 N010 VDD 0 nand101
XX6 N013 N012 N009 VDD 0 nand101
XX7 f_clk_in N005 VDD 0 inv101
XX8 f_VCO N013 VDD 0 inv101
XX9 N002 N003 VDD 0 inv101
XX10 N003 N004 VDD 0 inv101
XX11 N009 N014 VDD 0 inv101
XX12 N014 N015 VDD 0 inv101
XX13 N004 N006 N007 N001 VDD 0 nand301
XX14 N007 N010 N015 N012 VDD 0 nand301
XX15 N012 down VDD 0 inv101
XX16 N006 N002 N009 N010 VDD 0 N007 nand401
XX17 N001 up VDD 0 inv101



*______________________________________________
* _______SUBCIRCUITS_________________
*______________________________________________

.subckt nand101 in1 in2 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=180n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 GND GND nfet l=180n w=360n
.ends nand101

.subckt inv101 in out VDD GND
M1 out in GND GND nfet l=180n w=180n
M2 VDD in out VDD pfet l=180n w=360n
.ends inv101

.subckt nand301 in1 in2 in3 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=540n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=540n
M5 N002 in3 GND GND nfet l=180n w=540n
M6 VDD in3 out VDD pfet l=180n w=360n
.ends nand301

.subckt nand401 in1 in2 in3 in4 VDD GND out
M1 out in1 N001 N001 nfet l=180n w=720n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=720n
M5 N002 in3 N003 N003 nfet l=180n w=720n
M6 VDD in3 out VDD pfet l=180n w=360n
M7 N003 in4 GND GND nfet l=180n w=720n
M8 VDD in4 out VDD pfet l=180n w=360n
.ends nand401
*______________________________________________
* _______SUBCIRCUITS_ENDS________________
*______________________________________________


V1 f_clk_in 0 pulse 0 1.8 0 100p 100p 5n 10n
V2 f_VCO 0 pulse 0 1.8 2n 100p 100p 5n 9n
V3 VDD 0 1.8


.control
tran .1ns 50n
plot V(f_clk_in)+6 V(f_VCO)+4 V(up)+2 V(down)
.endc


.end

```


- Charge Pump with Phase Detector

```

*PFD_Charge

.include osu018.lib

XX1 f_in f_out_8 up down pfd_501
XX2 up down vin_vco charge_pump


*___________LPF________________*
C1 Vin_vco 0 200p
C2 N001 0 500p
R1 Vin_vco N001 500
*______________________________*


* block symbol definitions
.subckt pfd_501 f_clk_in f_VCO up down
XX1 N001 N005 N002 vddd 0 nand101
XX2 N002 N008 N006 vddd 0 nand101
XX3 N006 N007 N008 vddd 0 nand101
XX4 N007 N010 N011 vddd 0 nand101
XX5 N011 N009 N010 vddd 0 nand101
XX6 N013 N012 N009 vddd 0 nand101
XX7 f_clk_in N005 vddd 0 inv101
XX8 f_VCO N013 vddd 0 inv101
XX9 N002 N003 vddd 0 inv101
XX10 N003 N004 vddd 0 inv101
XX11 N009 N014 vddd 0 inv101
XX12 N014 N015 vddd 0 inv101
XX13 N004 N006 N007 N001 vddd 0 nand301
XX14 N007 N010 N015 N012 vddd 0 nand301
XX15 N012 down vddd 0 inv101
XX16 N006 N002 N009 N010 vddd 0 N007 nand401
XX17 N001 up vddd 0 inv101
V1 vddd 0 1.8
.ends pfd_501

.subckt charge_pump UP Down CP
M7 UP_bar UP 0 0 nfet l=180n w=180n
M8 DOWN_bar Down 0 0 nfet l=180n w=180n
M12 VDD Down DOWN_bar VDD pfet l=180n w=540n
M14 VDD UP UP_bar VDD pfet l=180n w=540n
M15 UP_bar 0 N001 N001 nfet l=180n w=180n
M16 DOWN_bar 0 N004 N004 nfet l=180n w=180n
M17 N003 N004 N003 N003 nfet l=180n w=180n
M18 0 VDD VDD 0 nfet l=180n w=180n
M19 CP VDD N003 N003 nfet l=180n w=180n
M20 N003 Down 0 0 nfet l=180n w=15u
M21 N001 VDD UP_bar N001 pfet l=180n w=540n
M22 N004 VDD DOWN_bar N004 pfet l=180n w=540n
M23 N002 UP N002 N002 pfet l=180n w=540n
M24 0 0 VDD VDD pfet l=180n w=540n
M25 N002 0 CP N002 pfet l=180n w=540n
M26 VDD N001 N002 VDD pfet l=180n w=45u
V4 VDD 0 1.8
.ends charge_pump

.subckt nand101 in1 in2 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=180n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 GND GND nfet l=180n w=360n
*V1 VDD 0 1.8
.ends nand101

.subckt inv101 in out VDD GND
M1 out in GND GND nfet l=180n w=180n
M2 VDD in out VDD pfet l=180n w=360n
*V1 VDD 0 1.8
.ends inv101

.subckt nand301 in1 in2 in3 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=540n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=540n
M5 N002 in3 GND GND nfet l=180n w=540n
M6 VDD in3 out VDD pfet l=180n w=360n
*V1 VDD 0 1.8
.ends nand301

.subckt nand401 in1 in2 in3 in4 VDD GND out
M1 out in1 N001 N001 nfet l=180n w=720n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=720n
M5 N002 in3 N003 N003 nfet l=180n w=720n
M6 VDD in3 out VDD pfet l=180n w=360n
M7 N003 in4 GND GND nfet l=180n w=720n
M8 VDD in4 out VDD pfet l=180n w=360n
*V1 VDD 0 1.8
.ends nand401

V1 f_in 0 PULSE 0 1.8 10p 60p 60p 100n 200n
V2 f_out_8 0 PULSE 0 1.8 40n 60p 60p 100n 180n
V3 VDD 0 1.8


.control
tran .1n 4u
plot V(f_in)+8 V(f_out_8)+6 V(up)+4 V(down)+2 V(vin_vco)
.endc


.end

```

- Voltage Controlled Oscillator

```

****************************
*VCO
***************************

.include osu018.lib

*______________________________________________
M7 Vp Vn 0 0 nfet l=180n w=360n
M8 Vp Vp VDD VDD pfet l=180n w=1800n
R1 Vn Vinvco 1
XU22 osc_fb Osc inv_20_10
XX3 n1 osc_fb Vp Vn cs_inv
XX16 N005 n1 Vp Vn cs_inv
XX17 N004 N005 Vp Vn cs_inv
XX18 N003 N004 Vp Vn cs_inv
XX19 N002 N003 Vp Vn cs_inv
XX20 N001 N002 Vp Vn cs_inv
XX21 osc_fb N001 Vp Vn cs_inv
*______________________________________________

*______________________________________________
* _______SUBCIRCUITS_________________
*______________________________________________

.subckt inv_20_10 In Out
M1 Out In 0 0 nfet l=180n w=360n
M2 Out In N001 N001 pfet l=180n w=720n
V1 N001 0 1.8
.ends inv_20_10

.subckt cs_inv In Out Vp Vn
M1 N003 Vn 0 0 nfet l=180n w=360n
M4 N002 Vp N001 VDD pfet l=180n w=720n
M3 Out In N002 VDD pfet l=180n w=720n
M2 Out In N003 0 nfet l=180n w=360n
*C1 Out 0 1pf
V1 N001 0 1.8
.ends cs_inv

*______________________________________________
* _______SUBCIRCUITS_ENDS________________
*______________________________________________


V2 Vinvco 0 .5
V3 VDD 0 1.8

.control
tran .1ns 1u
plot V(Vinvco)+2 V(osc_fb)
.endc


.end

```

- Frequency Divider

```

****************************
*freq_div_2
***************************


.include osu018.lib

M1 N004 N002 0 0 nfet l=180n w=180n
M2 VDD N002 N004 VDD pfet l=180n w=540n
M5 N003 N004 0 0 nfet l=180n w=180n
M6 VDD N004 N003 VDD pfet l=180n w=540n
M7 D clock_b N002 0 nfet l=180n w=180n
M3 N002 clock D VDD pfet l=180n w=540n
M4 N002 clock N003 0 nfet l=180n w=180n
M8 N003 clock_b N002 VDD pfet l=180n w=540n
M9 q N001 0 0 nfet l=180n w=180n
M10 VDD N001 q VDD pfet l=180n w=540n
M11 D q 0 0 nfet l=180n w=180n
M12 VDD q D VDD pfet l=180n w=540n
M13 N004 clock N001 0 nfet l=180n w=180n
M14 N001 clock_b N004 VDD pfet l=180n w=540n
M15 N001 clock_b D 0 nfet l=180n w=180n
M16 D clock N001 VDD pfet l=180n w=540n
M17 clock_b clock 0 0 nfet l=180n w=180n
M18 VDD clock clock_b VDD pfet l=180n w=540n



VDD VDD 0 1.8
V1 clock 0 PULSE 0 1.8 10p 50p 50p 100n 200n


.control
tran .1ns 1u
plot V(clock)+2 V(q)
.endc


.end

```


- PLL

```

*__________________PLL-combined in 1 file_________________________*


XX1 f_in f_VCO/8 up down pfd_501
XX2 up down Vin_vco charge_pump
XX3 Vin_vco f_out vco_19_16
XX5 f_out N003 freq_div_2
XX6 N003 N002 freq_div_2
XX7 N002 f_VCO/8 freq_div_2
V1 f_in 0 PULSE 0 1.8 10p 60p 60p 100n 200n

.include osu018.lib

*V2 f_in 0 PULSE 0 1.8 10p 60p 60p 50n 100n
*V3 f_in 0 PULSE 0 1.8 10p 60p 60p 41.665n 83.33n

*___________LPF________________*
C1 Vin_vco 0 200p
C2 N001 0 500p
R1 Vin_vco N001 500
*______________________________*

*__________________Subcircuits_____________________*

******************PFD*******************************
.subckt pfd_501 f_clk_in f_VCO up down
XX1 N001 N005 N002 vddd 0 nand101
XX2 N002 N008 N006 vddd 0 nand101
XX3 N006 N007 N008 vddd 0 nand101
XX4 N007 N010 N011 vddd 0 nand101
XX5 N011 N009 N010 vddd 0 nand101
XX6 N013 N012 N009 vddd 0 nand101
XX7 f_clk_in N005 vddd 0 inv101
XX8 f_VCO N013 vddd 0 inv101
XX9 N002 N003 vddd 0 inv101
XX10 N003 N004 vddd 0 inv101
XX11 N009 N014 vddd 0 inv101
XX12 N014 N015 vddd 0 inv101
XX13 N004 N006 N007 N001 vddd 0 nand301
XX14 N007 N010 N015 N012 vddd 0 nand301
XX15 N012 down vddd 0 inv101
XX16 N006 N002 N009 N010 vddd 0 N007 nand401
XX17 N001 up vddd 0 inv101
V3 vddd 0 1.8
.ends pfd_501

*****************charge_pump*******************************
.subckt charge_pump UP Down CP
M7 UP_bar UP 0 0 nfet l=180n w=180n
M8 DOWN_bar Down 0 0 nfet l=180n w=180n
M12 VDD Down DOWN_bar VDD pfet l=180n w=540n
M14 VDD UP UP_bar VDD pfet l=180n w=540n
M15 UP_bar 0 N001 N001 nfet l=180n w=180n
M16 DOWN_bar 0 N004 N004 nfet l=180n w=180n
M17 N003 N004 N003 N003 nfet l=180n w=180n
M18 0 VDD VDD 0 nfet l=180n w=180n
M19 CP VDD N003 N003 nfet l=180n w=180n
M20 N003 Down 0 0 nfet l=180n w=15u
M21 N001 VDD UP_bar N001 pfet l=180n w=540n
M22 N004 VDD DOWN_bar N004 pfet l=180n w=540n
M23 N002 UP N002 N002 pfet l=180n w=540n
M24 0 0 VDD VDD pfet l=180n w=540n
M25 N002 0 CP N002 pfet l=180n w=540n
M26 VDD N001 N002 VDD pfet l=180n w=45u
V1 VDD 0 1.8
.ends charge_pump

******************VCO*******************************
.subckt vco_19_16 Vinvco osc
M7 Vp Vn 0 0 nfet l=180n w=360n
M8 Vp Vp VDD VDD pfet l=180n w=1800n
R1 Vn Vinvco 1
XU22 osc_fb Osc inv_20_10
XX3 n1 osc_fb Vp Vn cs_inv
XX16 N005 n1 Vp Vn cs_inv
XX17 N004 N005 Vp Vn cs_inv
XX18 N003 N004 Vp Vn cs_inv
XX19 N002 N003 Vp Vn cs_inv
XX20 N001 N002 Vp Vn cs_inv
XX21 osc_fb N001 Vp Vn cs_inv
V1 VDD 0 1.8
.ends vco_19_16

******************Frequency divider*******************************
.subckt freq_div_2 clock Q
M1 N004 N002 0 0 nfet l=180n w=180n
VDD VDD 0 1.8
M2 VDD N002 N004 VDD pfet l=180n w=540n
M5 N003 N004 0 0 nfet l=180n w=180n
M6 VDD N004 N003 VDD pfet l=180n w=540n
M7 D clock_b N002 0 nfet l=180n w=180n
M3 N002 clock D VDD pfet l=180n w=540n
M4 N002 clock N003 0 nfet l=180n w=180n
M8 N003 clock_b N002 VDD pfet l=180n w=540n
M9 Q N001 0 0 nfet l=180n w=180n
M10 VDD N001 Q VDD pfet l=180n w=540n
M11 D Q 0 0 nfet l=180n w=180n
M12 VDD Q D VDD pfet l=180n w=540n
M13 N004 clock N001 0 nfet l=180n w=180n
M14 N001 clock_b N004 VDD pfet l=180n w=540n
M15 N001 clock_b D 0 nfet l=180n w=180n
M16 D clock N001 VDD pfet l=180n w=540n
M17 clock_b clock 0 0 nfet l=180n w=180n
M18 VDD clock clock_b VDD pfet l=180n w=540n
.ends freq_div_2

.subckt nand101 in1 in2 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=180n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 GND GND nfet l=180n w=360n
.ends nand101

.subckt inv101 in out VDD GND
M1 out in GND GND nfet l=180n w=180n
M2 VDD in out VDD pfet l=180n w=360n
.ends inv101

.subckt nand301 in1 in2 in3 out VDD GND
M1 out in1 N001 N001 nfet l=180n w=540n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=540n
M5 N002 in3 GND GND nfet l=180n w=540n
M6 VDD in3 out VDD pfet l=180n w=360n
.ends nand301

.subckt nand401 in1 in2 in3 in4 VDD GND out
M1 out in1 N001 N001 nfet l=180n w=720n
M2 VDD in2 out VDD pfet l=180n w=360n
M3 VDD in1 out VDD pfet l=180n w=360n
M4 N001 in2 N002 N002 nfet l=180n w=720n
M5 N002 in3 N003 N003 nfet l=180n w=720n
M6 VDD in3 out VDD pfet l=180n w=360n
M7 N003 in4 GND GND nfet l=180n w=720n
M8 VDD in4 out VDD pfet l=180n w=360n
.ends nand401

.subckt inv_20_10 In Out
M1 Out In 0 0 nfet l=180n w=360n
M2 Out In N001 N001 pfet l=180n w=720n
V1 N001 0 1.8
.ends inv_20_10

.subckt cs_inv In Out Vp Vn
M1 N003 Vn 0 0 nfet l=180n w=360n
M4 N002 Vp N001 VDD pfet l=180n w=720n
M3 Out In N002 VDD pfet l=180n w=720n
M2 Out In N003 0 nfet l=180n w=360n
C1 Out 0 1f
V1 N001 0 1.8
.ends cs_inv


************simulation commands**************

.tran .1ns 6u
.ic V(vin_vco) = 0
.control
run
plot V(f_in)+8 V(up)+6 V(down)+4 V(Vin_vco)+2 V(f_out)

*plot V(N002)
*plot V(Vin_vco)
*plot V(f_out)
.endc
.end

```


## Pre Layout Simulation

### Inverter 
 
- Simulation
  
 ![inverter -1](https://user-images.githubusercontent.com/83152452/137966878-488da8a4-0543-437e-ace9-1fcc402bca25.png)

- Waveform
  
 ![inverter-2](https://user-images.githubusercontent.com/83152452/137966890-d28ce1ae-5c48-4b14-adea-a828735ad99b.png)

### NAND 

- Simulation
  
 ![nand101-1](https://user-images.githubusercontent.com/83152452/137966908-c3b64460-15db-4e37-a87c-7e8d5a18ba8f.png)

- Waveform
  
 ![nand101-2](https://user-images.githubusercontent.com/83152452/137966913-065ac71d-e3d4-4e70-b210-c122c17e711b.png)

### 3-Input NAND 
  
- Simulation  
  
  ![nand301-1](https://user-images.githubusercontent.com/83152452/137966925-96346dcf-ab87-4298-81bf-48f1756b734e.png)

- Waveform
  
  ![nand301-2](https://user-images.githubusercontent.com/83152452/137966940-7f852443-953f-47b7-9fe0-e300f3cdcd16.png)

### 4-Input NAND 
  
- Simulation
  
 ![nand401-1](https://user-images.githubusercontent.com/83152452/137966954-214e83eb-529d-4362-a1e9-047e2c0a411d.png)

- Waveform
  
  ![nand401-2](https://user-images.githubusercontent.com/83152452/137966970-20e37c77-2110-46b0-ad43-5e4af4abfe20.png)

### Phase Frequency Detector 
  
- Simulation
  
  ![pfd-1](https://user-images.githubusercontent.com/83152452/137967476-d0c4f887-dc2c-459b-a293-b1e08c959e63.png)

- Waveform
  
 ![pfd-2](https://user-images.githubusercontent.com/83152452/137967483-ce5e7bce-94f8-4730-8c78-bbd2c558d3e5.png)

  
### Charge Pump with Phase Detector 

- Simulation

   ![pfd_cp-1](https://user-images.githubusercontent.com/83152452/137967497-790e304c-6cd8-41ab-aa3f-9335707e23e5.png)
   
- Waveform   
  
  ![pfd_cp-2](https://user-images.githubusercontent.com/83152452/137967501-ed33527d-1d02-4ec1-9d9e-38f74d16e4b3.png)

### Voltage Controlled Oscillator 

- Simulation
  
 ![vco-1](https://user-images.githubusercontent.com/83152452/137967553-e2438219-6d9c-4df6-a3dc-2260975dd61e.png)

- Waveform
   
 ![vco-2](https://user-images.githubusercontent.com/83152452/137967563-86907f5c-e46d-476a-85fa-fb4ebf5395d9.png)  

### Frequency-Divider 

- Simulation
  
 ![freq-div-1](https://user-images.githubusercontent.com/83152452/137967578-0e7ed5e8-8417-41dd-a7e5-d98ad26b37f6.png)
  
- Waveform  

  ![freq-div-2](https://user-images.githubusercontent.com/83152452/137967585-c8fe2e2d-6ac3-49f6-99f2-135d1b72b509.png)
  
  
### PLL 

- Simulation
  
  ![pll-1](https://user-images.githubusercontent.com/83152452/137967597-20765196-022d-460e-b13d-6efd5deb05ae.png)

- Waveform

  ![pll-2](https://user-images.githubusercontent.com/83152452/137967615-f136dd6c-50e3-434b-9d57-45b528fc0f1c.png)

  
## Layout

  
### Standard cell Layout is as shown below
  
  Command used -
  
  ```
  cd std_cells/
  magic -T ../SCN6M_SUBM.10.tech std_cells.mag
  ```
  
  ![mag-1](https://user-images.githubusercontent.com/83152452/137968190-9bed8441-25e4-4459-9147-d9861498a066.png)


### Phase Frequency Detector Layout
 
  Command used -
  
  ```
  cd pfd/
  magic -T ../SCN6M_SUBM.10.tech pfd.mag
  ```
  
  ![mag-3-pfd](https://user-images.githubusercontent.com/83152452/137968206-05d632ce-c997-408d-b4b4-56251a54e998.png)


 ### Voltage-Controlled Oscillator Layout 
   
 Command used -
   
  ```
  cd vco/
  magic -T ../SCN6M_SUBM.10.tech vco101.mag
  ```
  
  ![mag-3-vco](https://user-images.githubusercontent.com/83152452/137968219-0f8ccfbc-e35b-4990-8020-2656581dd276.png)
  
  
 ### MUX Layout 
 
  Command used -
  
  ```
  cd mux21/
  magic -T ../SCN6M_SUBM.10.tech mux21.mag
  ```
  
  ![mag-4-mux](https://user-images.githubusercontent.com/83152452/137968227-20c5d103-6ea4-42f4-94d9-71c084487a13.png)

  
### Frequency Divider (/2) Layout
 
  Command used -
  
  ```
  cd freqdiv2/
  magic -T ../SCN6M_SUBM.10.tech freq_divider2.mag
  ```
  
  ![mag-5-freqdiv_2](https://user-images.githubusercontent.com/83152452/137968239-9a9c900c-ed37-4aff-9f67-cc1fe9ec0448.png)

### Frequency Divider (/8) Layout
 
  Command used -
  
  ```
  cd freqdiv8/
  magic -T ../SCN6M_SUBM.10.tech freq_divider8.mag
  ```
  
  ![mag-6-freqdiv_8](https://user-images.githubusercontent.com/83152452/137968267-d86a7b0b-3135-4088-af89-e04859520fc3.png)
  
### Final PLL Layout
 
  Command used -
  
  ```
  cd PLL/ 
  magic -T ../SCN6M_SUBM.10.tech pll.mag
  
  ```
  
  ![mag-7-pll-1](https://user-images.githubusercontent.com/83152452/137968275-57419b78-963c-449c-a265-eb33ead85740.png)



## Post Layout Simulation
  
  - Phase Frequency Detector 
  
  ![pfd-post-sim](https://user-images.githubusercontent.com/80625515/137934471-e42e38d9-842f-4f78-ba8b-a7c8eee02135.png)

  - Multiplexer 
  
  ![mux-post-sim](https://user-images.githubusercontent.com/80625515/137934546-9994597b-cf52-41d9-a031-0c463c93dc05.png)

  - Frequency Divider(/2)
  
  ![freqdiv-post-sim](https://user-images.githubusercontent.com/80625515/137934571-b4fde167-7ea9-4815-aaa5-7a3b0ad9e5cb.png)

  - Frequency Divider(/8)

  ![freqdiv8-post-sim](https://user-images.githubusercontent.com/80625515/137935295-8d2500f3-b760-4024-b9b1-8813bb3e9e53.png)

  - Voltage Controlled Oscillator 
  
 ![vco-sim-post](https://user-images.githubusercontent.com/80625515/137947386-4c38c1e9-5661-42f3-ae1b-6053f4eb142e.png)


  - PLL
  
  ![pllv2-vcp-post](https://user-images.githubusercontent.com/80625515/137935995-4275daaf-4c76-46a8-8682-f4f7da901179.png)

  ![pllv2-post](https://user-images.githubusercontent.com/80625515/137936016-5a620799-cc41-4106-b305-d59efd6709d5.png) 
  
  ![pllv2-2post](https://user-images.githubusercontent.com/80625515/137936071-67e25448-5ad3-44ad-a3a9-e122e7ac926e.png)
  
  ![pllv2-3-post](https://user-images.githubusercontent.com/80625515/137936101-2ab247e7-b794-4370-889f-7979d1247736.png)

  
## References

- https://github.com/parasgidd/avsdpll_3v3.git

## Author

- A Devipriya, Bachelor of Engineering (ECE), Cambridge Institute of Technology, Bangalore, adevipriya1900@gmail.com

## Acknowledgement

- Paras Gidd, M.Tech.( Microelectronics ), Manipal Institute of Technology,(MAHE), parasgidd@gmail.com
- Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalpghosh@gmail.com
