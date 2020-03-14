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

  String response = ""; 

  esp8266.print(command);           // Send the command to the ESP8266

  long int time = millis();

  while( (time+timeout) > millis()) 

  {

    while(esp8266.available();

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
