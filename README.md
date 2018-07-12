# hello-world
小小的一步，旅途的起点，让我所思所想之物留于此处。
#FPGA数字钟
#top module
module top(
    input clk,
    input adj,
	 input alarm,
	 input sw,
	 input en,
    output [0:6] seg,
    output wire[0:3] sc,
	 output led_alarm,led_s,led_chime,led_am,led_alarm_sw,
	 output [7:0] Hour,Minute,Second
    );
	

wire [5:0] ct;
wire [7:0] sw_Hour,hour_al,minute_al;

supply0 rst;
		
//***Hour:Minute:Second counter***
counter main(clk,rst,{ct[5],ct[3:2]},Hour,Minute,Second,led_s,led_chime);

//********显示器********
display dis(clk,ct[4:0],led_alarm_sw,en,sw_Hour,Minute,Second,hour_al,minute_al,seg,sc);

//***************控制器************
controller c0(clk,rst,adj,alarm,sw,en,ct);
hour_switch h0(clk,Hour,ct[4],sw_Hour,led_am);
alarm al0(clk,rst,{ct[5],ct[1:0]},Hour,Minute,hour_al,minute_al,led_alarm,led_alarm_sw);

endmodule

Test bench代码：
module top_test;

	// Inputs
	reg clk;
	reg adj;
	reg alarm;
	reg sw;
	reg en;

	// Outputs
	wire [6:0] seg;
	wire [3:0] sc;
	wire [7:0] Hour;
	wire [7:0] Minute;
	wire [7:0] Second;

	// Instantiate the Unit Under Test (UUT)
	top uut (
		.clk(clk), 
		.adj(adj), 
		.seg(seg), 
		.sc(sc), 
		.Hour(Hour), 
		.Minute(Minute),
		.Second(Second),
		.alarm(alarm),
		.sw(sw),
		.en(en),
		.led_alarm(led_alarm),
		.led_s(led_s),
		.led_chime(led_chime),
		.led_am(led_am),
		.led_alarm_sw(led_alarm_sw)
	);
	always begin
		clk<=~clk;
		#1;
		end
	always begin
	sw = 1;#180;	sw = 0;#180;
		end	
	always begin
	adj =1;#180;	adj =0;#180;
		end
	always begin
	en = 1;#720;	en = 0;#180;
		end
	always begin
	alarm =1;#180;	alarm =0;#180;
		end
	initial begin
		#0.5;
		adj = 0;
		alarm =0;
		sw = 0;
		en = 0;
      clk = 0;
		en = 1;
	end
endmodule
