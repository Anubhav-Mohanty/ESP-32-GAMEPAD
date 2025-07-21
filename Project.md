# ESP-32-GAMEPAD

Repository for ESP-32 based DIY GamePad  .

# Introduction

This is my project "ESP-32 based Gamepad". We all have played games such as Mario,Contra and Tetris etc  with our gamepad controller. I always wanted to have a game controller and ever since i lost my gamepad and game disc i have been longing to have one . i have always missed my old game pad and my small gaming cd which had over 50 games , old fashioned games , pure nostalgia and excitement.

This project i made this for fun and i did it because i liked it and i wanted to play . In fact i also had an idea why not make a small io game which i can play whenever i want no internet required just a power supply .

# Components I used

 - ESP-32 
 - 6 Tacticle switches
 - One Micro USB cable
 - A breadboard
 - Male to Male Jumper wires .

Since this was my DIY project i have used minimal set up , so that anyone can play with minimal setup , no complex thingy .

# Explanation
This project has two main purpose :

1-To make a small ands simple io game based on HTML/CSS AND JS.
2-To make a controller based on ESP-32 to control the movement of our player on the screen and simultaneoulsy host it via our ESP.

Tactile Switches help to enter input to our ESP-32 microcontroller . Based on the state of input entered(i.e 1=high , 0=low), the ESP-32 will make changes in the position of the player's object . 

## Flow of Control 

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


















