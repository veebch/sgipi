![Sketch](/images/sgipi_gh.jpg)

[![YouTube Channel Views](https://img.shields.io/youtube/channel/views/UCz5BOU9J9pB_O0B8-rDjCWQ?style=flat&logo=youtube&logoColor=red&labelColor=white&color=ffed53)](https://www.youtube.com/channel/UCz5BOU9J9pB_O0B8-rDjCWQ) [![Github](https://img.shields.io/github/stars/veebch?style=flat&logo=github&logoColor=black&labelColor=white&color=ffed53)](https://www.github.com/veebch/)

# SGI Pi

The lack of modularity on the Pi 500+ was enough to make us build an alternative out of a Compute Module 5. The idea is to make something a bit more future proof that doesn't involve a perfectly good keyboard becoming ewaste once the brains of the machine start to look dated. 

It has the upside of letting you use a hot-swappable keyboard so that you can swap out switches as you go down the mechanical keyboard rabbit hole. It also has full size HDMI ports and a speaker to let you add a [startup sound](https://www.youtube.com/watch?v=CH9saUP2460).

# Parts
- A Raspberry Pi CM5
- A Raspberry Pi CM5 IO Board
- nVME drive
- An 8 ohm 3w speaker
- MAX98357 Audio Power Amplifier Module
- 3d printed case (stl files in [/3d](/3d))
- 4 x feet (to give some clearance for the down-firing speaker)
- Wires (to attach the speaker to the HAT)

# Assembly

Attach the 8 ohm speaker to the 3d printed lid, pointing downwards. Then connect to the MAX98357 amp as follows:

| MAX98357A Pin | Connect to CM5 J8 Pin | GPIO   | Function                |
| ------------- | --------------------- | ------ | ----------------------- |
| VIN           | Pin 2 or Pin 4        | —      | 5 V power supply        |
| GND           | Pin 6 (or any GND)    | —      | Ground                  |
| BCLK          | Pin 12                | GPIO18 | I²S Bit Clock           |
| LRC           | Pin 35                | GPIO19 | I²S Word Select (LRCLK) |
| DIN           | Pin 40                | GPIO21 | I²S Audio Data          |

| MAX98357A | Speaker   |
| --------- | --------- |
| SPK+      | Speaker + |
| SPK−      | Speaker − |













