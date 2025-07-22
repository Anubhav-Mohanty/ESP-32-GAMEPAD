# ESP-32-GAMEPAD

Repository for ESP-32 based DIY GamePad  .
![]

# INTRODCUTION

This is my project "ESP-32 based Gamepad". We all have played games such as Mario,Contra and Tetris etc  with our gamepad controller. I always wanted to have a game controller and ever since i lost my gamepad and game disc i have been longing to have one . i have always missed my old game pad and my small gaming cd which had over 50 games , old fashioned games , pure nostalgia and excitement.

This project i made this for fun and i did it because i liked it and i wanted to play . In fact i also had an idea why not make a small io game which i can play whenever i want no internet required just a power supply .

# MINIMUM COMPONENTS NEEDED 

 - **ESP-32 (38-pin)**
 - **6 Tacticle switches**
 - **One Micro USB cable**
 - **A breadboard**
 - **Male to Male Jumper wires .**

Since this was my DIY project i have used minimal set up , so that anyone can play with minimal setup , no complex thingy .

# EXPLANATION
This project has two main purpose :

1-To make a small ands simple io game based on HTML/CSS AND JS.

2-To make a controller based on ESP-32 to control the movement of our player on the screen and simultaneoulsy host it via our ESP.

Tactile Switches help to enter input to our ESP-32 microcontroller . Based on the state of input entered(i.e 1=high , 0=low), the ESP-32 will make changes in the position of the player's object . 

## FLOW OF CONTROL 

In this section I will explain how the code actually works via flow of Control .




## How to make this circuit ?
TO make this circuit we will be using 6 tactile switches ( start , sensitivity control , up ,down , right ,left ) , and they were connected to esp32 gpio pins . 

 | Button Name | GPIO Number |         Purpose         |
 |-------------|-------------|-------------------------|
 |   Start     |       2     |To Start the Game when pressed.
 | Sensitivity |      27     |To adjest the sensitivity of the movement 
 |     Up      |      15     |To move the player up by n 
 |    Down     |      13     |To move the player down by n
 |    Left     |      14     |To move the player left by n 
 |    Right    |       0     |To move the player right by n 

The variable n stands for sensitivty or how much distance the player covers in one click of direction buttons . FOr this code i have taken sensitivity from 1 to 5 although we can change it as per our requirement . 

## GamePad Screen 


## CODE EXPLANATION

### BACKEND  CODE (ESP-32 AND PUSH BUTTONS) AND HARDWARE EXPLANATION

The backend part of the code is the raw , proper ardunio ide code for programming the esp32 with push button . I will explain all my code in layman's terms so that anyone can understand how this thing works . 

So how to think what we need for this project . 

1. The Game is based on wifi connectivity and is being hosted on a webserver so thats why we need :
```
#include<Wifi.h>
#include<Webserver.h>
```
`#include<Wifi.h>` - this includes the wifi headewr file into our program 
`#include<Webserver.h>` - this includes the header file necessary to host the webserver via ESP-32 .

Now we need a network since we are hosting a webserver . 
```
const ssid="Write your name of hostpot device";

const password ="Password of your device that is providing hotspot "
```
`const ssid`- This variable is initiaated with `const` keyword so that it wouldnt change over time or cause any sort of error.

`const password`- This variable is also initiatd with const and stores the password of our hostpot provider , like in my code i have used my mobile as a device.

Since we are using 6 buttons i have initialized 6 buttons for start sensitivity and 4 direction buttons .
```
// Button pin definitions
const int start_button_Pin = 2;
const int top_button_Pin = 15;
const int left_button_Pin = 14;
const int right_button_Pin = 0;
const int down_button_Pin = 13;
const int sensi_button_Pin = 27;
```
We have initialized our buttons with their respecitve GPIOs in ESP32 .

Now we  have to initlize the button states of each button . In simpler terms button states are the initial states of button when they are not pressed .

```
// Button states
int start_buttonState = HIGH;
int top_buttonState = HIGH;
int left_buttonState = HIGH;
int right_buttonState = HIGH;
int down_buttonState = HIGH;
int sensi_buttonState = HIGH;
```
Now one may ask why it is initialized with HIGH . The reason is **most of tactile button initially have their ground disconnected in their off state hence logic1(HIGH) but when the button is pressed the circuit is connected to ground hence making logic0(LOW)**

After initilizing we the button states , we must be careful with one common mistake involving button pressing (high state an low state) . 

**What is this mistake  ?**

The mistake im reffering to is **not using edge detection in our program** . One may ask what is edge detection or how is it important here .Edge detection means we are detecting the rising edge and falling edge of the input(button state). In other words we dont want coordinate values of player to change when button is held down continously , we will only change it when button is pressed once(single press). 

**Is edge detection necessary**

Yes edge detection is very necessary because Arduino IDE runs at faster pace in the `void loop()` section so if not used edge detection it would change position multiple time even if the button is pressed once . In other words if the control flow is at nx speed and the time gap between your prssing and releasing the button is x second the the command would be excetecuted 

> nx/x= n times .


So even if you've just pressed once instead of updating the player position by `(position+sensitivity)` it will update `n X (position+sensitivity)` which would be an error . 

In other words for every change we need in position

- **we have to press the button once and rapidly**
- **holding it down would chnage only once since , but not increase it continously if held down for a long time.**

Here is the piece of code that does the edge detection thing : 
```
// Previous button states for edge detection
int last_start_buttonState = HIGH;
int last_top_buttonState = HIGH;
int last_left_buttonState = HIGH;
int last_right_buttonState = HIGH;
int last_down_buttonState = HIGH;
int last_sensi_buttonState = HIGH;
```
See how I have initilized the rpevious button states as HIGH (same as the first initlized button state) .

Now we need to set up the sensitivity.
```
// Sensitivity setup
bool setSensitivity = false;
int sensi_count = 1;
```
`bool setSensitivity = false;`Sets the inital sensitivity value as false .

`int sensi_count = 1;`Sets the initial sensiticvity count to 1 , although we can change the sensitivity as we want . I do not recommend playing with sensitivity 1 change it to 5 or higher its easy to shift . 

Now we proceed to the `void setup()` part of the program.
```
// Initialize button pins
  pinMode(start_button_Pin, INPUT_PULLUP);
  pinMode(top_button_Pin, INPUT_PULLUP);
  pinMode(left_button_Pin, INPUT_PULLUP);
  pinMode(right_button_Pin, INPUT_PULLUP);
  pinMode(down_button_Pin, INPUT_PULLUP);
  pinMode(sensi_button_Pin, INPUT_PULLUP);
```
The pinMode(<button_Pin>,INPUT_PULLUP) syntax means : We are setlecting the button and assigning its function that is taking input. The `INPUT_PULLUP` helps to ttack the 10k ohm  internal pull-up resistor to the button.

This is the wifi connectivity block of my code .
```
// Connect to WiFi
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println();
  Serial.print("Connected! IP address: ");
  Serial.println(WiFi.localIP());
  Serial.println("Open this IP in your browser to play the game!");
```
Once the ESP-32 is connected to our wifi and it will show the message as well as the IP adress of wifi in the Serial Monitor . There is anothe approach to solve this that is by assigning a static ip-adress but i do not recommend that method as it would be problematic if there happens to be a device with same IP addrsss our ESP . 

After getting the IP Address we can enter it in Search bar of Google CHrome /Edge whatever webserver you use .



### FRONTEND CODE (ESP-32 AND WEBSERVER)

The frontend part of the code is whzt is being displayed on the screen it cincludes contents such as:

- ESP-32 status
- Sensitivity
- Game Canvas (the screen where game is working)
- Initial Position of Player in the Game
- Score
- Help Manual for the User .

  Below are the code blocks used for server display with their working .




# IMAGES 


# VIDEOS 



# WHY I MADE THIS ?














