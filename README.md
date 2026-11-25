# EXP-6--Shift-Register
# Design and Verification of a 4-bit Serial-In Parallel-Out (SIPO) Shift Register
## Aim

To design and verify a 4-bit Serial-In Parallel-Out (SIPO) Shift Register using Verilog HDL, and to observe its behaviour using a testbench and VCD waveform.

## Theory / Synopsis

A shift register is a group of flip-flops connected in cascade, sharing a common clock, in which data is shifted from one flip-flop to the next on every active clock edge. In a Serial-In Parallel-Out (SIPO) shift register, one bit of data (sin) is fed in serially at every clock edge, and after several clock pulses the accumulated bits are available simultaneously at the parallel output Q[3:0].

Each clock pulse shifts the existing contents one position (here, left) and inserts the new serial input bit into the vacated (LSB) position. The bit that was in the most significant position is shifted out and discarded (or may be taken as the serial output, sout). An active-high asynchronous reset clears the register to all zeros.

## Functional Description
On every rising edge of clk:

if (rst) Q <= 4'b0000;

else Q <= {Q[2:0], sin};   // shift left, sin enters at LSB

Serial output: sout = Q[3]   (the bit about to be shifted out)

## Design / RTL Program
module mux2to1 (
    input  wire A,      // input line 0
    input  wire B,      // input line 1
    input  wire S,      // select line
    output wire Y       // output
);
    assign Y = (~S & A) | (S & B);
endmodule
 
module demux1to2 (
    input  wire D,      // data input
    input  wire S,      // select line
    output wire Y0,     // output line 0
    output wire Y1      // output line 1
);
    assign Y0 = (~S) & D;
    assign Y1 = S & D;
endmodule
Testbench Program
// gedit tb4.v
module tb4;
    reg  A, B, S, D;
    wire Y;
    wire Y0, Y1;
 
   // Instantiate the 2:1 Multiplexer
    mux2to1 mux_uut (
        .A(A),
        .B(B),
        .S(S),
        .Y(Y)
    );
 
  // Instantiate the 1:2 Demultiplexer
    demux1to2 demux_uut (
        .D(D),
        .S(S),
        .Y0(Y0),
        .Y1(Y1)
    );
 
  initial begin
        // ---- VCD dump setup ----
        $dumpfile("mux_demux.vcd");   // name of the VCD file to be generated
        $dumpvars(0, tb4);             // dump all signals in this testbench hierarchy
 
  // ---- Apply all input combinations ----
        $monitor("Time=%0t S=%b A=%b B=%b Y=%b | D=%b Y0=%b Y1=%b",
                  $time, S, A, B, Y, D, Y0, Y1);
 
   // MUX select = 0 -> Y should follow A
        S = 0; A = 0; B = 0; D = 0; #10;
        S = 0; A = 0; B = 1; D = 1; #10;
        S = 0; A = 1; B = 0; D = 1; #10;
        S = 0; A = 1; B = 1; D = 0; #10;
 
  // MUX select = 1 -> Y should follow B
        S = 1; A = 0; B = 0; D = 0; #10;
        S = 1; A = 0; B = 1; D = 1; #10;
        S = 1; A = 1; B = 0; D = 1; #10;
        S = 1; A = 1; B = 1; D = 0; #10;
      #10 $finish;
    end
endmodule

Testbench Program




## Operation / Truth Table

Register contents after each active clock edge (sin sequence = 1, 0, 1, 1)
<img width="406" height="181" alt="image" src="https://github.com/user-attachments/assets/7a5c1c67-a5d5-4160-938b-3647f8630dd7" />

## Simulation Procedure

STEP 1 – Open Terminal
Open a terminal in the experiment folder.

STEP 2 – Load Synopsys Environment
source /synopsys/start.sh

STEP 3 – Compile Using VCS
vcs shift_reg.v tb.v -full64
If compilation is successful, VCS generates the simulation executable:
simv

STEP 4 – Run Simulation
./simv
The terminal displays the reset, clock, serial input and the resulting parallel output Q and serial output sout at every change (via $monitor). A VCD waveform file is also generated:
shift_reg.vcd

STEP 5 – Open DVE
dve -full64
Other option:
dve -full64 &
A DVE environment will open.

## DVE Waveform Verification
In DVE:
●	Open the testbench hierarchy.

●	Locate the signals: clk, rst, sin, Q[3:0], sout.

●	Add the signals to the waveform window.

●	Run / inspect the waveform.

●	Verify that on every rising edge of clk (with rst = 0), Q shifts left by one bit and sin enters at Q[0].

●	Verify that sout always equals Q[3], the bit that is about to be shifted out.

●	Confirm that Q resets to 4'b0000 immediately whenever rst = 1, independent of clk.

The waveform should agree with the operation table.

## Expected Result

The 4-bit SIPO Shift Register was realized using Verilog HDL, with the characteristic behaviour:

Q(next) = {Q[2:0], sin}

The design was compiled and simulated using Synopsys VCS, and the functionality was verified using DVE waveform analysis, matching the expected operation table.

## Output

<img width="1600" height="880" alt="EXPERIMENT 6" src="https://github.com/user-attachments/assets/4115e633-b6c2-479e-927e-cd72e12c0cba" />


## Result

Thus, the 4-bit Serial-In Parallel-Out Shift Register was designed, implemented using Verilog HDL, successfully simulated using Synopsys VCS, and verified using Synopsys DVE.

