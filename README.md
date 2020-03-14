
First we can connect ESP8266 with the Arduino Uno. The ESP8266 runs on 3.3V, it may damage if you connect it directly to 5V from Arduino. 

Connect the VCC and CH_PD of the ESP8266 to the 3.3V output pin of Arduino. CH_PD is Chip Power Down pin, which is active low. So we will give 3.3V to it, which will enable the chip. Then connect the TXD pin of the ESP8266 with the digital pin 2 of the Arduino. Then make a voltage divider to make 3.3V for the RXD of the ESP8266 which is connected to the pin 3 of Arduino. Here we are using software UART through digital pins 2 & 3 of Arduino. Lastly, connect the ground of the ESP8266 with the ground of the Arduino.

Now we can connect relays to Arduino. Connect three relays to pins 11, 12 and 13 of the Arduino. Also connect 5V and ground from the Arduino to power the relay. Note that here I am using relay modules which having built in transistor driver. We can connect AC devices to the output terminals of those relays. First connect one wire (Phase) of the AC source with the common terminal (COM) of all relays and the second wire (Neutral) of AC source to one terminal of AC devices. Then connect the other terminal of AC devices to the NO (Normally Open) terminal of relays.

You may change the default SSID of ESP8266 WiFi module by using AT+CWSAP command.

On pasting the api key of thingspeak cloud on COM of arduino.cc. We connect through the thingspeak cloud and virtually Control and monitor our smart fan.











# Iot-smart-fan
#include <SoftwareSerial.h>  

#define DEBUG true

SoftwareSerial esp8266(2,3); 

void setup() 

{

  Serial.begin(9600);  

  esp8266.begin(9600); 

  pinMode(11,OUTPUT);  

  digitalWrite(11,LOW);

  pinMode(12,OUTPUT);  

  digitalWrite(12,LOW); 

  pinMode(13,OUTPUT);  

  digitalWrite(13,LOW); 

  sendData("AT+RST\r\n",2000,DEBUG);            

  sendData("AT+CWMODE=2\r\n",1000,DEBUG);       

  sendData("AT+CIFSR\r\n",1000,DEBUG);          

  sendData("AT+CIPMUX=1\r\n",1000,DEBUG);      

  sendData("AT+CIPSERVER=1,80\r\n",1000,DEBUG); 

}

void loop() 

{

  if(esp8266.available()) 

  { 

    if(esp8266.find("+IPD,"))

    { 

      delay(1000);        

      int connectionId = esp8266.read()-48;   

      esp8266.find("pin=");                   

      int pinNumber = (esp8266.read()-48)*10; 

      pinNumber += (esp8266.read()-48);    

      digitalWrite(pinNumber, !digitalRead(pinNumber));

      // The following commands will close the connection 

      String closeCommand = "AT+CIPCLOSE="; 

      closeCommand+=connectionId; 

      closeCommand+="\r\n";

      sendData(closeCommand,1000,DEBUG);

    } 

  } 

}

String sendData(String command, const int timeout, boolean debug) 

{

  String response = " "; 

  esp8266.print(command);           // Send the command to the ESP8266

  long int time = millis();

  while( (time+timeout) > millis()) 

  {

    while(esp8266.available()) ;

    {

      char c = esp8266.read();     

      response+=c;                  

    }

  }

  if(debug)

  { 

    Serial.print(response);         

  }

  return response;
