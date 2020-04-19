---
layout: project_single
title:  "COEN6741: Mini-MIPS Design and Simulation"
slug: "COEN6741"
---
Project description:

<img src="{{site.baseurl}}/assets/img/6741-0.png">



**The following VHDL code is written block by block. The name of block is specified. Each block is corresponding to blocks in the diagram above. (In that diagram, the name of blocks are not demonstrated).**

I didn't comment these code, it's not good to read now.

***VHDL code:***

PC:

```VHDL
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;
entity PC is
	port( pc_i: in std_logic_vector(31 downto 0);
		pc	:	out std_logic_vector(31 downto 0);
		clk : 	in std_logic;
		en  : 	in std_logic;
	   reset: 	in std_logic);
end entity PC;
architecture behavioral of PC is
	
begin
		process(clk)
			begin
				if reset = '1' then
					pc <= x"00000000";
				elsif en = '1' then
					if rising_edge(clk) then
						pc <= pc_i;
					end if;
					end if;
					end process;
end architecture behavioral;
```

PC-mux:

```VHDL
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity pc_mux is
	port ( sel : in std_logic_vector(1 downto 0);
	   bran_add: in std_logic_vector(31 downto 0);
		 jr_add: in std_logic_vector(31 downto 0);
			pc4: in std_logic_vector(31 downto 0);
		     pc: out std_logic_vector(31 downto 0));
end entity pc_mux;	
architecture behavioral of pc_mux is	
begin
	process(sel,bran_add,jr_add,pc4)
		begin
			if sel = "01" then
				pc <= bran_add;
			elsif sel = "10" then
				pc <= jr_add;
			else 
				pc <= pc4;
			end if;
	end process;
end architecture behavioral;
```

RAM:

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.std_logic_unsigned.all;

entity ram is
	port ( --wb_sel_i  :    in std_logic;
			   --wb_en_i  :     in std_logic;
			   mem_r_en  :  in std_logic;
			   mem_w_en  :  in std_logic;
			  -- bran_en  :   in std_logic;
			   --rd_ad_i  :     in std_logic_vector(4 downto 0);
			   result_i  :    in std_logic_vector(31 downto 0);
			   reset: in std_logic;
			   mem_data  :  in std_logic_vector(31 downto 0);
			   --bran_zero  : in std_logic;
			   --pc_sel1:     out std_logic;
			   mem_r_data:  out std_logic_vector(31 downto 0));
			   --result:      out std_logic_vector(31 downto 0);
			   --rd_ad:       out std_logic_vector(4 downto 0);
			   --wb_sel  :    out std_logic;
			   --wb_en   :    out std_logic);
end entity ram;	
architecture behavioral of ram is
type ram_array_type is array (15 downto 0) of std_logic_vector(31 downto 0);
   
   signal ram_array : ram_array_type;
begin
		process
			
			begin
				wait on mem_r_en,mem_w_en,result_i;
				if mem_r_en = '1' then
					mem_r_data <= ram_array(conv_integer(result_i));
				end if;
				if mem_w_en = '1' then
					 ram_array(conv_integer(result_i))<= mem_data;
				end if;
     end process;
end architecture behavioral;
```

rd_ad_mux:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity rd_ad_mux is 
	port ( rd_ad1: in std_logic_vector(4 downto 0);
		     rd_ad2: in std_logic_vector(4 downto 0);
		    ex_sel2: in std_logic;
		     	rd_ad: out std_logic_vector(4 downto 0));
end entity rd_ad_mux;
architecture behavioral of rd_ad_mux is
	
begin
	
	process(rd_ad1,rd_ad2,ex_sel2)
		begin
			if ex_sel2 = '1' then
				rd_ad <= rd_ad2;
			else
				rd_ad <= rd_ad1;
			
			end if;
	end process;
end architecture behavioral;
```

rd_data-mux:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;
entity rd_data_mux is
	port( wb_sel: in std_logic;
		    result: in std_logic_vector(31 downto 0);
		    mem_r_data: in std_logic_vector(31 downto 0);
		    rd_data: out std_logic_vector(31 downto 0));
end entity rd_data_mux;
	
architecture behavioral of rd_data_mux is
	
begin
	
	process(mem_r_data,result,wb_sel)
		begin
			if wb_sel = '1' then
				rd_data <= result;
			else 
				rd_data <= mem_r_data;
			end if;
		end process;
end architecture behavioral;
```

ref:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity regf is
    Port ( rs_ad : in STD_LOGIC_VECTOR(4 downto 0);
    	     rt_ad : in std_logic_vector(4 downto 0);
    	     rd_ad : in std_logic_vector(4 downto 0);
    	     rd_data: in std_logic_vector(31 downto 0);
    	     w_en: in std_logic;
    	     clk:  in std_logic;
           rs_data : out STD_LOGIC_VECTOR(31 downto 0);
           rt_data : out STD_LOGIC_VECTOR(31 downto 0);
           	reset: in std_logic);
end entity regf;

architecture Behavioral of regf is
type reg_array_type is array(31 downto 0) of STD_LOGIC_VECTOR(31 downto 0);
signal reg_array : reg_array_type;

begin
reg_array(0) <= x"00000000"; 			
    process(clk)
    begin  
    	if rs_ad = rd_ad and rs_ad /= "00000" and w_en = '1' then
    	       	rs_data <= rd_data;
    	elsif rs_ad = "00000" then
    		rs_data <= x"00000000";
    	else 
    		rs_data <= reg_array(to_integer(unsigned(rs_ad)));
    	end if;
    	if rt_ad = rd_ad and rt_ad /= "00000" and w_en = '1'then
    	       	rt_data <= rd_data;
    	elsif rt_ad = "00000" then
    		rt_data <= x"00000000";
    	else 
    		rt_data <= reg_array(to_integer(unsigned(rt_ad)));
    	end if;
    end process;
    process(clk)
    	begin
    		if rising_edge (clk) then
    		if w_en = '1' then
    			reg_array(to_integer(unsigned(rd_ad))) <= rd_data;
    		end if;
    		end if;
    end process;
end architecture Behavioral;
```

Rom:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity rom is
    Port ( add : in STD_LOGIC_VECTOR(31 downto 0);
           inst : out STD_LOGIC_VECTOR(31 downto 0));
end rom;

architecture Behavioral of rom is
begin
    
    process
    type rom_array_type is array(63 downto 0) of STD_LOGIC_VECTOR(31 downto 0);
    variable rom_array : rom_array_type;
    file filein : text;
    variable fstatus : FILE_OPEN_STATUS;
    variable buf : LINE;
    variable output : LINE;
    variable data : STD_LOGIC_VECTOR(31 downto 0);
    variable index : STD_LOGIC_VECTOR(5 downto 0) := "000000";
    begin
        -- 在process中，先打开文件
        file_open(fstatus, filein, "D:\Projects\VHDL projetc\rom.data", read_mode);  
        -- 这里给出的是绝对地址，因为我也不知道如果用相对地址，应该把文件放在哪里……
            while not endfile(filein) loop
                readline(filein, buf);
                -- 正常情况下，endfile(filein)就可以了，但是这里需要单独判断buf有没有读到内容
                -- 否则会报错：Error: STD_LOGIC_ll64.HREAD End of String encountered
                if buf'length = 0 then
                    exit;
                end if;
                hread(buf, data);
                rom_array(to_integer(unsigned(index))) := data;    
                index := index + "000001";
            end loop;
        -- 等待add变化
        -- 正常情况下是写在process的敏感信号中，但此处需要读完rom_array的初始数据后再开始等待
            wait on add;
            inst <= rom_array(to_integer(unsigned(add)));
    end process;

end Behavioral;
```

shift:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;
entity shift is
	port( sign_imm: in std_logic_vector(31 downto 0);
		    imm_shift: out std_logic_vector(31 downto 0));
end entity shift;
architecture behavioral of shift is
begin
--imm_shift <= sign_imm(29 downto 0) & "00";
imm_shift <= sign_imm(31 downto 0);
end architecture behavioral;
```

test_bench:

```vhdl
library ieee;
use ieee.std_logic_1164.all;
entity test_bench is	
end entity test_bench;	
architecture behavioral of test_bench is	
signal reset: std_logic;
signal clk: std_logic;		
component  cpu is
    Port ( reset : in STD_LOGIC;
           clk : in STD_LOGIC
           );
end component cpu;
	
begin
	
	cpu_0 : cpu
	
	port map ( reset => reset, clk => clk);
process
constant period : time :=2ns;	
begin
	
	wait for period;
	clk<='1';
	wait for period;
	clk<='0';
end process;
process
begin
	reset<='1';
	wait for 4ns;
	reset<='0';
	wait;
end process;
end architecture behavioral;	
```

XM:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_SIGNED.ALL;
USE IEEE.STD_LOGIC_ARITH.ALL;

entity XM IS 
	port(clk:  in std_logic;
		  --clr:   in std_logic;
		  wb_sel_i:    in std_logic;
			wb_en_i:     in std_logic;
			mem_r_en_i:  in std_logic;
			mem_w_en_i:  in std_logic;
			--bran_en_i:   in std_logic;
			rd_ad_i:     in std_logic_vector(4 downto 0);
			result_i:    in std_logic_vector(31 downto 0);
			--bran_add_i:  in std_logic_vector(31 downto 0);
			mem_data_i:  in std_logic_vector(31 downto 0);
			--bran_zero_i: in std_logic_vector;
			wb_sel  :    out std_logic;
			wb_en  :     out std_logic;
			mem_r_en  :  out std_logic;
			mem_w_en  :  out std_logic;
			--bran_en  :   out std_logic;
			rd_ad  :     out std_logic_vector(4 downto 0);
			result  :    out std_logic_vector(31 downto 0);
			--bran_add  :  out std_logic_vector(31 downto 0);
			mem_data  :  out std_logic_vector(31 downto 0));
			--bran_zero  : out std_logic_vector);
end entity XM;
	
architecture behaviora of XM is
	
begin
	
	process(clk)
		begin
			if (rising_edge(clk)) then
				   wb_sel   <= wb_sel_i;
			       wb_en    <= wb_en_i;
			       mem_r_en <=  mem_r_en_i;
			       mem_w_en <=  mem_w_en_i;
			       --bran_en  <=  bran_en_i;
			       rd_ad    <=  rd_ad_i;
			       result   <=  result_i;
			      -- bran_add <=  bran_add_i;
			       mem_data <=  mem_data_i;		     
			    end if;
	end process;
end architecture behaviora;
```

ALU:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_unSIGNED.ALL;

USE IEEE.STD_LOGIC_ARITH.ALL;

entity ALU is 
	port (
			 
			 --ex_sel1: in std_logic_vector(1 downto 0);
			 
			   aluop: in std_logic_vector(1 downto 0);
			  
			 rs_data: in std_logic_vector(31 downto 0);
			 rt_data: in std_logic_vector(31 downto 0);
			 	
			--sign_imm: in std_logic_vector(31 downto 0);
			--zero_imm: in std_logic_vector(31 downto 0);

		 
			  result: out std_logic_vector(31 downto 0));
end entity ALU;

architecture behavioural of ALU is
begin
	 
	 	process(rs_data,rt_data,aluop)
	 		
	 		begin
	 			
	 			case aluop is
	 				when "00" =>
	 					result <= rs_data and rt_data;
	 				when "01" =>
	 					result <= rs_data - rt_data;
	 				when "10" =>
	 					result <= rs_data XOR rt_data;
	 				when "11" =>
	 					result <= rs_data + rt_data;
	 				when others =>
	 					null;
	 				end case;
	 	end process;

end architecture behavioural;	 				
```

bran_adder:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity bran_adder is
	
	port( imm_shift : in std_logic_vector(31 downto 0);
		    pc4       : in std_logic_vector(31 downto 0);
		    	bran_add: out std_logic_vector(31 downto 0));

end entity bran_adder;
	
architecture behavioral of bran_adder is
	
begin
	
bran_add <= imm_shift + pc4;

end architecture behavioral;
```

compare_zero:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity compare is
	port ( rs_data: in std_logic_vector (31 downto 0);
		     rt_data: in std_logic_vector (31 downto 0);
		     bran_zero: out std_logic );
end entity compare;
	
	
architecture behavioral of compare is
	
begin
	 
	 process(rs_data, rt_data)
	 	 begin
	 	 	if rs_data = rt_data then
	 	 		bran_zero <= '1';
	 	 	else
	 	 		bran_zero <= '0';
	 	 	end if;
	 	end process;
end architecture behavioral;
```

control_unit:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_SIGNED;

USE IEEE.STD_LOGIC_ARITH.ALL;

entity controlunit is
	port (

		  opcode: in std_logic_vector(3 downto 0);
			
			hazard_detect_en:  out std_logic;
			wb_sel: out  std_logic;
			wb_en: out std_logic;
			mem_r_en: out std_logic;
			mem_w_en: out std_logic;
			bran_en: out std_logic;
			ex_sel1: out std_logic_vector(1 downto 0);
			ex_sel2: out std_logic;
			aluop: out std_logic_vector(1 downto 0);
			jr_en: out std_logic);
			
end entity controlunit;
                                  

architecture behaviora of controlunit is
	begin
	
		process(opcode) --controlunit
			begin
					case opcode is
						when "0001" => --AND
							aluop <= "00"; 
							wb_sel <= '1';
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <='0';
			       jr_en   <= '0';
			       hazard_detect_en <='1';			       
						when "0010" => --SUB
							 aluop <= "01"; 
							wb_sel <= '1';
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '0';
			       hazard_detect_en <='1';
						when "0011" => --XOR
							aluop <= "10"; 
							wb_sel <= '1';
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '0';
			      hazard_detect_en <='1';
						when "0110" => --ADD
							aluop <= "11";
							wb_sel <= '1';
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '0';
			      hazard_detect_en <='1';
						when "1010" => --JR
							aluop <= "XX";
							wb_sel <= '0';
			         wb_en <= '0';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '1';
			      hazard_detect_en <='1';
			      when "0100" => --ANDI
			      	aluop <= "00";
							wb_sel <= '1'; 
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "01";
			       ex_sel2 <= '1';
			       jr_en   <= '0';
			      hazard_detect_en <='1';
			      when "0101" => --SUBI
			      	aluop <= "01";
							wb_sel <= '1';
			         wb_en <= '1';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "01";
			       ex_sel2 <= '1';
			       jr_en   <= '0';
			      hazard_detect_en <='1';
			      when "1000" => --LW
			      	aluop <= "11";
							wb_sel <= '0';
			         wb_en <= '1';
			      mem_r_en <= '1';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "01";
			       ex_sel2 <= '1';
			       jr_en   <= '0';
			       hazard_detect_en <='1';
			      when "1001" => --SW
			      	aluop <= "11";
							wb_sel <= '0';
			         wb_en <= '0';
			      mem_r_en <= '0';
		       	mem_w_en <= '1';
			       bran_en <= '0';
			       ex_sel1 <= "01";
			       ex_sel2 <= '1';
			       jr_en   <= '0';
			       hazard_detect_en <='1';
			      when "0111" => --BEQ
			      	aluop <= "XX";
							wb_sel <= '0';
			         wb_en <= '0';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '1';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '0';
			       hazard_detect_en <='1';
			      when others =>
								aluop <= "XX";
							wb_sel <= '0';
			         wb_en <= '0';
			      mem_r_en <= '0';
		       	mem_w_en <= '0';
			       bran_en <= '0';
			       ex_sel1 <= "00";
			       ex_sel2 <= '0';
			       jr_en   <= '0';
			       hazard_detect_en <='0';
						end case;
				
			end process;
			
end architecture behaviora;
```

DX:

```VHDL
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_SIGNED;

USE IEEE.STD_LOGIC_ARITH.ALL;

entity DX is
	port ( clk:      in std_logic;
		     clr:      in std_logic;
		     --jr_i:     in std_logic;
		     --pc4_i:    in std_logic_vector(31 downto 0);
			wb_sel_i:    in std_logic;
			wb_en_i:     in std_logic;
			mem_r_en_i:  in std_logic;
			mem_w_en_i:  in std_logic;
			--bran_en_i:   in std_logic;
			ex_sel1_i:   in std_logic_vector(1 downto 0);
			ex_sel2_i:   in std_logic;
			aluop_i:     in std_logic_vector(1 downto 0);
			rd_ad1_i:    in std_logic_vector(4 downto 0);
			rd_ad2_i:    in std_logic_vector(4 downto 0);
			sign_imm_i:  in std_logic_vector(31 downto 0);
			zero_imm_i:  in std_logic_vector(31 downto 0);
			rs_data_i:   in std_logic_vector(31 downto 0);
			rt_data_i:   in std_logic_vector(31 downto 0);
				--pc4:     out std_logic_vector(31 downto 0);
				--jr:      out std_logic;
			wb_sel:    out std_logic;
			wb_en:     out std_logic;
			mem_r_en:  out std_logic;
			mem_w_en:  out std_logic;
			--bran_en:   out std_logic;
			ex_sel1:   out std_logic_vector(1 downto 0);
			ex_sel2:   out std_logic;
			aluop:     out std_logic_vector(1 downto 0);
			rd_ad1:    out std_logic_vector(4 downto 0);
			rd_ad2:    out std_logic_vector(4 downto 0);
			sign_imm:  out std_logic_vector(31 downto 0);
			zero_imm:  out std_logic_vector(31 downto 0);
				rs_data: out std_logic_vector(31 downto 0);
				rt_data: out std_logic_vector(31 downto 0));
end entity DX;
	
architecture behavioural of DX is
	
begin
	process(clk)
		begin
			if (rising_edge(clk)) then
				if clr = '1' then
					wb_en  <= '0';
          mem_w_en  <= '0';
          rd_ad1 <= "ZZZZZ";
          rd_ad2 <= "ZZZZZ";
        else 
             --jr       <=   jr_i;
			       wb_sel   <=   wb_sel_i;
			       wb_en    <=   wb_en_i;
			       mem_r_en <=   mem_r_en_i;
			       mem_w_en <=   mem_w_en_i;
			       --bran_en  <=   bran_en_i;
			       ex_sel1  <=   ex_sel1_i;
			       ex_sel2  <=   ex_sel2_i;
			       aluop    <=   aluop_i;
			       rd_ad1   <=   rd_ad1_i;
			       rd_ad2   <=   rd_ad2_i;
			       sign_imm <=   sign_imm_i;
			       zero_imm <=   zero_imm_i;
			       rs_data  <=   rs_data_i;
			       rt_data  <=   rt_data_i;
			     end if;
			    end if;
			 end process;
end architecture behavioural;
```

FD:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_SIGNED;

USE IEEE.STD_LOGIC_ARITH.ALL;

entity FD is
	port (inst_i: in std_logic_vector(31 downto 0);
		clr: in std_logic;
		en: in std_logic;
		clk: in std_logic;
		npc4_i: in std_logic_vector(31 downto 0);
			inst_o: out std_logic_vector(31 downto 0);
			npc4_o: out std_logic_vector(31 downto 0)
			);
end entity FD;


architecture behavioral of FD is
	begin
		process(clk)
			begin
				if (rising_edge(clk)) then
				if clr = '1' then
					inst_o <= x"00000000";
					npc4_o <= x"00000000";
				elsif en = '1' then
				   inst_o <= inst_i;
				   npc4_o <= npc4_i;
				 end if;
				end if;
				end process;
end architecture behavioral;
```

hazard_detect:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity hazard_detect is
	
	port(		  hazard_detect_en      :  in std_logic;
						ex_rd_ad:  in std_logic_vector (4 downto 0);
		 			  mem_rd_ad: in std_logic_vector (4 downto 0);
		  			rs_ad:     in std_logic_vector(4 downto 0);
		 				rt_ad:     in std_logic_vector(4 downto 0);
		 				bran_en:   in std_logic;
		 				bran_zero: in std_logic;
		 				jr_en   :  in std_logic;
						FD_en: 		 out std_logic; 
						PC_en: 		 out std_logic;
						DX_clr:		 out std_logic;
						FD_clr:    out std_logic;
						pc_mux_sel: out std_logic_vector(1 downto 0));
end entity hazard_detect;
	
	
architecture behavioral of hazard_detect is

begin

		process(rs_ad,rt_ad,ex_rd_ad,mem_rd_ad) ---data_hazard_detect
				begin
					if (rs_ad /= "00000" and rs_ad /= "UUUUU" and rs_ad /= "XXXXX" and rt_ad /= "00000" and rt_ad /= "UUUUU" and rt_ad /= "XXXXX" ) then 
					if (rs_ad = ex_rd_ad or rt_ad = ex_rd_ad or rs_ad = mem_rd_ad or rt_ad = mem_rd_ad) then
						FD_en <= '0';
						PC_en <= '0';
						DX_clr <= '1';
					else 
						FD_en <= '1';
						PC_en <= '1';
						DX_clr <= '0';
					end if;
				else
					FD_en <= '1';
					PC_en <= '1';
					DX_clr <= '0';
				end if;
		end process;
		
		process(bran_en, bran_zero,jr_en ) --- beq and jr detect
			begin
				if bran_en = '1' and bran_zero = '1' then
					pc_mux_sel <= "01";
					FD_clr <= '1';
				elsif jr_en = '1' then
					pc_mux_sel <= "10";
					FD_clr <= '1';
				else
					pc_mux_sel <= "00";
					FD_clr <= '0';
				end if;
		end process;
		
end architecture behavioral;
```

ID:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.STD_LOGIC_SIGNED;

USE IEEE.STD_LOGIC_ARITH.ALL;

entity ID is 
	port(ins_i: in std_logic_vector (31 downto 0);
		opcode: out std_logic_vector(3 downto 0);
				rs_ad: out std_logic_vector(4 downto 0);
					rt_ad: out std_logic_vector(4 downto 0);
						rd_ad1:out std_logic_vector(4 downto 0);
							rd_ad2:out std_logic_vector(4 downto 0);
								sign_imm: out std_logic_vector(31 downto 0);
									zero_imm: out std_logic_vector(31 downto 0));
end entity ID;
	
architecture behavioural of ID is
	begin
		process(ins_i)
			begin
				  opcode  <= ins_i(31 downto 28);
        	rs_ad   <= ins_i(27 downto 23);
        	rt_ad   <= ins_i(22 downto 18);
        	rd_ad1  <= ins_i(17 downto 13);
        	rd_ad2  <= ins_i(22 downto 18);
        	sign_imm(31 downto 18) <= (others=>ins_i(17));
          sign_imm(17 downto 0) <= ins_i(17 downto 0) ;
          zero_imm(31 downto 18) <= (others=>'0');
          zero_imm(17 downto 0) <= ins_i(17 downto 0) ;
          end process;
end architecture behavioural;
```

MW:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity MW is 
	port ( clk:      in std_logic;
		     wb_sel_i: in std_logic;
		     wb_en_i:  in std_logic;
		     mem_r_data_i: in std_logic_vector(31 downto 0);
		     result_i:     in std_logic_vector(31 downto 0);
		     rd_ad_i:      in std_logic_vector(4 downto 0);
		     wb_sel:     out std_logic;
		     wb_en:      out std_logic;
		     mem_r_data: out std_logic_vector(31 downto 0);
		     result:     out std_logic_vector(31 downto 0);
		     rd_ad:      out std_logic_vector(4 downto 0));
end entity;
	
architecture behavioral of MW is
	
	begin
		
		process(clk)
			begin
				if (rising_edge(clk)) then
					wb_sel      <= wb_sel_i;
		      wb_en       <= wb_en_i;
		      mem_r_data  <= mem_r_data_i;
		      result      <= result_i;
		      rd_ad       <= rd_ad_i;
		    end if;
		end process;
end architecture behavioral;
```

ins:

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity cpu is
    Port ( reset : in STD_LOGIC;
           clk : in STD_LOGIC
           );
end entity cpu;




architecture behavioral of cpu is


component PC is
	
	port( pc_i: in std_logic_vector(31 downto 0);
				pc	:	out std_logic_vector(31 downto 0);
				clk : in std_logic;
				en  : in std_logic;
				reset: in std_logic);
end component PC;
	
	
	
component pc4_adder is
	
	port (pc_i : in std_logic_vector(31 downto 0);
				pc4  : out std_logic_vector(31 downto 0));
end component pc4_adder;



component pc_mux is
	port ( sel : in std_logic_vector(1 downto 0);
				bran_add: in std_logic_vector(31 downto 0);
					jr_add:	in std_logic_vector(31 downto 0);
						pc4:  in std_logic_vector(31 downto 0);
						pc:  out std_logic_vector(31 downto 0));
end component pc_mux;


component rom is
    Port ( add : in STD_LOGIC_VECTOR(31 downto 0);
           inst : out STD_LOGIC_VECTOR(31 downto 0));
end component rom;



component FD is
	port (inst_i: in std_logic_vector(31 downto 0);
		clr: in std_logic;
		en: in std_logic;
		clk: in std_logic;
		npc4_i: in std_logic_vector(31 downto 0);
			inst_o: out std_logic_vector(31 downto 0);
			npc4_o: out std_logic_vector(31 downto 0)
			);
end component FD;



component ID is 
	port(ins_i: in std_logic_vector (31 downto 0);
		opcode: out std_logic_vector(3 downto 0);
				rs_ad: out std_logic_vector(4 downto 0);
					rt_ad: out std_logic_vector(4 downto 0);
						rd_ad1:out std_logic_vector(4 downto 0);
							rd_ad2:out std_logic_vector(4 downto 0);
								sign_imm: out std_logic_vector(31 downto 0);
									zero_imm: out std_logic_vector(31 downto 0));
end component ID;



component controlunit is
	port (

		  opcode: in std_logic_vector(3 downto 0);
			
			hazard_detect_en:  out std_logic;
			wb_sel: out  std_logic;
			wb_en: out std_logic;
			mem_r_en: out std_logic;
			mem_w_en: out std_logic;
			bran_en: out std_logic;
			ex_sel1: out std_logic_vector(1 downto 0);
			ex_sel2: out std_logic;
			aluop: out std_logic_vector(1 downto 0);
			jr_en: out std_logic);
			
end component controlunit;



component hazard_detect is
	
	port(		  hazard_detect_en :  in std_logic;
						ex_rd_ad:  in std_logic_vector (4 downto 0);
		 			  mem_rd_ad: in std_logic_vector (4 downto 0);
		  			rs_ad:     in std_logic_vector(4 downto 0);
		 				rt_ad:     in std_logic_vector(4 downto 0);
		 				bran_en:   in std_logic;
		 				bran_zero: in std_logic;
		 				jr_en    : in std_logic;
						FD_en: 		 out std_logic; 
						PC_en: 		 out std_logic;
						DX_clr:		 out std_logic;
						FD_clr:    out std_logic;
						pc_mux_sel: out std_logic_vector(1 downto 0));
end component hazard_detect;





component regf is
    Port ( rs_ad : in STD_LOGIC_VECTOR(4 downto 0);
    	     rt_ad : in std_logic_vector(4 downto 0);
    	     rd_ad : in std_logic_vector(4 downto 0);
    	     rd_data: in std_logic_vector(31 downto 0);
    	     w_en: in std_logic;
    	     clk:  in std_logic;
           rs_data : out STD_LOGIC_VECTOR(31 downto 0);
           rt_data : out STD_LOGIC_VECTOR(31 downto 0);
           	reset: in std_logic);
end component regf;



component bran_adder is
	
	port( imm_shift : in std_logic_vector(31 downto 0);
		    pc4       : in std_logic_vector(31 downto 0);
		    	bran_add: out std_logic_vector(31 downto 0));

end component bran_adder;



component compare is
	port ( rs_data: in std_logic_vector (31 downto 0);
		     rt_data: in std_logic_vector (31 downto 0);
		     bran_zero: out std_logic );
end component compare;



component shift is
	port( sign_imm: in std_logic_vector(31 downto 0);
		    imm_shift: out std_logic_vector(31 downto 0));
end component shift;



component DX is
	port ( clk:      in std_logic;
		     clr:      in std_logic;
		     --jr_i:     in std_logic;
		     --pc4_i:    in std_logic_vector(31 downto 0);
			wb_sel_i:    in std_logic;
			wb_en_i:     in std_logic;
			mem_r_en_i:  in std_logic;
			mem_w_en_i:  in std_logic;
			--bran_en_i:   in std_logic;
			ex_sel1_i:   in std_logic_vector(1 downto 0);
			ex_sel2_i:   in std_logic;
			aluop_i:     in std_logic_vector(1 downto 0);
			rd_ad1_i:    in std_logic_vector(4 downto 0);
			rd_ad2_i:    in std_logic_vector(4 downto 0);
			sign_imm_i:  in std_logic_vector(31 downto 0);
			zero_imm_i:  in std_logic_vector(31 downto 0);
			rs_data_i:   in std_logic_vector(31 downto 0);
			rt_data_i:   in std_logic_vector(31 downto 0);
				--pc4:     out std_logic_vector(31 downto 0);
				--jr:      out std_logic;
			wb_sel:    out std_logic;
			wb_en:     out std_logic;
			mem_r_en:  out std_logic;
			mem_w_en:  out std_logic;
			--bran_en:   out std_logic;
			ex_sel1:   out std_logic_vector(1 downto 0);
			ex_sel2:   out std_logic;
			aluop:     out std_logic_vector(1 downto 0);
			rd_ad1:    out std_logic_vector(4 downto 0);
			rd_ad2:    out std_logic_vector(4 downto 0);
			sign_imm:  out std_logic_vector(31 downto 0);
			zero_imm:  out std_logic_vector(31 downto 0);
				rs_data: out std_logic_vector(31 downto 0);
				rt_data: out std_logic_vector(31 downto 0));
end component DX;
	



component ALU is 
	port (
			 
			 ---ex_sel1: in std_logic_vector(1 downto 0);
			 
			   aluop: in std_logic_vector(1 downto 0);
			  
			 rs_data: in std_logic_vector(31 downto 0);
			 rt_data: in std_logic_vector(31 downto 0);
			 	
			--sign_imm: in std_logic_vector(31 downto 0);
			---zero_imm: in std_logic_vector(31 downto 0);

		 
			  result: out std_logic_vector(31 downto 0));
end component ALU;



component operand2_mux is
	
	port(rt_data: in std_logic_vector(31 downto 0);
		   sign_imm: in std_logic_vector(31 downto 0);
		   	zero_imm: in std_logic_vector(31 downto 0);
		   	ex_sel1:  in std_logic_vector(1 downto 0);
		   	operand2: out std_logic_vector(31 downto 0)
		   	);
end component operand2_mux;



component rd_ad_mux is 
	port ( rd_ad1: in std_logic_vector(4 downto 0);
		     rd_ad2: in std_logic_vector(4 downto 0);
		    ex_sel2: in std_logic;
		     	rd_ad: out std_logic_vector(4 downto 0));
end component rd_ad_mux;
	
	
	
component XM IS 
	port(clk:  in std_logic;
		 --- clr:   in std_logic;
		  wb_sel_i:    in std_logic;
			wb_en_i:     in std_logic;
			mem_r_en_i:  in std_logic;
			mem_w_en_i:  in std_logic;
			--bran_en_i:   in std_logic;
			rd_ad_i:     in std_logic_vector(4 downto 0);
			result_i:    in std_logic_vector(31 downto 0);
			--bran_add_i:  in std_logic_vector(31 downto 0);
			mem_data_i:  in std_logic_vector(31 downto 0);
			--bran_zero_i: in std_logic_vector;
			wb_sel  :    out std_logic;
			wb_en  :     out std_logic;
			mem_r_en  :  out std_logic;
			mem_w_en  :  out std_logic;
			--bran_en  :   out std_logic;
			rd_ad  :     out std_logic_vector(4 downto 0);
			result  :    out std_logic_vector(31 downto 0);
			--bran_add  :  out std_logic_vector(31 downto 0);
			mem_data  :  out std_logic_vector(31 downto 0));
			--bran_zero  : out std_logic_vector);
end component XM;
	
	
	
component ram is
	port ( --wb_sel_i  :    in std_logic;
			   --wb_en_i  :     in std_logic;
			   mem_r_en  :  in std_logic;
			   mem_w_en  :  in std_logic;
			  -- bran_en  :   in std_logic;
			   --rd_ad_i  :     in std_logic_vector(4 downto 0);
			   result_i  :    in std_logic_vector(31 downto 0);
			   reset: in std_logic;
			   mem_data  :  in std_logic_vector(31 downto 0);
			   --bran_zero  : in std_logic;
			   --pc_sel1:     out std_logic;
			   mem_r_data:  out std_logic_vector(31 downto 0));
			   --result:      out std_logic_vector(31 downto 0));
			   --rd_ad:       out std_logic_vector(4 downto 0);
			   --wb_sel  :    out std_logic;
			   --wb_en   :    out std_logic);
end component ram;
	


component MW is 
	port ( clk:      in std_logic;
		     wb_sel_i: in std_logic;
		     wb_en_i:  in std_logic;
		     mem_r_data_i: in std_logic_vector(31 downto 0);
		     result_i:     in std_logic_vector(31 downto 0);
		     rd_ad_i:      in std_logic_vector(4 downto 0);
		     wb_sel:     out std_logic;
		     wb_en:      out std_logic;
		     mem_r_data: out std_logic_vector(31 downto 0);
		     result:     out std_logic_vector(31 downto 0);
		     rd_ad:      out std_logic_vector(4 downto 0));
end component MW;

component rd_data_mux is
	port( wb_sel: in std_logic;
		    result: in std_logic_vector(31 downto 0);
		    mem_r_data: in std_logic_vector(31 downto 0);
		    rd_data: out std_logic_vector(31 downto 0));
end component rd_data_mux;


signal pc4: std_logic_vector (31 downto 0);
signal pc4_id: std_logic_vector (31 downto 0);
signal pc_to_rom: std_logic_vector (31 downto 0);
signal npc: std_logic_vector (31 downto 0);
signal pc_mux_sel: std_logic_vector (1 downto 0);
signal bran_add: std_logic_vector (31 downto 0);
--signal jr_add: std_logic_vector (31 downto 0);
signal pc_en: std_logic;
signal inst_i: std_logic_vector (31 downto 0);
signal inst_o: std_logic_vector (31 downto 0);
signal FD_clr: std_logic;
signal FD_en: std_logic;
signal DX_clr: std_logic;
signal opcode:STD_LOGIC_VECTOR(3 downto 0);
signal rs_ad: std_logic_vector (4 downto 0);
signal rt_ad: std_logic_vector (4 downto 0);
signal rs_data_id: std_logic_vector (31 downto 0);
signal rt_data_id: std_logic_vector (31 downto 0);
signal rs_data_ex:std_logic_vector(31 downto 0);
signal rt_data_ex: std_logic_vector (31 downto 0);
signal hazard_detect_en:  std_logic;
signal operand2:std_logic_vector (31 downto 0);
signal result_ex:std_logic_vector(31 downto 0);
signal result_mem:std_logic_vector(31 downto 0);
signal result_wb:std_logic_vector(31 downto 0);
signal rd_data: std_logic_vector (31 downto 0);
signal rd_ad_wb: std_logic_vector (4 downto 0);
signal rd_ad_mem: std_logic_vector (4 downto 0);
signal rd_ad_ex: std_logic_vector (4 downto 0);
signal rd_ad1_id: std_logic_vector (4 downto 0);
signal rd_ad2_id: std_logic_vector (4 downto 0);
signal rd_ad1_ex: std_logic_vector (4 downto 0);
signal rd_ad2_ex: std_logic_vector (4 downto 0);
signal wb_en: std_logic;
signal wb_en_mem: std_logic;
signal wb_en_ex: std_logic;
signal wb_en_id: std_logic;
signal wb_sel_id: std_logic;
signal wb_sel_ex: std_logic;
signal wb_sel_mem: std_logic;
signal wb_sel: std_logic;
signal mem_r_en_id: std_logic;
signal mem_r_en_ex: std_logic;
signal mem_r_en: std_logic;
signal mem_w_en_id: std_logic;
signal mem_w_en_ex: std_logic;
signal mem_w_en: std_logic;
signal mem_w_data: std_logic_vector(31 downto 0);
signal mem_r_data: std_logic_vector(31 downto 0);
signal mem_r_data_wb:std_logic_vector(31 downto 0);
signal aluop_id: std_logic_vector(1 downto 0);
signal aluop: std_logic_vector(1 downto 0);
signal operand2_sel_id: std_logic_vector (1 downto 0);
signal operand2_sel: std_logic_vector (1 downto 0);
signal rd_ad_sel_id: std_logic;
signal rd_ad_sel: std_logic;
signal bran_zero: std_logic;
signal bran_en: std_logic;
signal jr_en: std_logic;
signal imm_shift: std_logic_vector (31 downto 0);
signal sign_imm_id: std_logic_vector (31 downto 0);
signal sign_imm_ex: std_logic_vector (31 downto 0);
signal zero_imm_id: std_logic_vector (31 downto 0);
signal zero_imm_ex: std_logic_vector (31 downto 0);


begin
    PC_0: PC
     port map(reset => reset , clk => clk, pc_i => npc, pc => pc_to_rom, en => pc_en);
     	
     	pc4_adder_0: pc4_adder
     	Port map (pc_i => pc_to_rom, pc4 => pc4);
     	
     	
     pc_mux_0 : pc_mux
     	port map(sel => pc_mux_sel, bran_add => bran_add, jr_add =>rs_data_id, pc4 =>pc4, pc => npc);
     		
     	rom_0 :rom
     	port map(add => pc_to_rom, inst => inst_i);
     		
     	FD_0 : FD
     	port map(inst_i => inst_i, clr => FD_clr, en => FD_en, clk => clk, npc4_i =>
     		pc4, inst_o => inst_o, npc4_o => pc4_id );
     		
     	ID_0 : ID
     	port map(ins_i => inst_o, opcode=>opcode, rs_ad=>rs_ad, rt_ad=>rt_ad,
     		rd_ad1=> rd_ad1_id, rd_ad2=>rd_ad2_id, sign_imm=>sign_imm_id, zero_imm=>zero_imm_id);
     		
     	controlunit_0 : controlunit
     	port map(opcode=>opcode, wb_sel=>wb_sel_id, wb_en=>wb_en_id, mem_r_en=>mem_r_en_id,
     		mem_w_en=>mem_w_en_id, bran_en=>bran_en, ex_sel1=>operand2_sel_id, ex_sel2=>
     		rd_ad_sel_id, aluop=>aluop_id, jr_en=>jr_en, hazard_detect_en => hazard_detect_en);
     		
     	hazard_detect_0 : hazard_detect
     	port map(ex_rd_ad=>rd_ad_ex, mem_rd_ad=>rd_ad_mem, rs_ad=>rs_ad, rt_ad=>rt_ad,bran_en=>bran_en,
     		bran_zero => bran_zero, jr_en=>jr_en, FD_en=>FD_en, PC_en=>pc_en, DX_clr=>DX_clr,
     		FD_clr=>FD_clr, pc_mux_sel=>pc_mux_sel, hazard_detect_en=> hazard_detect_en);
     		
     	
      regf_0 : regf
      port map(rs_ad => rs_ad, rt_ad=>rt_ad, rd_ad=>rd_ad_wb, rd_data=>rd_data, w_en=>wb_en, clk=>clk,
      	rs_data=>rs_data_id, rt_data=>rt_data_id, reset=>reset );
      	
      	
      bran_adder_0 : bran_adder
      port map(imm_shift=>imm_shift, pc4=>pc4_id, bran_add=>bran_add);
      	
      	
      
     	compare_0: compare
     	port map(rs_data=>rs_data_id, rt_data=>rt_data_id,bran_zero=>bran_zero);
     		
     	
     	
     	shift_0 : shift
     	Port map(sign_imm=>sign_imm_id, imm_shift => imm_shift);
     		
     		
     	DX_0 : DX
     	port map(clk=>clk,
		     clr     => DX_clr,
			wb_sel_i   => wb_sel_id,
			wb_en_i    => wb_en_id,
			mem_r_en_i => mem_r_en_id,
			mem_w_en_i => mem_w_en_id,
			ex_sel1_i  => operand2_sel_id,
			ex_sel2_i  => rd_ad_sel_id,
			aluop_i    =>  aluop_id,
			rd_ad1_i   =>  rd_ad1_id,
			rd_ad2_i   =>  rd_ad2_id,
			sign_imm_i =>   sign_imm_id,
			zero_imm_i =>   zero_imm_id,
			rs_data_i  =>   rs_data_id,
			rt_data_i  =>   rt_data_id,
			wb_sel    => wb_sel_ex,
			wb_en      => wb_en_ex,
			mem_r_en   => mem_r_en_ex,
			mem_w_en   => mem_w_en_ex,
			ex_sel1   => operand2_sel,
			ex_sel2    => rd_ad_sel,
			aluop     => aluop,
			rd_ad1    => rd_ad1_ex,
			rd_ad2    => rd_ad2_ex,
			sign_imm   => sign_imm_ex,
			zero_imm   => zero_imm_ex,
				rs_data  => rs_data_ex,
				rt_data  => rt_data_ex);
				
				
			ALU_0 : ALU
			port map(aluop=>aluop, rs_data=>rs_data_ex, rt_data=>operand2, result=>result_ex);
		operand2_mux_0 : operand2_mux
     	port map(rt_data=>rt_data_ex, sign_imm=>sign_imm_ex, zero_imm=>zero_imm_ex,
     		ex_sel1=>operand2_sel, operand2=>operand2);
     		
     		
     	rd_ad_mux_0: rd_ad_mux
     	port map(rd_ad1=>rd_ad1_ex, rd_ad2=>rd_ad2_ex, ex_sel2=>rd_ad_sel, rd_ad=>rd_ad_ex);
     		
     	
     	XM_0: XM
     	port map(wb_sel_i => wb_sel_ex,
			       wb_en_i => wb_en_ex,
			        mem_r_en_i => mem_r_en_ex,
			        mem_w_en_i => mem_w_en_ex,
			        clk => clk,
			        rd_ad_i => rd_ad_ex,
			        result_i => result_ex,
			        
			        mem_data_i => rt_data_ex,
			       wb_sel    => wb_sel_mem,
			       wb_en     => wb_en_mem,
			       mem_r_en  => mem_r_en,
			       mem_w_en  => mem_w_en,
			       
			       rd_ad     => rd_ad_mem,
			       result    => result_mem,
			       
			       mem_data  => mem_w_data );
			       
			   ram_0 :ram
			   port map (mem_r_en=>mem_r_en, mem_w_en=> mem_w_en, result_i=>result_mem,
			   	mem_data=> mem_w_data, mem_r_data=>mem_r_data, reset => reset);
			   	
			   	
			   MW_0 : MW
			   port map(clk=>clk,
			   	wb_sel_i=>wb_sel_mem,
			   	wb_en_i=>wb_en_mem,
			   	mem_r_data_i =>mem_r_data,
			   	result_i=>result_mem,
			   	rd_ad_i=>rd_ad_mem,
			   	wb_sel=>wb_sel,
			   	wb_en=>wb_en,
			   	mem_r_data=>mem_r_data_wb,
			   	result=>result_wb,
			   	rd_ad=>rd_ad_wb);
			   	
			   	
			   	rd_data_mux_0 : rd_data_mux
			   	port map (wb_sel=>wb_sel,result=>result_wb,
			   		 mem_r_data=>mem_r_data_wb, rd_data=>rd_data);    	
end Behavioral;
```

operand2_mux:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

 
entity operand2_mux is
	
	port(rt_data: in std_logic_vector(31 downto 0);
		   sign_imm: in std_logic_vector(31 downto 0);
		   	zero_imm: in std_logic_vector(31 downto 0);
		   	ex_sel1:  in std_logic_vector(1 downto 0);
		   	operand2: out std_logic_vector(31 downto 0)
		   	);
end entity operand2_mux;
	
architecture behavioral of operand2_mux is
	
begin
	
	process(rt_data,sign_imm,zero_imm,ex_sel1)
		
		begin
			
			if ex_sel1 = "10" then
				operand2 <= zero_imm;
			elsif ex_sel1= "01" then
				operand2 <= sign_imm;
			else
				operand2 <= rt_data;
			end if;
			end process;
end architecture behavioral;
```

pc4_adder:

```vhdl
LIBRARY  IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use STD.TEXTIO.ALL;
use IEEE.STD_LOGIC_TEXTIO.ALL;

entity pc4_adder is
	
	port (pc_i : in std_logic_vector(31 downto 0);
				pc4  : out std_logic_vector(31 downto 0));
end entity pc4_adder;
	
architecture behavioral of pc4_adder is
	
begin
		
		pc4 <= pc_i + 1;
		
end architecture behavioral;	
```



***Report:***

<img src="{{site.baseurl}}/assets/img/6741-1.png">

<img src="{{site.baseurl}}/assets/img/6741-2.png">

<img src="{{site.baseurl}}/assets/img/6741-3.png">

<img src="{{site.baseurl}}/assets/img/6741-4.png">

<img src="{{site.baseurl}}/assets/img/6741-5.png">

<img src="{{site.baseurl}}/assets/img/6741-6.png">

<img src="{{site.baseurl}}/assets/img/6741-7.png">

<img src="{{site.baseurl}}/assets/img/6741-8.png">

<img src="{{site.baseurl}}/assets/img/6741-9.png">

<img src="{{site.baseurl}}/assets/img/6741-10.png">

<img src="{{site.baseurl}}/assets/img/6741-11.png">

<img src="{{site.baseurl}}/assets/img/6741-12.png">

<img src="{{site.baseurl}}/assets/img/6741-13.jpg">

<img src="{{site.baseurl}}/assets/img/6741-14.jpg">