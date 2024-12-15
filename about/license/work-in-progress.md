# Work in Progress

In this chapter we want to give an hands on example on how to hack your first IoT device and dive into hardware hacking.

I found this old Asus router in the basement, which was replaced a long time ago and was not of any use anymore. A perfect target for a hardware hacking experiment!

## Reconnaissance

#### OSINT

In the first step I googled the model number for my router "ASUS RT N12 D1" and I came accross this [article](https://redfoxsec.com/blog/asus-rt-n12-b1s-privilege-escalation-cve-2024-28326/). It shows that a similar model the  "ASUS RT N12+ B1" appears to have an open UART interface, which gives unauthenticated root access. It does not show how to exacltly abuse this or any details where to find the UART interface.  Let's see if our router model may have the same vulnerability!

Moreover we find the FCC ID on the back of the router:

<figure><img src="../../.gitbook/assets/image (79).png" alt="" width="383"><figcaption><p>FCC ID</p></figcaption></figure>

In the US every device, which provides RF communication like an router must have an FCC-ID. The Federal Communications Commission (FCC) will publish an report including internal pictures of each device. So we can take a look on internal pictures of the router without the need to open it!

As you can see there are 4 connector pads on the top right of the PCB. This layout is very typical for a UART interfaces, which often provides 4 pins (RX,TX,VCC,GND).

#### Open the device

Enough research! Let's take a look for ourselves and open the device.  Our goal is to identify components of interest, which might be storing chips like RAM or flash chips, debug ports and interfaces. It's always a good idea to take a  of your device and label component, which you could identify.

<figure><img src="../../.gitbook/assets/image.png" alt="" width="375"><figcaption><p>Identified components</p></figcaption></figure>

&#x20;As we can see next to the flash chip there are 4 connector pins. Using a multimeter, we can try to identify each of the pins performing a continuity test. For that we need test points for ground and power. We can use the GND and VCC pins of the flash chip as a reference, since we can look them up on the datasheet of the flash chip.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption><p>Flash chip pinout</p></figcaption></figure>

Using this method we can easily identify GND and VCC. To distinguish TX and RX pins, we can power on the device and see that one of the pin has a fluctuating voltage ranging from 1.8 to 3.3 V. This pin should be the TX pin of the UART interface. Hence, we got this layout:

<figure><img src="../../.gitbook/assets/image (84).png" alt="" width="320"><figcaption><p>UART interface pinout</p></figcaption></figure>

## Interface Interaction

To interact with the UART interface we need an UART-to-TTL USB adapter.