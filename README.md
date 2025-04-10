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
