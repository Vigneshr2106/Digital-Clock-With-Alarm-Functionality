## INTRODUCTION


A digital clock with alarm functionality is a ubiquitous device found in homes, offices, and personal devices like smartphones,It combines the basic function of timekeeping with the added utility of setting alarms to alert users at specified times. 
This dual functionality is essential in modern life for managing schedules, waking up on time, and organizing daily activities.
The most distinguishing feature of a digital clock is its display, which shows the time in numerical digits rather than using hands on a dial. This makes reading the time quick and straightforward.
Digital clocks allow users to set alarms for specific times. These alarms can often be customized with various sound options and volumes, and many modern digital clocks support multiple alarms for different times of the day.


## OVERVIEW
To Create a digital clock using a RISC-V board that displays the current time and allows the user to set alarms. 
OLED (Organic Light Emitting Diodes) is used for dispalying the time.
It should also have an interrupt-driven system to trigger the alarm at the set time.

## COMPONENTS REQUIRED
VSD Squadron Mini Board


O LED Display (to show the time)


Buzzer (for the alarm sound)


Wires and Breadboard (for connections)

## BILL OF MATERIALS(BOM)
O LED Display: Rs.270

Buzzer: Rs.25

Wires and Breadboard: Rs.200

## CIRCUIT CONNECTION DIAGRAM

![Screenshot 2024-06-09 220821](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/3f3ea4b2-b7a0-4178-ad43-fdf4f122de72)






## PIN CONNECTIONS

![Screenshot 2024-06-09 220751](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/8f653f88-bfa0-4bd3-83e9-7c6006136e17)





## CODE

#include "debug.h"

#include "ssd1306.h"

#include "ch32v00x.h"

void GPIO_Config(void)

{
   
   GPIO_InitTypeDef GPIO_InitStructure = {0};
    
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);
    
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);

   // Light pin configuration
   
   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
   
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    
   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    
   GPIO_Init(GPIOD, &GPIO_InitStructure);
   
}

// Define a structure to hold the time

typedef struct 

{

   int hours;
   
   int minutes;
   
   int seconds;
   
} 

Time;

Time currentTime = {0, 0, 0};

Time alarmTime = {6, 30, 0}; 

// Example alarm time at 06:30:00

void Start()

{
    OLED_GotoXY(1, 1);
    
   OLED_Puts("DIGITAL", &Font_11x18, 1);
    
   OLED_GotoXY(1, 30);
    
   OLED_Puts("CLOCK", &Font_11x18, 1);
    
   OLED_UpdateScreen();
    
   Delay_Ms(1000);
   
}


void Buzzer(void)

{
   uint32_t elapsedTime = 0;
    
   while (elapsedTime < 5000)
   
   {
        GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_SET);
        
   Delay_Ms(500);
        
   GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_RESET);
        
   Delay_Ms(500);
        
   elapsedTime += 1000;
        
   }
}


void UpdateClockDisplay(Time time)

{
    char buffer[9];  // Buffer to hold the time string in HH:MM:SS format
    
   snprintf(buffer, sizeof(buffer), "%02d:%02d:%02d", time.hours, time.minutes, time.seconds);
    
   OLED_GotoXY(1, 1);
    
   OLED_Puts(buffer, &Font_11x18, 1);
    
   OLED_UpdateScreen();
    
   Delay_Ms(1000);
   
}

void CheckAlarm(Time time, Time alarm)

{

   if (time.hours == alarm.hours && time.minutes == alarm.minutes && time.seconds == alarm.seconds) 
   
   {
   
   OLED_GotoXY(1, 30);
   
   OLED_Puts("ALARM!", &Font_11x18, 1);
   
   OLED_UpdateScreen();
   
   Buzzer();
    
   }
    
}

void IncrementTime(Time *time)

{
    time->seconds++;
    
   if (time->seconds >= 60) 
   
   {
    
   time->seconds = 0;
        
   time->minutes++;
    
   }
    
  if (time->minutes >= 60) 
  
  {
  
   time->minutes = 0;
   
   time->hours++;
   
   }
    
  if (time->hours >= 24) 
  
  {
    
  time->hours = 0;
  
   }
    
}

int main(void)

{
    SystemCoreClockUpdate();
    
   Delay_Init();
   
   OLED_Init();
   
   GPIO_Config();
   
   Start();

   while (1)
   
   {
    
   UpdateClockDisplay(currentTime);
        
   CheckAlarm(currentTime, alarmTime);
   
   IncrementTime(&currentTime);
   
   Delay_Ms(500);  // Wait for 1 second
    
   }
}


## Demo Video


https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165021886/2b1cbfa0-0de4-4219-aa33-eb12c8ded15d



## Fault Injection Using Arduino

Fault injection in software is a technique used to test the robustness and reliability of software by intentionally introducing errors or faults. 

For a digital clock with alarm functionality implemented on an Arduino using Tinkercad, we can simulate fault injection by randomly altering the time or alarm settings during execution.

By simulating fault injection, we can test how robust our digital clock with alarm functionality is against unexpected errors.


## Code

#include <LiquidCrystal.h>

#include <Wire.h>

// Initialize the LCD library with the numbers of the interface pins

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

struct Time 
{
  int hours;
  
  int minutes;
  
  int seconds;
}
;

Time currentTime = {0, 0, 0};

Time alarmTime = {0, 0, 10};

// Example alarm time

bool alarmTriggered = false;

void updateClockDisplay(Time time);

void checkAlarm(Time time, Time alarm);

void incrementTime(Time &time);

void buzzer();

void faultInjection(Time &time);

void setup() 
{
  lcd.begin(16, 2);  // Set up the LCD's number of columns and rows
  
  updateClockDisplay(currentTime);
  
}

void loop() 
{
  incrementTime(currentTime);
  
  updateClockDisplay(currentTime);
  
  checkAlarm(currentTime, alarmTime);

  // Introduce fault injection at random intervals
  
  if (random(10) == 0) {  // 10% chance of fault injection each loop iteration
  
   faultInjection(currentTime);
  
  }
  
  delay(1000);  // Wait for 1 second

}

void updateClockDisplay(Time time) 

{
  lcd.setCursor(0, 0);
  
  lcd.print("Time: ");
  
  if (time.hours < 10) lcd.print('0');
  
  lcd.print(time.hours);
  
  lcd.print(':');
  
  if (time.minutes < 10) lcd.print('0');
  
  lcd.print(time.minutes);
  
  lcd.print(':');
  
  if (time.seconds < 10) lcd.print('0');
  
  lcd.print(time.seconds);

}

void checkAlarm(Time time, Time alarm) 

{
  if (time.hours == alarm.hours && time.minutes == alarm.minutes && time.seconds == alarm.seconds && !alarmTriggered) 
  
  {
    lcd.setCursor(0, 1);
    
   lcd.print("ALARM!");
   
   buzzer();
   
   alarmTriggered = true;
  
  }
}

void incrementTime(Time &time) 
{
  time.seconds++;
  
  if (time.seconds >= 60) 
  {
    
   time.seconds = 0;
    
   time.minutes++;
  
  }
  if (time.minutes >= 60) 
  
  {
    
   time.minutes = 0;
    
   time.hours++;
  
  }
  
  if (time.hours >= 24) 
  {
    
   time.hours = 0;
  
  }
}

void buzzer() 
{
  for (int i = 0; i < 5; i++) 
  
  {
    
   digitalWrite(8, HIGH);
    
   delay(500);
   
   digitalWrite(8, LOW);
   
   delay(500);
  
  }
}

void faultInjection(Time &time) 
{
 
  // Randomly corrupt the time value
  
  time.seconds = random(60);
  
  time.minutes = random(60);
  
  time.hours = random(24);
  
  lcd.setCursor(0, 1);
  
  lcd.print("FAULT INJECTED");
}

## Video of Fault Injection

https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165021886/1eaffbf8-d685-46e1-a3f6-df438ea8dd1c

## Fault Injection Using OLED Display

#include "debug.h"

#include "ssd1306.h"

#include "ch32v00x.h"

void GPIO_Config(void)
{

   GPIO_InitTypeDef GPIO_InitStructure = {0};
   
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);
   
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);

    
   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
   
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
   
   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
   
   GPIO_Init(GPIOD, &GPIO_InitStructure);
}

// Define a structure to hold the time
typedef struct

{
    int hours;
    
   int minutes
    
   int seconds;
} 

Time;

Time currentTime = {0, 0, 0};
Time alarmTime = {6, 30, 0}; // Example alarm time at 06:30:00

void Start()
{ 
    OLED_GotoXY(1, 1);
    OLED_Puts("DIGITAL", &Font_11x18, 1);
    OLED_GotoXY(1, 30);
    OLED_Puts("CLOCK", &Font_11x18, 1);
    OLED_UpdateScreen();
    Delay_Ms(1000);
}

void Buzzer(void)
{ 
    uint32_t elapsedTime = 0;
    while (elapsedTime < 5000)
    { 
        GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_SET);
        Delay_Ms(500);
        GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_RESET);
        Delay_Ms(500);
        elapsedTime += 1000;
    } 
}

void UpdateClockDisplay(Time time)
{ 
    char buffer[9]; // Buffer to hold the time string in HH:MM:SS format
    snprintf(buffer, sizeof(buffer), "%02d:%02d:%02d", time.hours, time.minutes, time.seconds);
    OLED_GotoXY(1, 1);
    OLED_Puts(buffer, &Font_11x18, 1);
    OLED_UpdateScreen();
    Delay_Ms(1000);
}

void CheckAlarm(Time time, Time alarm)
{
    if (time.hours == alarm.hours && time.minutes == alarm.minutes && time.seconds == alarm.seconds)
    {
        OLED_GotoXY(1, 30);
        OLED_Puts("ALARM!", &Font_11x18, 1);
        OLED_UpdateScreen();
        Buzzer();
    }
}

void IncrementTime(Time *time)
{ 
    time->seconds++;
    if (time->seconds >= 60)
    {
        time->seconds = 0;
        // Injected fault: The following line is commented out, so minutes never increment.
        // time->minutes++;
    }
    if (time->minutes >= 60)
    {
        time->minutes = 0;
        time->hours++;
    }
    if (time->hours >= 24)
    {
        time->hours = 0;
    }
}

int main(void)
{ 
    SystemCoreClockUpdate();
    Delay_Init();
    OLED_Init();
    GPIO_Config();
    Start();

 while (1)
    {
   UpdateClockDisplay(currentTime);
   
  CheckAlarm(currentTime, alarmTime);
  
 IncrementTime(&currentTime);
 
Delay_Ms(500); // Wait for 1 second
    }
}





