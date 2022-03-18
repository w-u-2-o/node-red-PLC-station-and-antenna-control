# node-red-PLC-station-and-antenna-control

Windows Node Red flows to control station and antenna switching via a Modbus PLC.

SCREENSHOTS

Screenshots can be found in the Github Wiki entry for this repository.

INTRODUCTION

This implementation is highly specific to my particular amateur radio station. Nevertheless, it may be useful in part to other efforts, and as an example of the great things you can do with Modbus communications and a programmable logic controller (PLC).

This submission is not intended to be a detailed Modbus or PLC tutorial. There are plenty of other resources for that, and some study of those topics before delving into these flows may be beneficial.

My station utilizes the very low cost Click PLC components from Automation Direct. There are four modules: power supply, CPU, low current relay unit and high current relay unit. Modbus communications between Node Red and the PLC CPU are via Ethernet.

The PLC relays control power and switching throughout the station, including tuner power, dummy load switching, radio power switching, and control of the relays in a KK1L 2x6 antenna switch.

This being only control of relays, the implementation is quite simple, as PLCs are capable of digital and analog input sensing and a great many other things. Eventually I may add current and voltage sensing and other functions.

THE FLOWS

There are two flows, one for control and UI, the other to capture settings associated with the antenna switch.

The Control and UI Flow:

I use the buttons for both control and feedback. The color and text labelling of a button reflect the state of a PLC relay. This state is implemented by a function node that feeds each button. This state is determined in true closed loop fashion, i.e. I never assume a button click has the desired effect, instead the PLC is polled for relay (or in PLC parlance, "coil") state, and THAT is the state reflected by the button. Thus if something should go wrong with the Modbus comm's or if there is a bug in the flow at least the operator knows the true state of affairs. Coil status is polled at a 4Hz rate so that the flow is nice and responsive.

I also sometimes use buttons as mere labels because I like the way they look!

That's the complicated part. The easy part are the buttons issuing the Modbus commands to the PLC. Well, it's slightly complicated as the function nodes change those messages based on the coil states so that the buttons become toggle buttons, but that's not too bad. The entire UI is therefore rather compact and efficient. I know that many people prefer separate buttons and indicators. However the same basic function node-to-button-and-indicator configuration should work just as well in such a case.

There are a couple of custom status (text) messages that are hard-coded and displayed based on the state of the coils that control the KK1L 2x6 antenna switch. Again, these are very specific to my implementation, but perhaps a good example for someone who is implementing PLC control.

At the bottom of the flow a link-in node receives the same CAT commands sent to the KPA1500 amp for band switching. These commands are decoded and used to access a table of BCD values that are in turn used to drive the KK1L switch via Modbus. This section is fairly generic and programmable. If I ever add more antennas to the switch this will allow easy access without additional programming. Perhaps I will extend this in the future to include antenna labels instead of the hard-coded way I did that.

That same link-in node also receives custom, unique CAT commands that start with "xx" ("XX" is also supported), xxat1; commands the tuner power on, xxat0; commands the tuner power off. This works with the station macro buttons (in another flow documented elsewhere). I can easily extend this to any other switching function, such as with the dummy laod. Indeed, this method could be used to invoke a large list of custom CAT commands for the PLC in the future. But at this time I only needed the one CAT command for the tuner.

Settings flow:

The settings flow implements a table that contains a BCD value for the antenna switch by band. As noted above, I will probably extend this in the future to make antenna naming and annunciation less hard-coded.

Unfortunately the node-red-contrib-modbus package does not allow for non-hard-coded control of PLC IP or port addresses, which is too bad, else those would also be handled in the settings flow. Also, be cautious with the configuration of Node Red and this package as it tends to lag behind the latest release of Node Red and you may need to use nvm or an equivalent method to install an ealier revision of Node Red than the current release.

Antenna switch settings are saved in c:\node-red\antenna_settings.json. The directory will be created if it is missing.
 
Developed under node 16.12.0 and npm 8.1.0.
 