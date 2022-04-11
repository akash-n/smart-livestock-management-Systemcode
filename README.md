# smart-livestock-management-Systemcode
SlMI- Ardiuno,ESP8266 and Blynk code
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <SoftwareSerial.h>
#include <SimpleTimer.h>
WidgetLED led(V3);
// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "tg9pdWBomXlWJ2da0AsJHVBtJN1ks3AX";     // Your WiFi credentials.
                                                      // Set password to "" for open networks.
char ssid[] = "iotdata30";
char pass[] = "12345678";
SimpleTimer timer;
int  hbflag=0,tflag=0,hflag=0,mflag=0;
String myString;                      // complete message from arduino, which consists of snesors data
char rdata;                           // received characters
int firstVal, secondVal,thirdVal,fourthVal;     // sensors 

// This function sends Arduino's up time every second to Virtual Pin (1).
// In the app, Widget's reading frequency should be set to PUSH. This means
// that you define how often to send data to Blynk App.
void myTimerEvent()
{
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  Blynk.virtualWrite(V5, millis() / 1000);  
}


void setup()
{
  // Debug console
  Serial.begin(9600);
  Blynk.begin("tg9pdWBomXlWJ2da0AsJHVBtJN1ks3AX ","iotdata30", "12345678");
  timer.setInterval(1000L,sensorvalue1); 
  timer.setInterval(1000L,sensorvalue2);
  timer.setInterval(1000L,sensorvalue3); 
  timer.setInterval(1000L,sensorvalue4);
}
void loop()
{
   if (Serial.available() == 0 ) 
   {
  Blynk.run();
  timer.run(); // Initiates BlynkTimer
   }
   
  if (Serial.available() > 0 ) 
  {
    rdata = Serial.read(); 
    myString = myString+ rdata; 
    Serial.print(rdata);
    
    if( rdata == '\n')
    {
      Serial.print(myString);
    String l = getValue(myString, ',', 0); // humidity
    String m = getValue(myString, ',', 1); // temperature
    String n = getValue(myString, ',', 2); // mems
    String k = getValue(myString, ',', 3); // heart
    firstVal = l.toInt();   // humidity
    secondVal = m.toInt();  // temperature
    thirdVal = n.toInt();   // mems 
    fourthVal = k.toInt();  // heart
    myString = "";
    // end new code
    
    }
  }
}

void sensorvalue1()
{
int sdata = firstVal; // humidity value
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  if (sdata < 80 && hflag==0)
  {
    hflag=1;
    Blynk.notify("***Humidity normal***"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "***Humidity normal***");
  }  
  if (sdata >= 80 && hflag==1)
  {
    hflag=0;
    Blynk.notify("Humidity exceeded!!!"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "Humidity exceeded!!!");
  }
  Blynk.virtualWrite(V1, sdata);
 
}
void sensorvalue2()
{
int sdata = secondVal; // temperature value
 if (sdata < 45 && tflag==0)
  {
    tflag=1;
    Blynk.notify("***temperature normal***"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "***temperature normal***");
  }  
  if (sdata > 45 && tflag==1)
  {
    tflag=0;
    Blynk.notify("temperature exceeded!!!"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "temperature exceeded!!!");
  }
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  Blynk.virtualWrite(V2, sdata);
}

void sensorvalue3()
{
int sdata = thirdVal*11; // mems value
 if (sdata < 300 && mflag==0)
  {
    mflag=1;
    Blynk.notify("****cattle_stand****"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "****cattle_stand****");
  }  
  if (sdata >400 && mflag==1)
  {
    mflag=0;
    Blynk.notify("Cattle Fall_!!!"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "cattle_Fall_!!!");
  }
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  Blynk.virtualWrite(V4, sdata);
}

void sensorvalue4()
{
int sdata = fourthVal*2; // HEART value
 if (sdata < 90 && hbflag==0)
  {
    hbflag=1;
    Blynk.notify("****Heart_Rate_Normal****"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "****Heart_Rate_Normal****");
    led.off();
  }  
  if (sdata >90 && hbflag==1)
  {
    hbflag=0;
    Blynk.notify("Heart_Rate_High!!!"); 
    Blynk.email("nanotronicsnss@gmail.com","ESP8266 Alert", "Heart_Rate_High!!!");
    led.on();
  }
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  Blynk.virtualWrite(V3, sdata);
}

String getValue(String data, char separator, int index)
{
    int found = 0;
    int strIndex[] = { 0, -1 };
    int maxIndex = data.length() - 1;
 
    for (int i = 0; i <= maxIndex && found <= index; i++) {
        if (data.charAt(i) == separator || i == maxIndex) {
            found++;
            strIndex[0] = strIndex[1] + 1;
            strIndex[1] = (i == maxIndex) ? i+1 : i;
            }
    }
    return found > index ? data.substring(strIndex[0], strIndex[1]) : "";
}



//ARDIUNO CODING
#include <LiquidCrystal.h>
#include <TimerOne.h>
#include <SoftwareSerial.h>
SoftwareSerial nodemcu(4,5);
#include "DHT.h"
LiquidCrystal lcd(13, 12, 11, 10, 9, 8);
#define DHTPIN 2 
#define DHTTYPE DHT11 
const byte interruptPin = 3;
int temppin = A0;
int humpin = 2;
int yaxispin = A4;
int Tled =A3;
int Hled =A2;int Mled =6;int HBled =7;int buzzer =A5;
int T,H,X,Y,Z;
int t1,t2,t3,h1,h2,h3,x1,x2,x3,x4,y1,y2,y3,y4,z1,z2,z3,z4,tim1,tim2,hb1,hb2,hb3,count,count1;
float h,t,f; 
int H1,H2,H3,H4;
int Count1,Count2,Heart,pulse,count3;
int humidity;
DHT dht(DHTPIN, DHTTYPE); 

int sdata1 = 0; // humidity 
int sdata2 = 0; // temperature
int sdata3 = 0; // MEMS
int sdata4 = 0; // Heart
String cdata;

int setflag;


void setup() {
  // put your setup code here, to run once:
  lcd.begin(16,2);
  Serial.begin(9600);
  lcd.setCursor(0,0);
  lcd.print("   --CATTLE--   ");
  lcd.setCursor(0,1);
  lcd.print(" --MONITORING-- ");
  delay(3000);
  pinMode(temppin,INPUT);
  pinMode(humpin,INPUT);
  pinMode(yaxispin,INPUT);
  pinMode(Tled,OUTPUT); pinMode(Hled,OUTPUT); pinMode(HBled,OUTPUT); pinMode(Mled,OUTPUT); pinMode(buzzer,OUTPUT);
  Timer1.initialize(100000); // set a timer for 100ms
  Timer1.attachInterrupt(timerIsr);
  attachInterrupt(digitalPinToInterrupt(interruptPin), blink, FALLING);          
  lcd.clear();
  dht.begin();
  nodemcu.begin(9600);
 }

void loop() {
  count++;count1++;
  if(count>=100)
  {
  count=0;
  T=analogRead(temppin);
  lcd.setCursor(0,0);
  lcd.print("T:");
  T=T/2;
  t1=T/100;
  t2=(T%100)/10;
  t3=T%10;
  lcd.setCursor(2,0);
  //lcd.print(t1);
  lcd.print(t2);
  lcd.print(t3);
  lcd.print((char)223);
  lcd.print("C");
  delay(10);
  }

 if(count1>=50)
 {
  count1=0;
//  X=analogRead(xaxispin);
//  // X=X*2.25;
//  if(X>1023) X=1023;
//  lcd.setCursor(0,1);
//  lcd.print("X");
//  x1=X/1000;
//  x2=(X%1000)/100;
//  x3=(X%100)/10;
//  x4=X%10;
//  lcd.setCursor(1,1);
//  // lcd.print(x1);
//  lcd.print(x2);
//  lcd.print(x3);
//  lcd.print(x4);
//  delay(10);
  Y=analogRead(yaxispin);
 // Y=Y*2.25;
  if(Y>1023) Y=1023;
  lcd.setCursor(0,1);
  lcd.print("Y:");
  y1=Y/1000;
  y2=(Y%1000)/100;
  y3=(Y%100)/10;
  y4=Y%10;
  lcd.setCursor(2,1);
  lcd.print(y2);
  lcd.print(y3);
  lcd.print(y4);
  delay(10);
if(Y<300 )
{
  lcd.setCursor(6,1);
  lcd.print("P:R ");
}
else if(Y>300 && Y<400)
{
  lcd.setCursor(6,1);
  lcd.print("P:S ");
}
else
{
  lcd.setCursor(5,1);
  lcd.print("P:F ");
}
  }
  ////////////////////////////////for heart beat sensor
  lcd.setCursor(11,1);
  lcd.print("H:");
  lcd.setCursor(13,1);
  h1=Heart/100;
  h2=(Heart%100)/10;
  h3=Heart%10;
  lcd.print(h1);
  lcd.print(h2);
  lcd.print(h3);
  if(Heart>90)// cow heart rate 48 to 84
  {
    lcd.setCursor(13,0);
    lcd.print("AB");
    digitalWrite(HBled,HIGH);
    digitalWrite(buzzer,HIGH);
  }
  else
  {
    lcd.setCursor(13,0);
    lcd.print("NR");
  }
  
  /////////////////////////////////////////////dht read section
  h = dht.readHumidity();
  float h = dht.readHumidity();
  humidity=(int)h;
  // Read temperature as Celsius (the default)
  t = dht.readTemperature();
  // Read temperature as Fahrenheit (isFahrenheit = true)
  f = dht.readTemperature(true);
  H1=humidity/100;
  H2=(humidity%100)/10;
  H3=humidity%10;
  // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t) || isnan(f)) {
  Serial.println(F("Failed to read from DHT sensor!"));
  return;
  }
  lcd.setCursor(7,0);
  lcd.print("H:");
  lcd.print(H2);
  lcd.print(H3);
  lcd.print("%");
  if(humidity>=80)
  {
    digitalWrite(Hled,HIGH);
    digitalWrite(buzzer,HIGH);
  }
  if(T>45)
  {
    digitalWrite(Tled,HIGH);
    digitalWrite(buzzer,HIGH);
  }
  if(Y>=400)
  {
    digitalWrite(Mled,HIGH);
    digitalWrite(buzzer,HIGH);
  }
  if(Y<400 && T<45 && Heart<90 && humidity<80)
  {
    if(Y<400) {digitalWrite(Mled,LOW);}
    if(T<45)  {digitalWrite(Tled,LOW);}
    if(Heart<90) {digitalWrite(HBled,LOW);}
    if(humidity<80) {digitalWrite(Hled,LOW);}
    digitalWrite(buzzer,LOW);  
  }
  else
  {
    if(Y<400) {digitalWrite(Mled,LOW);}
    if(T<45)  {digitalWrite(Tled,LOW);}
    if(Heart<90) {digitalWrite(HBled,LOW);}
    if(humidity<80) {digitalWrite(Hled,LOW);}      
  }

   if(setflag==1)
   { 
   setflag=0;
   sdata1 = humidity;   //HUMIDITY
   sdata2 =  T;         //TEMPERATURE
   sdata3 =  Y/11;         //MEMS
   sdata4 =  Heart/2;         //HEART
   cdata = cdata + sdata1+","+sdata2+","+sdata3+","+sdata4;        // comma will be used a delimeter
   Serial.println(cdata); 
   nodemcu.println(cdata);
   cdata = ""; 
   count3=0;
   }

/////////////////////////////////////////////////////////
}


void blink() 
{
 pulse++;
}


void timerIsr()
{
Count2++;count3++;
if(Count2>=100) { Heart=pulse*6;Count2=0;pulse=0;}  
if(count3>=10){count3=0;setflag=1;}
}
