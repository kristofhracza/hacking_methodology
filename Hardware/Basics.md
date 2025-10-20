*All credits go to Alex Teague of Prism Infosec.*
- Always take notes on any device in case there's an update that changes something
- Have two machines of different distros.
	1. One out of date, to make sure nothing breaks and have a backup.
	2. One new and up to date.
- Take notes anytime a test is being done. Good for later reference.

# Analog I/O
- Very granular - uses ranges as output
- It runs between 4-20 mA.
- Less than 4 mA is used for error checking.

# Digital I/O
- Uses Off/ON state instead of range like Analog.
- ADC / DAC is used for calculations.

# Micro Controller
Has:
- I/O
- RAM and EPROM
- CPU

# System on Chip (SOC)
- Lot of systems run on SOC
- SOC is able to run shellcode as well (good for testing)

# Single Board Computer (SBC):
- Small computer device
- It works kind of like a Micro Controller but the output is on cable.
	- The output is a MINIMUM -- but always assume higher
- Good standard to read through data sheets to get familiar with the chip/computer

# UART
- **Used for Asynchronous communication**
- Can be enumerated via the Init (`init.d`) script.


# FCCID
- Documentation and data sheet storage: [https://fccid.io/](https://fccid.io/)