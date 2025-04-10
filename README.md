# Time-Driven-Access-Control-System

__Project Title:__ The Time-Driven Access Control System is an embedded security solution that regulates access based on predefined time schedules. 

*OBJECTIVE:* 
1. Display RTC information (date, time and day) on an LCD. 
2. Allow users to modify RTC information via a 4x4 matrix keypad. 
3. Allow users to modify the system access time via 4x4 matrix keypad.  
4. Grant the access based on the correct password entry with in the scheduled time.


__Block Diagram:__

![image](https://github.com/user-attachments/assets/0cef70ed-b5a2-4010-b1f3-04c8f769c686)


__Hardware Requirements:__ 
 - LPC2148 
 - 16x2 LCD 
 - 4x4 matrix keypad 
 - Buzzer/LED 
 - Switches 
 - USB-UART converter / DB-9 cable 


__Software Requirements:__
 - Keil uVision 
 - Flash Magic
 - Proteus


__Hardare Connection:__\
Keypad Connection:

![image](https://github.com/user-attachments/assets/50041eea-8f15-4202-a809-4415c3fb5960)

Lcd Connections:

![image](https://github.com/user-attachments/assets/37cffa9f-efba-4a85-9b15-7a7fe9b33177)

Switches and Led Connection:
1. switch 1----> LPC2148 P0.0
2. switch 2----> LPC2148 P0.1
3. Led --------> LPC2148 P0.5


__Software Flow:__ 
1. Initialize system: RTC, LCD, Keypad, and Buzzer/Led. 
2. Display current time, date and day on LCD. 
3. Allow user to enter the password based on switch1 press.   
4. After switch1 is pressed, user has to enter the password from the 4x4 matrix keypad. If the password is matched with the current/updated password, then check the scheduled time. If correct/updated password is entered with in the scheduled time, then give the access for the security system. 
5. If user want to edit the RTC information and schedule time, then need to generate the interrupt by pressing switch2. Based on the interrupt request below mentioned menu will display. 
      1. Edit RTC Info 
      2. Edit Schedule Time 
      3. Exit 
6. Editing process need to follow as per the user requirement. 
7. After editing, again application program will start running from step2. 


__Software Simulation:__

![image](https://github.com/user-attachments/assets/40461195-135f-4916-9db3-96f510c1e67d)


__Hardware Simulation:__

![WhatsApp Image 2025-04-10 at 09 35 46_7066e8b0](https://github.com/user-attachments/assets/43d3aa08-c120-4ab3-95a2-1d916b20eb55)

![WhatsApp Image 2025-04-10 at 09 35 48_9cfb33d9](https://github.com/user-attachments/assets/648c2be0-7976-4a0c-aad6-fa770b882c02)

![WhatsApp Image 2025-04-10 at 09 35 50_f16b0127](https://github.com/user-attachments/assets/4de7b7b7-eb6e-4a7b-a6f2-3f704ffc4c84)

![WhatsApp Image 2025-04-10 at 09 35 57_e37b8d55](https://github.com/user-attachments/assets/1110f5a7-f0d4-48d5-abff-b974f65e7eaf)

![WhatsApp Image 2025-04-10 at 09 35 47_8800ac2c](https://github.com/user-attachments/assets/5f6a3ea9-d46c-4997-825f-c02bee04c4fd)

![WhatsApp Image 2025-04-10 at 09 35 47_1c9f21fa](https://github.com/user-attachments/assets/3b1233ed-4427-41c1-a5b2-3346102539f7)

![WhatsApp Image 2025-04-10 at 09 35 51_4bf3ead6](https://github.com/user-attachments/assets/052a72bf-2e5b-435d-9e51-328e9ac3f4b0)




__Project Code:__
1. main.c
```
#include <lpc21xx.h>
#include "types.h"
#include "delay.h"
#include "kpm.h"
#include "lcd.h"		   
#include "rtc.h"
#include "main.h"
#include "rtc_defines.h"
#include "lcd_defines.h"
#include "main_defines.h"
//default scheduled time
s32 sh=8,eh=18;
void CfgInterrupt()
{
   //Cfg p0.1 as eint0 via pcb
	PINSEL0=((PINSEL0&~(3<<2)) | (3<<2));
	//Cfg VIC block
	//select eint0 as fiq
	VICIntSelect=1<<EINT0_VIC_CHNO;
	//VICIntSelect=0;
	//enable eint0
  	VICIntEnable=(1<<EINT0_VIC_CHNO);	
	//Cfg EXT block
	//enable eint0
	//EXTINT=0;
	//selet mode
	EXTMODE=(1<<0);
	//select falling edge
	//EXTPOLAR=0;
}

int check_schedule_time()
{
	if(HOUR>=sh && HOUR<=eh)
	{
		return 1;
	}
	return 0;
	
}

void FIQ_Interrupt(void) __irq
{
	u32 key,flag=0;
	//some activity
	CmdLCD(CLEAR_LCD);
	StrLCD("1:E.RTC  3:EXIT");
	CmdLCD(GOTO_LINE2_POS0);
	StrLCD("2:E.Schedul Time");
	
	key=KeyScan();
	switch(key)
		{
			case '1':EditRTC();
				      break;
			case '2':EScheduleTime(&sh,&eh);
				      break;
			case '3':flag=1;
			         break;	      
		}
		if(flag==1)
		{
			CmdLCD(CLEAR_LCD);
			EXTINT=1<<0;
			return ;
		}
}


void EditRTC()
{
	u32 key,flag=0;
	while(1)
	{
	CmdLCD(CLEAR_LCD);
	StrLCD("1.H 2.M 3.S 4.Da");
	CmdLCD(GOTO_LINE2_POS0);
    StrLCD("5.d 6.M 7.Y 8.E");
	delay_ms(1000);
	
	    key=KeyScan();
	
	 switch(key)
		{
			case '1':SetRTCHour();
				      break;
		 	case '2':SetRTCMin();
				      break;
			case '3':SetRTCSec();
			         break;
			case '4':SetRTCDay();
				      break;
			case '5':SetRTCDa();
				      break;
			case '6':SetRTCMonth();
			          break;
			case '7':SetRTCYear();
				      break;
			case '8':flag=1;
			        break;			      
		}
		if(flag)
		{
			CmdLCD(CLEAR_LCD);
			break;
		}
	}
}

void EScheduleTime(s32 *SH,s32 *EH)
{
	s32 key1,key2;
	CmdLCD(CLEAR_LCD);
	StrLCD("Start Time:");
	while(ColScan()==0);
	key1=KPM_Read_Data();
 
	if(key1>=0 && key1<=23)
	{
	   *SH=key1;
	}
	else
	{
		StrLCD("Invalid");
	}
	delay_ms(1000);
	CmdLCD(CLEAR_LCD);
	StrLCD("End Time:");
  	
	while(ColScan()==0);
	key2=KPM_Read_Data();
	delay_ms(100);
	if((key2>*SH)&&(key2>=0 && key2<=23))
	{
	   *EH=key2;
	}
	else
	{
		StrLCD("Invalid");
	}
	delay_ms(1000);
}	

```


2. main_test.c
```
#include <lpc21xx.h>
#include "types.h"
#include "delay.h"
#include "kpm.h"
#include "lcd.h"
#include "rtc.h"
#include "main.h"
#include "rtc_defines.h"
#include "lcd_defines.h"
#include "main_defines.h"
s32 hour,min,sec,date,month,year,day;
u32 pass=1234;

int main()
{
	u32 password;
    IODIR0 |=1<<LED_AL;
	InitLCD();
	InitKPM();
	InitRTC();
	CfgInterrupt();
 	
	while(1)
	{
	    GetRTCTime(&hour,&min,&sec);
	    DisplayRTCTime(hour,min,sec);
		GetRTCDate(&date,&month,&year);
		DisplayRTCDate(date,month,year);
			
		GetRTCDay(&day);
        DisplayRTCDay(day);
		  
	   if(((IOPIN0>>SW1_AL)&1)==0)
		{
			  while(((IOPIN0>>SW1_AL)&1)==0);
			  CmdLCD(CLEAR_LCD);
		      StrLCD("Enter Password:");	 
			  CmdLCD(GOTO_LINE2_POS0);
		     
			  password=KPM_Read_Num();
			  if(password==pass)
			  {  
				 if(check_schedule_time())
				 {
				    CmdLCD(CLEAR_LCD);
				    StrLCD("login sucessful");
			        IOSET0=1<<LED_AL;
				   //BuildCGRAM(lock,8);
					delay_ms(5000);
					IOCLR0=1<<LED_AL;
				 }
				 else
				 {
					  CmdLCD(CLEAR_LCD);
					  StrLCD("TIME OVER");
					  delay_ms(1000);
				 }
			}
			else
			{
				  CmdLCD(CLEAR_LCD);
				  StrLCD("failed");
				  delay_ms(1000);
			 }
			 CmdLCD(CLEAR_LCD);	 
		}		 	
	}
}
```


# __Project Improvement:__
1. Show Scheduled Time to another admin also LCD
2. Change LCD 16*2 to 20*4 (more Display Area for Better UI and Improved Professional Presentation)

__Software Simulation:__

![image](https://github.com/user-attachments/assets/abf0d0e8-0d92-4e44-b51f-5b9db93a11b4)

__Hardware Simulation:__

![WhatsApp Image 2025-04-10 at 09 57 56_6ea9805b](https://github.com/user-attachments/assets/fe652286-c0c0-4beb-bc38-34d668313ca9)

![WhatsApp Image 2025-04-10 at 09 57 56_803a73f0](https://github.com/user-attachments/assets/846c7b7a-3569-4c47-bfe6-4ad881b643c2)

![WhatsApp Image 2025-04-10 at 09 57 55_27d0c8e3](https://github.com/user-attachments/assets/299c7ddc-a93e-4c4c-92f0-e3070c9a9eb9)

![WhatsApp Image 2025-04-10 at 09 57 55_fbb93ce2](https://github.com/user-attachments/assets/9c15b03b-9900-4d96-a3b3-05672404ec95)


__Project Code:__
1. main_1.c
```
#include <lpc21xx.h>
#include "types.h"
#include "delay.h"
#include "kpm.h"
#include "lcd.h"		   
#include "rtc.h"
#include "main.h"
#include "rtc_defines.h"
#include "lcd_defines.h"
#include "main_defines.h"
#include <stdio.h>
//default scheduled time
s32 sh=8,eh=18;
void CfgInterrupt()
{
   //Cfg p0.1 as eint0 via pcb
	PINSEL0=((PINSEL0&~(3<<2)) | (3<<2));
	//Cfg VIC block
	//select eint0 as fiq
	VICIntSelect=1<<EINT0_VIC_CHNO;
	//VICIntSelect=0;
	//enable eint0
  	VICIntEnable=(1<<EINT0_VIC_CHNO);	
	//Cfg EXT block
	//enable eint0
	//EXTINT=0;
	//selet mode
	EXTMODE=(1<<0);
	//select falling edge
	//EXTPOLAR=0;
}

int check_schedule_time()
{
	if(HOUR>=sh && HOUR<=eh)
	{
		return 1;
	}
	return 0;
	
}

void FIQ_Interrupt(void) __irq
{
	u32 key,flag=0;
	//some activity
	CmdLCD(CLEAR_LCD);
	CmdLCD(GOTO_LINE1_POS0);
	StrLCD("1:Edit RTC");
	
	CmdLCD(GOTO_LINE2_POS0);
	StrLCD("2:Edit Schedule");
	
	CmdLCD(GOTO_LINE3_POS0);
	StrLCD("3:Exit");
	
	CmdLCD(GOTO_LINE4_POS0);
	StrLCD("4:Check Schedule");
	
	key=KeyScan();
	switch(key)
		{
			case '1':EditRTC();
				      break;
			case '2':EScheduleTime(&sh,&eh);
				      break;
			case '3':flag=1;
			         break;	  
			case '4':DisplayScheduleTime(sh, eh);
				     break;    
		}
		if(flag==1)
		{
			CmdLCD(CLEAR_LCD);
			EXTINT=1<<0;
			VICVectAddr=0;
			return ;
		}
}


void EditRTC()
{
	u32 key,flag=0;
	while(1)
	{
	CmdLCD(CLEAR_LCD);
	StrLCD("1.HOUR    2.MIN");
	CmdLCD(GOTO_LINE2_POS0);
	StrLCD("3.SEC     4.DATE");
   	CmdLCD(GOTO_LINE3_POS0);
    StrLCD("5.DAY     6.MONTH"); 
	CmdLCD(GOTO_LINE4_POS0);
	StrLCD("7.YEAR    8.EXIT");
	delay_ms(1000);
	
	    key=KeyScan();
	
	 switch(key)
		{
			case '1':SetRTCHour();
				      break;
		 	case '2':SetRTCMin();
				      break;
			case '3':SetRTCSec();
			         break;
			case '4':SetRTCDay();
				      break;
			case '5':SetRTCDa();
				      break;
			case '6':SetRTCMonth();
			          break;
			case '7':SetRTCYear();
				      break;
			case '8':flag=1;
			        break;			      
		}
		if(flag)
		{
			CmdLCD(CLEAR_LCD);
			break;
		}
	}
}

void EScheduleTime(s32 *SH,s32 *EH)
{
	s32 key1,key2;
	CmdLCD(CLEAR_LCD);
	StrLCD("Start Time:");
	while(ColScan()==0);
	key1=KPM_Read_Data();
 
	if(key1>=0 && key1<=23)
	{
	   *SH=key1;
	}
	else
	{
		CmdLCD(GOTO_LINE2_POS0);
		StrLCD("Invalid");
	}
	delay_ms(1000);
	CmdLCD(CLEAR_LCD);
	StrLCD("End Time:");
  	
	while(ColScan()==0);
	key2=KPM_Read_Data();
	delay_ms(100);
	if((key2>*SH)&&(key2>=0 && key2<=23))
	{
	   *EH=key2;
	}
	else
	{  	
		CmdLCD(GOTO_LINE2_POS0);
		StrLCD("Invalid");
	}
	delay_ms(1000);
}	
void DisplayScheduleTime(s32 sh, s32 eh)
{
    char buff[20];
    CmdLCD(CLEAR_LCD);
    sprintf(buff, "Start: %02d", sh);
    CmdLCD(GOTO_LINE1_POS0);
    StrLCD(buff);

    sprintf(buff, "End:   %02d", eh);
    CmdLCD(GOTO_LINE2_POS0);
    StrLCD(buff);

	delay_ms(2000);
}

```


2. main_test_1.c
```
#include <lpc21xx.h>
#include "types.h"
#include "delay.h"
#include "kpm.h"
#include "lcd.h"
#include "rtc.h"
#include "main.h"
#include "rtc_defines.h"
#include "lcd_defines.h"
#include "main_defines.h"
s32 hour,min,sec,date,month,year,day;
u32 pass=1234;

int main()
{
	u32 password;
    IODIR0 |=1<<LED_AL;
	InitLCD();
	InitKPM();
	InitRTC();
	CfgInterrupt();
 	
	while(1)
	{
	    GetRTCTime(&hour,&min,&sec);
	    DisplayRTCTime(hour,min,sec);
		GetRTCDate(&date,&month,&year);
		DisplayRTCDate(date,month,year);
			
		GetRTCDay(&day);
        DisplayRTCDay(day);
		  
	   if(((IOPIN0>>SW1_AL)&1)==0)
		{
			  while(((IOPIN0>>SW1_AL)&1)==0);
			  CmdLCD(CLEAR_LCD);
		      StrLCD("Enter Password:");	 
			  CmdLCD(GOTO_LINE2_POS0);
		     
			  password=KPM_Read_Num();
			  if(password==pass)
			  {  
				 if(check_schedule_time())
				 {
				    CmdLCD(CLEAR_LCD);
				    StrLCD("LOGIN SUCCESSFUL");
			        IOSET0=1<<LED_AL;
				   //BuildCGRAM(lock,8);
					delay_ms(3000);
					IOCLR0=1<<LED_AL;
				 }
				 else
				 {
					  CmdLCD(CLEAR_LCD);
					  StrLCD("TIME OVER");
					  delay_ms(1000);
				 }		
			}
			else
			{
				  CmdLCD(CLEAR_LCD);
				  StrLCD("FAILED");
				  delay_ms(1000);
			 }
			 CmdLCD(CLEAR_LCD);	 
		}		 	
	}
}
```

____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

