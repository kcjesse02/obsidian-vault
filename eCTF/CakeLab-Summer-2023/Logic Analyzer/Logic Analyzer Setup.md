# Hardware
Here is all of the stuff that came with the logic analyzer
![[Screenshot 2023-07-17 at 12.39.54 PM.png]]

This Logic analyzer has 8 channels. These are where the desired voltage data will be collected. Here is the pin configuration from the data sheet (although it is also present on the bottom of the analyzer)
![[Screenshot 2023-07-17 at 12.06.45 PM.png]]

![[Screenshot 2023-07-17 at 12.37.23 PM.png]]

The logic analyzer kit also comes with two 8 pin jumper wires to connect devices to the various logic channels (and to ground)
![[Screenshot 2023-07-17 at 1.49.39 PM.png]]

With that, we can either connect directly to a pin on a board or use one of the provided grabbers to attach it to an exposed part of the connection.
![[Screenshot 2023-07-17 at 2.02.24 PM.png]]

The first can be done if attempting to read the uart on the chipwhisperer since all of the pins are set up to be connected to via jumper wires by the base board.

The latter is useful for a less 

So, hardware setup is pretty easy, just connect the desired channel


# Software
The Saleae logic analyzers connect to your computer and use this program to control the hardware and analyze signals: https://www.saleae.com/downloads/
(There are demo scenarios that you can try that don't require a logic analyzer)

The program should automatically detect the logic analyzer once it's plugged in and you should see a view like this:
![[Screenshot 2023-07-17 at 11.49.50 AM.png]]

To Read UART You will need two Digital Channels, one for TX and one for RX. I used channel 0 and 1. You can rename the channels to something more specific by clicking on their names

![[Screenshot 2023-07-17 at 11.53.39 AM.png]]

Now you need to set up the analyzer for each channel. This will allow the collected signals to be interpreted in hex automatically. 

go to the `analyzers` tab on the left side of the application and click the plus-sign in the corner and choose the `Async Serial` option.

This tab will show all of the active analyzers and the data gathered from them.

![[Screenshot 2023-07-17 at 2.18.53 PM.png]]

Creating the analyzer will open a settings window. These are the settings used for the Chipwhisperer. 

Most as the settings can be left as is, but the Bit Rate will be different for each device you are viewing and should be adjusted. Common Bit Rates for UART are 9600, 19200, 38400, 57600, and 115200. using one of these should work.

Also, you can only designate one channel per analyzer, so you will need to make one analyzer for the Tx channel, and one for the Rx.

![[Screenshot 2023-07-17 at 2.14.36 PM.png]]

This tab will also show 
#### Determining bit rate
This example has a bit rate that is too low. You can see that it is only interpreting 4 bytes when there really should be around 8.

![[Screenshot 2023-07-17 at 2.40.23 PM.png]]

This example has a bitrate that is too high (1152000). It is interpreting many more bits than UART can transfer (which is 9 bits) so we know it should be lower

![[Screenshot 2023-07-17 at 2.42.29 PM.png]]

#### Final Setup view
![[Screenshot 2023-07-17 at 11.44.35 AM.png]]

## Collecting data
To start collecting data, click the green play button. relevant data points will appear in the analyzers section. You may need to scroll out to find the section with relevent data, but finding the area where transmission is occurring should be rather simple

## Questions
- Is there a tool that can allow for the replay of UART signals?
- Could this have helped diagnosing the uart pull-up resistor issue?
- 