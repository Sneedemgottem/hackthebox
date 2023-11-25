# Writeup for HTB Factory

Problem: Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system.

Very unfamiliar environment for me. The pictures seem to be useful.
![interface_setup.png](https://raw.githubusercontent.com/Sneedemgottem/hackthebox/main/Hardware/Factory/files/interface_setup.png)

Before any research, let's connect to "laptop 1" using netcat and see what we get.

![ncinterface.png](https://raw.githubusercontent.com/Sneedemgottem/hackthebox/main/Hardware/Factory/files/ncinterface.png)

So we get 2 important options here. Using option 1, it's become obvious how we can acquire this flag. My guess is we have to change the valve values until the water gets drained from the facility and the flag will reveal itself.

That being said, let's find out what a modbus command is.
