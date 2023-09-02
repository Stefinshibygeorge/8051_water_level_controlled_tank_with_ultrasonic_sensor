#include<reg51.h>
#include<intrins.h>

#define LCD_PORT P0         // Lcd connected to these ports
sbit rs = P1^0;             // lcd pins
//sbit rw = P1^1;
sbit en = P1^2;


sbit trig_pin = P3^5;      // ultrasonic pins
sbit echo_pin = P3^2;

sbit pump_pin = P2^0;

sfr16 DPTR=0x82;



void delay(unsigned int time);  // delay in us
void lcd_setup();
void lcd_cmd(unsigned char command);
void lcd_dat(unsigned char dat);
void lcd_show(unsigned char* str);
void ultra_trigger();
void start_timer0();
unsigned char get_range();
int distance();


void delay(unsigned int time)     //delay in us
{
	unsigned int i;
	for(i = 0;i < time;i++)
	{
	   _nop_();
	}
}

void lcd_setup() // setup lcd by senting commands
{
     lcd_cmd(0x38);
     delay(1);	
     lcd_cmd(0x0E);
     delay(1);
     lcd_cmd(0x01);
     delay(1);
     lcd_cmd(0x80);
     delay(1);
}

void lcd_cmd(unsigned char cmd)         // setup lcd to recieve commands
{
	LCD_PORT = cmd;
	rs = 0;
	//rw = 0;      (rw pin is grounded instear of using a 8051 pin)
	en = 0;
	delay(100);
	en = 1;
}

void lcd_dat(unsigned char dat)         // set lcd to write data
{
	LCD_PORT = dat;
	rs = 1;
	//rw = 0;  (rw pin is grounded instear of using a 8051 pin)
	en = 0;
	delay(100);
	en = 1;
}

void lcd_show(unsigned char* str)       //display a string on lcd
{
	while(*str != '\0')
	{
	 lcd_dat(*str++);
	}
}

void ultra_trigger()
{
	trig_pin = 1;
	delay(10);
	trig_pin = 0;
}

void start_timer0()
{
	TMOD = 0x01;
	TH0 = TL0 = 0x00;
	TR0 = 1;
}

unsigned char get_range()             // measure distance
{
	unsigned char distance;
	ultra_trigger();
	start_timer0();
	while(!echo_pin);
        while(echo_pin);
	TR0 = 0;
	DPH=TH0;
        DPL=TL0;
	distance=DPTR/59;
  return distance;
}

int distance()
{
	unsigned int range;
	lcd_cmd(0x88);
	range = get_range();
	range = range - 35;                 // self adjustments
        range = 10 - range;        
	lcd_dat((range/100)+48);           // transfer the distance (integer value character by character) to lcd
        lcd_dat(((range/10)%10)+48);
        lcd_dat(((range%10)+48));
        lcd_show("  ppd");
	delay(1000000);
	return range;
}

void main()
{ 
	int level;
	lcd_setup();
	lcd_show("LEVEL: ");
	while(1)
	 {
		level = distance();
		while(level < 2)                          // tank starts to pump when level goes beyond 2
		{
		 lcd_cmd(0xC0);
		 lcd_show("Pumping...");
		 pump_pin = 0;
		 delay(1000000);
	       level = distance();
		 while(level < 9)                        // tank is full at level = 9
		 {
			 level = distance();
			 delay(1000000);
		 }
		 lcd_cmd(0xC0);
		 lcd_show("Pumped.");
		 delay(1000000);
		 pump_pin = 1;
		}
	 } 
 }	
