# Writeup for HTB Factory

Problem: Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system.

Very unfamiliar environment for me. The pictures seem to be useful.
![interface_setup.png](https://raw.githubusercontent.com/Sneedemgottem/hackthebox/main/Hardware/Factory/files/interface_setup.png)

Before any research, let's connect to "laptop 1" using netcat and see what we get.

![ncinterface.png](https://raw.githubusercontent.com/Sneedemgottem/hackthebox/main/Hardware/Factory/files/ncinterface.png)

So we get 2 important options here. Using option 1, it's become obvious how we can acquire this flag. My guess is we have to change the valve values until the water gets drained from the facility and the flag will reveal itself.

That being said, let's find out what a modbus command is. There are two specific functions that caught my eye when looking at the modbus RTU functions. **Read Coils** and **Write Single Coil**. From the picture, we can see a table of addresses and coils. And from the ladder logic, it only shows coils. It's safe to assume we are only dealing with coils in this case and not registers. Further deduction shows that because we can see the status of important coils from the netcat interface, we will only be sending write single coil packets. Let's see what's inside a write single coil packet:


![interface_setup.png](https://raw.githubusercontent.com/Sneedemgottem/hackthebox/main/Hardware/Factory/files/writesinglecoil.png)

In the [image](https://www.modbustools.com/modbus.html#function05), it says that laptop 2 will turn our command into a valid packet. I'm not sure what fields outside of the error check and CRC we are supposed to include, so through some fuzzing, it appears option 2 in the nc interface only takes 12 characters, meaning we have to populae slave address to write data lo. Also a very important thing to note: "The requested ON / OFF state is specified by a constant in the request data field. A value of FF 00 hex requests the coil to be ON. A value of 00 00 requests it to be OFF. All other values are illegal and will not affect the coil."

With this information in mind, we have enough information to turn any coil on/off. Here is a table I manually created with the commands:

Coil Name|On|Off
:---:|:---:|:---:
Manual Mode Control|520526dbff00|520526db0000
In Valve|5205000Cff00|5205000C0000
Out valve|52050015ff00|520500150000
Start|52050021ff00|520500210000
Cutoff|52050005ff00|520500050000
Cutoff In|5205001aff00|5205001a0000
Force Start Out|52050034ff00|520500340000
Force Start In|52050538ff00|520505380000

Using this information, you should be able to follow the PLC ladder logic diagram and eventually force the water out. Once the facility stops flooding, the flag will reveal itself. Good luck!
