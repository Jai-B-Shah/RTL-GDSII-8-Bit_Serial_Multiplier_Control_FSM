module multiplicand_register (
    input clk, rst, load,
    input [7:0] A_in,
    output reg [7:0] A_out
);
    always @(posedge clk or posedge rst)
        if (rst) A_out <= 0;
        else if (load) A_out <= A_in;
endmodule

module multiplier_register (
    input clk, rst, load, shift,
    input [7:0] B_in,
    output reg [7:0] B_out,
    output wire B0
);
    always @(posedge clk or posedge rst)
        if (rst) B_out <= 0;
        else if (load) B_out <= B_in;
        else if (shift) B_out <= B_out >> 1;

    assign B0 = B_out[0];
endmodule

module adder (
    input [7:0] A,
    input [7:0] B,
    output [7:0] sum
);
    assign sum = A + B;
endmodule

module mux2to1 (
    input [7:0] in0,
    input [7:0] in1,
    input sel,
    output [7:0] out
);
    assign out = sel ? in1 : in0;
endmodule

module product_register (
    input clk, rst, load,
    input shift,
    input [7:0] mux_out,
    output reg [15:0] product
);
    always @(posedge clk or posedge rst)
        if (rst) product <= 0;
        else if (load) product <= 0;
        else if (shift) product <= product >> 1;
        else product[15:8] <= mux_out;
endmodule

module counter (
    input clk, rst, load, inc,
    output reg [2:0] count
);
    always @(posedge clk or posedge rst)
        if (rst) count <= 0;
        else if (load) count <= 0;
        else if (inc) count <= count + 1;
endmodule

module fsm (
    input clk, rst, start,
    input B0,
    input [2:0] count,
    output reg load_A, load_B, load_product, load_counter,
    output reg shift_B, shift_product, increment_counter,
    output reg add_enable, done
);
    reg [2:0] state;
    parameter idle = 0, load = 1, check = 2, add = 3, shift = 4, done_state = 5;

    always @(posedge clk or posedge rst)
        if (rst) state <= idle;
        else begin
            case (state)
                idle: begin
                    done <= 0;
                    {load_A, load_B, load_product, load_counter} <= 0;
                    {shift_B, shift_product, increment_counter, add_enable} <= 0;
                    state <= start ? load : idle;
                end
                load: begin
                    {load_A, load_B, load_product, load_counter} <= 4'b1111;
                    state <= check;
                end
                check: begin
                    {load_A, load_B, load_product, load_counter} <= 0;
                    state <= B0 ? add : shift;
                end
                add: begin
                    add_enable <= 1;
                    state <= shift;
                end
                shift: begin
                    add_enable <= 0;
                    {shift_B, shift_product, increment_counter} <= 3'b111;
                    state <= (count == 3'd7) ? done_state : check;
                end
                done_state: begin
                    {shift_B, shift_product, increment_counter} <= 0;
                    done <= 1;
                    state <= idle;
                end
            endcase
        end
endmodule

module serial_multiplier (
    input clk, rst, start,
    input [7:0] A, B,
    output [15:0] product,
    output done
);
    wire [7:0] A_out, B_out;
    wire [7:0] sum, mux_out;
    wire B0, shift_B, shift_prod, load_A, load_B, load_prod, load_ctr;
    wire add_en, inc_ctr;
    wire [2:0] count;
    reg [15:0] prod;

    multiplicand_register u_A (clk, rst, load_A, A, A_out);
    multiplier_register u_B (clk, rst, load_B, shift_B, B, B_out, B0);
    adder u_adder (A_out, prod[15:8], sum);
    mux2to1 u_mux (prod[15:8], sum, add_en, mux_out);
    product_register u_prod (clk, rst, load_prod, shift_prod, mux_out, prod);
    counter u_ctr (clk, rst, load_ctr, inc_ctr, count);
    fsm u_fsm (clk, rst, start, B0, count, load_A, load_B, load_prod, load_ctr, shift_B, shift_prod, inc_ctr, add_en, done);

    assign product = prod;
endmodule
