// traffic light controller
// CSE140L 3-street, 12-state version
// inserts all-red after each yellow
// uses enumerated variables for states and for red-yellow-green
// 5 after traffic, 10 max cycles for green after conflict
// starter (shell) -- you need to complete the always_comb logic
import light_package ::*;           // defines red, yellow, green

// same as Harris & Harris 4-state, but we have added two all-reds
module traffic_light_controller1(
  input         clk, reset, 
                ew_str_sensor, ew_left_sensor, ns_sensor,  // traffic sensors, east-west straight, east-west left, north-south 
  output colors ew_str_light, ew_left_light, ns_light);    // traffic lights, east-west straight, east-west left, north-south

// HRR = red-red following YRR; RRH = red-red following RRY;
// ZRR = 2nd cycle yellow, follows YRR, etc. 
  typedef enum {GRR, YRR, ZRR, HRR, RGR, RYR, RZR, RHR, RRG, RRY, RRZ, RRH} tlc_states;  
  tlc_states    present_state, next_state, previous_state;
  integer ctr5, next_ctr5,       //  5 sec timeout when my traffic goes away
          ctr10, next_ctr10;     // 10 sec limit when other traffic presents

// sequential part of our state machine (register between C1 and C2 in Harris & Harris Moore machine diagram
// combinational part will reset or increment the counters and figure out the next_state
  always_ff @(posedge clk)
    if(reset) begin
	  present_state <= RRH;
      ctr5          <= 0;
      ctr10         <= 0;
    end  
	else begin
	  present_state <= next_state;
      ctr5          <= next_ctr5;
      ctr10         <= next_ctr10;
    end  


  // State table:
	// State | EW straight  |  EW left arrow | NS (straight or unprotected left)
	// 000        Red				      Red                       Red				(No traffic red)
	// 001        Green           Red                       Red
	// 010 		    Yellow			    Red							          Red
	// 011 		    Red				      Green							        Red
	// 100 		    Red             Yellow						        Red
	// 101 		    Red					    Red							          Green
	// 110		    Red					    Red							          Yellow
	// 111		    Red					    Red							          Red				(Transition red)
	
	// Fair decision doctrine: EWS > EWL > NS 

// combinational part of state machine ("C1" block in the Harris & Harris Moore machine diagram)
// default needed because only 6 of 8 possible states are defined/used
  always_comb begin
    next_state = RRH;            // default to reset state
    next_ctr5  = 0; 	         // default to clearing counters
    next_ctr10 = 0;
    case(present_state)
/* ************* Fill in the case statements ************** */
	  // East-West Straight
    GRR: begin 
         // when is next_state GRR? YRR? --> if no traffic for 1 cycle, set off counter5 then 2 yellows
          if(!ew_str_sensor) begin // there is a gap in this street
            next_state = YRR;
            next_ctr5  = 1;
          end else if(ew_left_sensor) begin // there is no gap BUT there is traffice waiting on E/W turn streets
            next_state = RGR;
            next_ctr10 = 1;
          end else if(ns_sensor) begin // there is no gap BUT there is traffice waiting on N/S streets
            next_state = RRG;
            next_ctr10 = 1;
          end
          else begin
              next_state = GRR; // no gap in traffic NOR traffic waiting on other streets
          end
         
         // what does ctr5 do? ctr10?
      end 
    YRR: begin
         // when is next_state YRR? ZRR? HRR?
         next_state = ZRR;
         // what does ctr5 do? ctr10?
      end
    ZRR: begin
         // when is next_state YRR? ZRR? HRR?
         next_state = HRR;
         // what does ctr5 do? ctr10?
      end
    HRR: begin
         // when is next_state RGR? RYR? RZR? RHR?
         next_state = RGR; // check priority using a previous_state variable
         // what does ctr5 do? ctr10?
      end
    
    // East-West Left turn
    RGR: begin
         // when is next_state RGR? RYR? RZR? RHR?
         if(!ew_left_sensor) begin // there is a gap in this street
              next_state = RYR;
              next_ctr5  = 1;
         end else if(ns_sensor) begin // there is no gap BUT there is traffice waiting on N/S  streets
            next_state = RGR;
            next_ctr10 = 1;
         end else if(ew_str_sensor) begin // there is no gap BUT there is traffice waiting on E/W  streets
            next_state = GRR;
            next_ctr10 = 1;
          end
          else begin
              next_state = RGR; // no gap in traffic NOR traffic waiting on other streets 
          end
         // what does ctr5 do? ctr10?
      end
    RYR: begin
         // when is next_state RGR? RYR? RZR? RHR?
         next_state = RZR;
         // what does ctr5 do? ctr10?
      end
    RZR: begin
         // when is next_state RGR? RYR? RZR? RHR?
        next_state = RHR;
         // what does ctr5 do? ctr10?
      end
    RHR: begin
         // when is next_state RGR? RYR? RZR? RHR?
         next_state = RRG; // check priority
         // what does ctr5 do? ctr10?
      end
    
    // North South (only has straight)
    RRG: begin
         // when is next_state RRG? RRY? RRZ? RRH?
         if(!ns_sensor) begin
              next_state = RRY;
              next_ctr5  = 1;
         end else if(ew_str_sensor) begin
            next_state = RRG;
            next_ctr10 = 1;
         end
          else begin
              next_state = RRG;
          end
         // what does ctr5 do? ctr10?
      end
    RRY: begin
         // when is next_state RRG? RRY? RRZ? RRH?
         next_state = RRZ;
         // what does ctr5 do? ctr10?
      end
    RRZ: begin
         // when is next_state RRG? RRY? RRZ? RRH?
         next_state = RRH;
         // what does ctr5 do? ctr10?
      end
    RRH: begin
         // when is next_state RRG? RRY? RRZ? RRH?
         next_state = GRR; // check priority
         // what does ctr5 do? ctr10?
      end
     // etc. 
    endcase
  end

  always_comb begin
    next_state = HR;            // default to reset state
    case(present_state)
// if green for street A, stay green if traffic, go to yellow if not
	GR:  if(!Ta)     next_state = YR;
	     else        next_state = GR;
// yellow for A street lasts one clock cycle, then on to all red 1
	YR:  next_state = HR;	   // all_red_1	  ZR
//  ZR:  next_state = HR;
// first all-red state; give precedence to other street for green
	HR:  if(Tb)      next_state = RG;    
    	 else if(Ta) next_state = GR;
		 else        next_state = HR;
// green in the other direction looks only at Tb (traffic on its own street)
	RG:  if(!Tb)     next_state = RY;
	     else        next_state = RG;
// yellow for B street goes to all red 2 
	RY:  next_state = RH;
//	RZ:
// second all-red state -- not "fairness" reversal of priorities
	RH:  if(Ta)       next_state = GR;
	     else if(Tb)  next_state = RG;
		 else         next_state = RH;
    endcase
  end

// combination output driver  ("C2" block in the Harris & Harris Moore machine diagram)
// Displays the colors of the lights after the combinational logic has determined the next state
  always_comb begin
    ew_str_light  = red;                // cover all red plus undefined cases
	  ew_left_light = red;
	  ns_light      = red;
    case(present_state)      // Moore machine
      GRR:     ew_str_light  = green;
	    YRR,ZRR: ew_str_light  = yellow;  // my dual yellow states -- brute force way to make yellow last 2 cycles
	    RGR:     ew_left_light = green;
	    RYR,RZR: ew_left_light = yellow;
	    RRG:     ns_light      = green;
	    RRY,RRZ: ns_light      = yellow;
    endcase
  end

endmodule