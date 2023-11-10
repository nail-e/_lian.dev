---
title: "In search of Amongi using Rust and Chudnovsky's Algorithm"
date: 2023-10-13T00:00:00+00:00
author: Elian Rieza
layout: post
permalink: /in-search-of-amongi-using-rust-and-chudnovskys-algorithm/
categories: Software
tags: [software, math, coding, rust]
---
Pi has always been 3.14159 to me and to everyone else really, it's only nerds who'd want to know pi up to its 100th or 1000th digit while its your supernerds who'd be searching for pi up to the millionth digit and well, I'm a supernerd (when I want to).

<img src="/assets/images/lightgate-post/real.jpg" alt="Arduino Lightgate" title="Lightgate" style="width:500px; height:300px; margin-right:20px; float: left;">

So I was bored one summers day and way too done with the monotony of math and physics revision and opened up YouTube and the first video that piqued my interest was Stand-Up Math's legendary video of finding Among Us in Pi. This Einsteinian ingenuity of a video warrants its own place in the annals of history for finding the meaning of life, death and everything in between but also raised a question in my head: how many Amongii are there in pi?

The code consisted of a function converting the first million digits of pi (removing the "3."") into binary then writing the binary output to a text file. Another function read from the file to search for the string "0111110011110101" or "1010111100111110". This was quite easy but it led me to a much cooler discovery.

\[ \sum_{n=1}^{\infty} \frac{1}{n^2} = \frac{\pi^2}{6} \]

{% highlight rust %}
// This code comes from GitHub page
#include <LiquidCrystal.h> 

LiquidCrystal lcd(12, 11, 5, 4, 3, 2); 
int threshold = 150;                      	  
const int count = 500; 
int readings[count];   
int readIndex, total, photoresistor; 
int length = 0; // Length of object passing thru tube in meters

void setup() { 
  Serial.begin(9600);          	  
  lcd.begin(16,2); 
  lcd.clear(); 
  for (int i = 0; i < count; i++) { 
    readings[i] = 0; 
  } 
} 

void loop() { 
  total = total - readings[readIndex]; 
  readings[readIndex] = analogRead(A0); 
  total = total + readings[readIndex]; 
  readIndex = readIndex + 1;
  if (readIndex >= count) { 
    readIndex = 0;
  } 

  photoresistor = analogRead(A0);    
  Serial.println(photoresistor);     
  
  if (photoresistor < threshold) { 
    lcd.clear(); 
    lcd.setCursor(0,0); 
    lcd.print("Lightgate Closed"); 
    lcd.setCursor(0,1); 
    lcd.print(length / readIndex);
    lcd.setCursor(5,1); 
    lcd.print("m/s"); 
  } 

  else { 
    lcd.setCursor(0,0); 
    lcd.print("Lightgate Clear "); 
    readIndex = 0; 
  } 
  delay(100);               	  
} 

{% endhighlight %}


<img src="/assets/images/lightgate-post/FTM2.jpg" alt="Arduino Lightgate" title="Lightgate" style="width:500px; height:300px; float: right; margin-left:20px">

In the end, I sided with it as I was planning to exhibit the model headless and my group really didn't want to lug around a behemoth of a gaming laptop just for this one exhibition.

The code for the board should first check the average reading of the first 10 readings to get the mean reading of the brightness. This allows for any negative deviations to register as the lightgate being used. As one photoresistor is attached, the length of the object planned to pass through the lightgate must be declared (in <span class="unit">km ⋅ h</span> <sup class="superscript">-1</sup> ) to get an accurate speed reading so as the photoresistor's digital value gets lower than a certain average threshold, a digital timer will start to calculate the time taken for the object to pass then the value is converted into hours. After this, the final value should be displayed on the LCD.

This is when I realized that I was missing a potentiometer. I couldn't find one in time so I decided to output the speed calculations on the computer instead and only use the LCD as a proof-of-concept. But all-in-all, writing the software for it was a breeze. The timer module for Arduino is an absolute godsend, using only two functions to be able to calculate time taken. I highly recommend it for anyone needing a timer function for Arduino. 


## Future Stuff 
One glaring issue was the concession I made with the average reading. While this probably could've been easily fixed by writing each value to an array of 10, time constraints (and severe procrastination) hampered it. It probably could've been added in 10 mins with an extra 10 minutes just for testing.

Lightgates are also usually upright but this one's upside down. Lab-grade lightgates use invisible lasers to calculate when and how fast an object breaks or shortens a laser's length so an analog sensor is caveman technology compared to a lab-grade lightgate.

Another problem is how the timer was counted. If I were creating this in a non-Arduino environment, my first instinct would've been using difference in Unix time but I found as I found a solution in the Timer module, which is way easier than Unix time difference. Timer isn't super efficient though, with a few tests showing different values for the same delay time but I guess 5 decimal places don't really matter.

## Links
- **[Github Page](https://github.com/nail-e/arduino-lightgate)**
- **[Project Gallery](https://github.com/nail-e/arduino-lightgate/tree/main/Gallery)**

