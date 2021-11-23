# HOW-ROTARY-ENCODER-WORKS-WITH-DC-MOTOR


![image](https://user-images.githubusercontent.com/19898602/127158880-d43152e6-31fd-4ea1-b4e3-32ff8b9dc970.png)

Hello friends in this post we will learn Controlling N20 Micro Gear Encoder Motor with arduino
This micro motor have metal gears box directly connected to the output shaft of the motor
in this way this micro looking motor delivery quite high torque comparative to its size.

Such micro metal gear motors come in two models with encoder and without encoders
In this post we are going to learn how we can connect the encoder of N20 micro motor and get the rotation feedback to the arduino.

This motor comes in different RPM configuration mine was 75RM

# N20 micro gear motor Encoder details

The encoder assembly is fitted on the back side of the motor
the encoder PCB is directly solder to the power terminals of the motor.

![image](https://user-images.githubusercontent.com/19898602/127158961-0999c932-5ce2-42ba-98d1-bae3ea05c4d0.png)


![image](https://user-images.githubusercontent.com/19898602/127158984-35250131-d7a7-4a83-bd6d-1a4b4f7c330b.png)

Q1 & Q2 – CC6101 hall effect sensor
R1, R2, R3 – 3.3Kohm Resistor
D1 – LED

Above is the internal circuit diagram of Encoder of N20 micro metal gear motor
as you can see Two C6101 hall effect sensors are placed diagonally to the circular magnet

and this magnet is fixed on the rear shaft of the moto as motor rotates the magnet will also rotate
and hall effect sensor detect the motion of the magnet and generate output at terminal C1 & C2

Before moving further in this tutorial, I would like to tell you something about PCB 

Yes PCB are the heart of the electronics based project usually we hesitate to try custom PCB and opt to homemade solutions

like breadboard or Zero PCB earlier I also was in the same boat, I hesitate to try custom PCB my belief was they are much expensive.

but then I came to know about [JLCPCB.com](https://jlcpcb.com/IAT) and I was totally surprised how low price PCB's are they offering 

there PCB quality is top to the mark now I always go with PCB for my project and [JLCPCB.com](https://jlcpcb.com/IAT) is my trusted 

PCB manufacturer, you can also try there PCB service for more details you can visit their website [JLCPCB.com](https://jlcpcb.com/IAT)


![image](https://user-images.githubusercontent.com/19898602/127161780-d9b742d8-fd97-4e1e-a35f-a6591f8c3411.png)![image](https://user-images.githubusercontent.com/19898602/127161903-d6753a2e-5242-4ab7-8406-29ac5a486a42.png)![image](https://user-images.githubusercontent.com/19898602/127162080-a4212957-1ebb-4e81-ad5e-bd3c1e0ecb87.png)




# Terminal details

![image](https://user-images.githubusercontent.com/19898602/127159205-488b2f3f-774a-4770-9bf7-f36da99cbd09.png)


> M1 – MOTOR TERMINAL
 
> GND – GND

> C1 – ENCODER OUTPUT (A)

> C2 – ENCODER OUTPUT (A)

> VCC – 5V SUPPLY OF ENCODER

> M2 – MOTOR TERMINAL


M1 & M2 – are the motor power terminals the positive and negative supply to this terminal will decide the direction of the rotation of motor

C1 & C2 – this terminal are the output A & B of Encoder
output must connect with arduino.

VCC – is 5V supply of encoder.

GND – is the system ground.

# Connection with arduino

Now we will connect our N20 encoder micro gear motor with arduino to do so we need a motor driver we cannot directly connect motor to the arduino pins.
For this we are using L293D motor driver for this purpose .

![image](https://user-images.githubusercontent.com/19898602/127159363-b6d8bf97-ded9-43c4-b2a2-0a0e23de20c1.png)

# Arduino Code


    #define MotFwd  3  // Motor Forward pin
    #define MotRev  4 // Motor Reverse pin
    
    int encoderPin1 = 2; //Encoder Output 'A' must connected with intreput pin of arduino.
    int encoderPin2 = 3; //Encoder Otput 'B' must connected with intreput pin of arduino.
    volatile int lastEncoded = 0; // Here updated value of encoder store.
    volatile long encoderValue = 0; // Raw encoder value
    
    
    void setup() {
    
      pinMode(MotFwd, OUTPUT); 
      pinMode(MotRev, OUTPUT); 
      Serial.begin(9600); //initialize serial comunication
    
       pinMode(encoderPin1, INPUT_PULLUP); 
      pinMode(encoderPin2, INPUT_PULLUP);
    
      digitalWrite(encoderPin1, HIGH); //turn pullup resistor on
      digitalWrite(encoderPin2, HIGH); //turn pullup resistor on
    
      //call updateEncoder() when any high/low changed seen
      //on interrupt 0 (pin 2), or interrupt 1 (pin 3) 
      attachInterrupt(0, updateEncoder, CHANGE); 
      attachInterrupt(1, updateEncoder, CHANGE);
    
    
    }
    
    void loop() {
    
    for (int i = 0; i <= 500; i++){
    digitalWrite(MotFwd, LOW); 
     digitalWrite(MotRev, HIGH);
     Serial.print("Forward  ");
     Serial.println(encoderValue);
    }
    
    delay(1000);
    
    for (int i = 0; i <= 500; i++){
    digitalWrite(MotFwd, HIGH); 
     digitalWrite(MotRev, LOW);
     Serial.print("Reverse  ");
     Serial.println(encoderValue);
    }
    
    delay(1000);
    
    } 
    
    void updateEncoder(){
      int MSB = digitalRead(encoderPin1); //MSB = most significant bit
      int LSB = digitalRead(encoderPin2); //LSB = least significant bit
    
      int encoded = (MSB << 1) |LSB; //converting the 2 pin value to single number
      int sum  = (lastEncoded << 2) | encoded; //adding it to the previous encoded value
    
      if(sum == 0b1101 || sum == 0b0100 || sum == 0b0010 || sum == 0b1011) encoderValue --;
      if(sum == 0b1110 || sum == 0b0111 || sum == 0b0001 || sum == 0b1000) encoderValue ++;
    
      lastEncoded = encoded; //store this value for next time
    
    }
    

Some explanation about the code.
This code will run motor in Forward direction for some time and then run in Reverse direction meanwhile we can get the encoder value at our Serial Monitor.

When motor moves in forward direction encoder value will increment and if motor moves in reverse direction the encoder value will decrease.
This is very basic code just to get idea how we can get encoder value from the encoder.

In next post we will se how we can perform position control using PID on N20 motor.
Encoder value calculation

Encoder pulse per revolution = 28

so one rotation of output shaft = Encoder pulse per revolution X Gear Ratio

So pulse per one rotation = 28 X 100

= 2800 Pulse per revolution.

