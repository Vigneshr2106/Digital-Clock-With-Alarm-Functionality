## INTRODUCTION


A digital clock with alarm functionality is a ubiquitous device found in homes, offices, and personal devices like smartphones,It combines the basic function of timekeeping with the added utility of setting alarms to alert users at specified times. 
This dual functionality is essential in modern life for managing schedules, waking up on time, and organizing daily activities.
The most distinguishing feature of a digital clock is its display, which shows the time in numerical digits rather than using hands on a dial. This makes reading the time quick and straightforward.
Digital clocks allow users to set alarms for specific times. These alarms can often be customized with various sound options and volumes, and many modern digital clocks support multiple alarms for different times of the day.


## OVERVIEW
To Create a digital clock using a RISC-V board that displays the current time and allows the user to set alarms. 
The clock should read the time from a real-time clock (RTC) module and display it on an LCD. 
It should also have an interrupt-driven system to trigger the alarm at the set time.

## COMPONENTS REQUIRED
VSD Squadron Mini Board

LCD (to show the time)

DS3231 RTC Module (for accurate time-keeping)

Buzzer (for the alarm sound)

Push Buttons (to set the time and alarm)


Wires and Breadboard (for connections)

## BILL OF MATERIALS(BOM)
LCD Display: Rs.200

Buttons: Rs.5 to 20

Real-Time Clock (RTC) Module: Rs.130

Buzzer: Rs.25

## CIRCUIT CONNECTION DIAGRAM


![Screenshot 2024-06-03 232753](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/b5050735-9f3d-4fb6-b048-f5f1cec12666)




## PIN CONNECTIONS

![Screenshot 2024-06-03 234106](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/6190bb20-322d-431a-b248-fc6108c0e7f5)



## CODE

#define RTC_SDA PC1
#define RTC_SCL PC2
#define BUZZER PA1
#define BUTTON PA2


int hours = 0, minutes = 0, seconds = 0;
int alarm_hours = 6, alarm_minutes = 30;
bool alarm_active = true;


int bcdToDec(uint8_t val) {
    return (val / 16 * 10) + (val % 16);
}

void rtcStart() {
    pinMode(RTC_SDA, OUTPUT);
    digitalWrite(RTC_SDA, LOW);
    digitalWrite(RTC_SCL, LOW);
}

void rtcStop() {
    pinMode(RTC_SDA, OUTPUT);
    digitalWrite(RTC_SDA, LOW);
    digitalWrite(RTC_SCL, HIGH);
    digitalWrite(RTC_SDA, HIGH);
}

void rtcWriteBit(bool bit) {
    digitalWrite(RTC_SDA, bit);
    digitalWrite(RTC_SCL, HIGH);
    delayMicroseconds(1);
    digitalWrite(RTC_SCL, LOW);
}

bool rtcReadBit() {
    pinMode(RTC_SDA, INPUT);
    digitalWrite(RTC_SCL, HIGH);
    delayMicroseconds(1);
    bool bit = digitalRead(RTC_SDA);
    digitalWrite(RTC_SCL, LOW);
    return bit;
}

void rtcWriteByte(uint8_t byte) {
    for (int i = 0; i < 8; i++) rtcWriteBit(byte & (0x80 >> i));
    pinMode(RTC_SDA, INPUT);
    digitalWrite(RTC_SCL, HIGH);
    delayMicroseconds(1);
    digitalWrite(RTC_SCL, LOW);
}

uint8_t rtcReadByte() {
    uint8_t byte = 0;
    for (int i = 0; i < 8; i++) byte |= (rtcReadBit() << (7 - i));
    return byte;
}

void rtcWrite(uint8_t address, uint8_t data) {
    rtcStart();
    rtcWriteByte(0x68); // RTC I2C address + write bit
    rtcWriteByte(address);
    rtcWriteByte(data);
    rtcStop();
}

uint8_t rtcRead(uint8_t address) {
    rtcStart();
    rtcWriteByte(0x68); // RTC I2C address + write bit
    rtcWriteByte(address);
    rtcStart();
    rtcWriteByte(0x69); // RTC I2C address + read bit
    uint8_t data = rtcReadByte();
    rtcStop();
    return data;
}

void rtcGetTime() {
    uint8_t rawSeconds = rtcRead(0x00);
    uint8_t rawMinutes = rtcRead(0x01);
    uint8_t rawHours = rtcRead(0x02);
    Serial.print("Raw RTC Values - Hours: ");
    Serial.print(rawHours);
    Serial.print(", Minutes: ");
    Serial.print(rawMinutes);
    Serial.print(", Seconds: ");
    Serial.println(rawSeconds);
    seconds = bcdToDec(rawSeconds);
    minutes = bcdToDec(rawMinutes);
    hours = bcdToDec(rawHours);
}

void setup() {
    Serial.begin(9600);
    pinMode(BUZZER, OUTPUT);
    pinMode(BUTTON, INPUT);
Serial.println("Initializing RTC...");
// Check if RTC is responding
    uint8_t test = rtcRead(0x00);
    if (test == 255) {
        Serial.println("Error: RTC not found or not responding.");
    } else {
        Serial.println("RTC initialized successfully.");
    }
    rtcGetTime();
    Serial.println("Time:");
    Serial.print("Alarm: ");
    if (alarm_hours < 10) Serial.print("0");
    Serial.print(alarm_hours);
    Serial.print(":");
    if (alarm_minutes < 10) Serial.print("0");
    Serial.println(alarm_minutes);
}

void loop() {
    rtcGetTime();
    Serial.print("Current Time: ");
    if (hours < 10) Serial.print("0");
    Serial.print(hours);
    Serial.print(":");
    if (minutes < 10) Serial.print("0");
    Serial.print(minutes);
    Serial.print(":");
    if (seconds < 10) Serial.print("0");
    Serial.println(seconds);
if (hours == alarm_hours && minutes == alarm_minutes && seconds == 0 && alarm_active) {
        for (int i = 0; i < 255; i++) {
            analogWrite(BUZZER, i);
            delay(10);
        }
        for (int i = 255; i > 0; i--) {
            analogWrite(BUZZER, i);
            delay(10);
        }
    } else {
        analogWrite(BUZZER, 0);
    }
    if (digitalRead(BUTTON) == HIGH) {
        alarm_active = false;
        analogWrite(BUZZER, 0);
    }
    delay(1000);
}



## Demo Video



