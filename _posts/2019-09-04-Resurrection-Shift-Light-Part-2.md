---
layout: post
title: Resurrection of the Shift Light - Part 2
---

Back in 2016 while in Uni, I thought of an interesting project based on some of my interests: cars, software, and hardware. The project was a shift light for manual transmission cars, displaying the RPM on a range of LEDs as well as a 7 segment display indicating the current gear.

## Some History

<p align="center">
    <img width="45%" height="45%" src="/images/resurrection_arduino_shift_light/rev_0_1_front.jpg">
    <img width="45%" height="45%" src="/images/resurrection_arduino_shift_light/rev_0_1_back.jpg">
</p>
<p align="center">
    <font size="3">
        Fig 1: Revision 0.1 Prototype shift light
    </font>
</p>

Above is the first prototype concept build of the shift light. Yes, it looks quite janky, however it was more of a proof of concept used to test out the code. This revision used ten LEDs for displaying the RPM data. 

Initial work began in May of 2016, with a prototype build finished around the end of June 2016. Although this revision worked, it was quite obviously not worth putting in the car. The build quality and design was non-existent, but the majority of the code and hardware was finalized during this revision.

<p align="center">
    <img width="45%" height="45%" src="/images/resurrection_arduino_shift_light/rev_0_2_front.jpg">
    <img width="45%" height="45%" src="/images/resurrection_arduino_shift_light/rev_0_2_back.jpg">
</p>
<p align="center">
    <font size="3">
        Fig 1: Revision 0.2 Prototype shift light
    </font>
</p>

About a year later around May 2017, I decided to revisit the shift light project. With the help of my friend Aaron, we 3D printed a well designed enclosure for the project. We even went as far as measuring the slight curvature of my dashboard, and the results were better than expected as shown above. After implementing some slight changes to the code, the "final" revision of the shift light was finished. Or so I thought.

Due to summer heat, the 3D printed housing started to sag caused by heat warping. This occurred because we used PLA filament for the enclosure, which has a glass transition temperature of around 60 to 65 degrees Celcius. In comparision, ABS filament has a transition temperature of around 80 to 110 degrees Celcius. 

## Next Steps
The shift light project is the longest running project I have, and also my most favorite. The original idea and design were simple, however the actual implementation was quite naive in its approach. Throughout the few years since the birth of this project, I have gained an abundance of information and skills related to embedded systems. My plan is to utilize this accumulated knowledge to build a better revision than before.

The next steps in this project will be to take a few steps back and reassess the design aspect. I will review existing part selection and add/remove components as needed. I will also design a more robust 3D printed enclosure using ABS filament. The messy circuitry from before will be binned and replaced with a custom PCB designed and populated by myself. The last potential improvement will be to completely redo the software, with the intent of making the code faster, modular, and portable. 

<p align="right">
    <br>
    <a href="/post/2019-09-04-Resurrection-Shift-Light-Part-3.md">
    Resurrection of the Shift Light - Part 3
    </a>
</p>

