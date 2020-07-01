# RFID-home-sequrity-lock


#include<reg51.h>
#include<string.h>
sbit buzzer = P2^3;
sbit m1 = P0^1;
sbit m2 = P0^2;
const char *CARD1="4500412CF8DF";
//int card_day1=0;
const char *CARD2="4500418F77EC";
void delay_ms(int);
void serial_init();				
char serial_rec();
void GSM_INIT();
void lcd_string(unsigned char *, unsigned char);
void lcd_data(unsigned char);
void lcd_cmd(unsigned char);
void lcd_ini();
void distribute(int);
intcompare_card(char *);
void motor(int);
void compaire();
ints,r,d,m, cb;
sbitrs = P2^2;
sbitrw = P2^1;
sbiten = P2^0;
#define lcd_data_pin P1
char card_id[11];
unsigned int k=65;
unsigned char current_byte = 0;
void lcd_ini()  //Function to inisialize the LCD
{
	lcd_cmd(0x28);  // for using 8-bit 2 row LCD
	delay_ms(5);
	lcd_cmd(0x0C);
	delay_ms(5);  //Force cursor to blink at line 1 positon 0
	lcd_cmd(0x01);
}
void lcd_cmd(unsigned char comm)  //Function to send command to LCD.
{
	lcd_data_pin=comm;
	en=1;
	rs=0;
	delay_ms(1);
	en=0;
}
           void lcd_data(unsigned char disp)  //Function to send data to LCD.
{
lcd_data_pin=disp;
	en=1;
	rs=1;
	delay_ms(1);
	en=0;
}
void lcd_string(unsigned char *disp, unsigned char loc)  //Function to send string data to LCD.
{
	int x;
	lcd_cmd(loc);
	for(x=0;disp[x]!='0';x++)
	{
	lcd_data(disp[x]);
	}
}
void delay_ms(int count)
{
	inti,j;
	for(i=0;i<=count;i++)
	{
		for(j=0;j<=54;j++)
		{
			if(RI)
			{
				//check_serial();
				break;
				break;
			}
		}
	}
}
void serial_init()
{
	TMOD=0x20;                            //Enable Timer 1
    TH1=0XFD;
    SCON=0x50;
    TR1=1;
    EA=1;
}
void recieve() interrupt 4    // Function to recieve data serialy from RS232 
{
card_id[current_byte]=SBUF;
            RI=0;              // Reset the serial interrupt after recieving the byte
current_byte++;    
}
  void GAIT()
{
			lcd_cmd(0x01);
			lcd_string("  GATE IS OPEN", 0x80);
             m1=1;
			 m2=0;
			delay_ms(5500);
			lcd_cmd(0x01);
			lcd_string("  GATE IS STOP      ", 0x80);
			lcd_string("   kindly go      ", 0xC0);
			 m1=0;
			 m2=0;
			delay_ms(500);   
			  buzzer =1;
			lcd_cmd(0x01);
			lcd_string("   GATE WILL      ", 0x80);
			lcd_string("  CLOSE SOON      ", 0x0C);
		delay_ms(2000);
			buzzer =0;
			lcd_cmd(0x01);
			lcd_string("GATE IS CLOSE", 0x80);
			 m1=0;
			 m2=1;
			delay_ms(500);
			 m1=0;
			 m2=1;
}
intcompare_card(char *num)
{	int i,c1=0,c2=0,c3=0;
	for(i=0;i<12;i++)
	{
	if(num[i]==CARD1[i])
	{
	c1++;
	}
	}
	for(i=0;i<12;i++)
	{
	if(num[i]==CARD2[i])
	{
	c2++;
	}
	}
	if(c1==12)
	{	
		lcd_cmd(0x01);
		lcd_string("CAR NUMBER ONE  ", 0x80);
		lcd_string("IS AUTHORISED   ", 0xC0);
		delay_ms(2000);
			 GAIT();
			}
	else if(c2==12)
	{		
	lcd_cmd(0x01);
		lcd_string("CAR NUMBER TWO  ", 0x80);
		lcd_string("IS AUTHORISED   ", 0xC0);
		delay_ms(2000);	
			GAIT();
	}
	else
	{	
		lcd_cmd(0x01);
		lcd_string("CARD DETAILS", 0x80);
		lcd_string("NOT FOUND", 0xC0);
		delay_ms(3000);
	}
}
int main()
{
	rw=0; //s=0; r=0; d=0; m1=0; m2=1; m=0; cb=0;
	buzzer=0;
	lcd_ini();
	serial_init();
	lcd_string("WELCOME", 0x80);
	delay_ms(2000);
	lcd_cmd(0x01);
	lcd_string("   RFID BASED   ", 0x80);
	lcd_string("  GATE OPENING   ", 0xC0);
	delay_ms(2000);
	IE=0x90;    //Interrupt enable 
	delay_ms(100);
              while(1)
	  {
lcd_string("  KINDLY SWIPE   ", 0x80);
		lcd_string("  YOUR RF CARD   ", 0xC0);
         if(current_byte==12)
		 {  
		current_byte=0;
	compare_card(card_id);
		 }

	  }	
	}
