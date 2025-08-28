# ADS-B Receiver with Raspberry Pi 2B and Nooelec NESDR SMArt v5

--------------------------------------------------
INTRODUCTION: WHAT IS ADS-B?
--------------------------------------------------

**ADS-B (Automatic Dependent Surveillance – Broadcast)** is a surveillance technology used in aviation that allows aircraft to broadcast their position, speed, altitude, and other flight data in real time. These signals are transmitted over 1090 MHz and can be received by ground stations, air traffic controllers, or hobbyists with affordable SDR (Software Defined Radio) equipment.

ADS-B has become a key component of modern air traffic management, as it provides more accurate and frequent updates compared to traditional radar systems. By using an SDR dongle and software such as **dump1090**, anyone can build a low-cost ADS-B receiver to track aircraft flying within range of their antenna. That is exactly what Flight Radar is doing :)

This project demonstrates how to install and configure an ADS-B receiver using a Raspberry Pi 2B connected to a Nooelec NESDR SMArt v5 dongle. The setup was done on Linux Fedora, but the instructions apply to other Debian-based distributions as well (e.g., Raspbian).

--------------------------------------------------
HARDWARE REQUIREMENTS
--------------------------------------------------
- Raspberry Pi 2B (or higher)
- Nooelec NESDR SMArt v5
- ADS-B antenna (1090 MHz)
- Network connection (Ethernet or WiFi)
- Here's the pack SDR+Antenna that I used: https://amzn.eu/d/9ScVvfN

--------------------------------------------------
SOFTWARE REQUIREMENTS
--------------------------------------------------
- Linux (Fedora, Raspbian, Debian-based)
- Development packages (git, cmake, build-essential, libusb-1.0-0-dev)
- librtlsdr
- dump1090

--------------------------------------------------
INSTALLATION AND CONFIGURATION
--------------------------------------------------


0. Prepare the Raspberry Pi:
   - Download and install the latest Raspberry Pi OS (or your preferred Linux distribution) onto a microSD card using tools like **Raspberry Pi Imager** or **balenaEtcher**.
   - Insert the microSD card into the Raspberry Pi.
   - Connect the Raspberry Pi to power, network (Ethernet or WiFi), and peripherals (keyboard, mouse, monitor) for the initial setup.
   - Enable SSH (can be done from Raspberry Pi Imager or by creating an empty file named `ssh` in the boot partition of the microSD).
   - Optionally, set up WiFi credentials before first boot by editing the `wpa_supplicant.conf` file in the boot partition.
   - Boot the Raspberry Pi and ensure it has internet access.

   
1. Connect to Raspberry Pi via SSH:
   ssh pi@raspberrypi.local
   (Default password: raspberry)

   If not working, find the IP with:
     arp -a
     ssh pi@<raspberry_ip>

2. Update the system:
   sudo apt update && sudo apt upgrade -y

3. Install dependencies:
   sudo apt install git cmake build-essential libusb-1.0-0-dev -y

4. Install librtlsdr:
   git clone https://github.com/steve-m/librtlsdr.git
   cd librtlsdr
   mkdir build && cd build
   cmake ..
   make
   sudo make install
   sudo ldconfig
   rtl_test

   (Expected output: Found 1 device(s): 0: Nooelec NESDR SMArt v5)

5. Install dump1090:
   cd ~
   git clone https://github.com/antirez/dump1090.git
   cd dump1090
   make

6. Run dump1090:
   ./dump1090 --interactive         # See aircraft in the terminal
   ./dump1090 --interactive --net   # Web view: http://<raspberry_ip>:8080

--------------------------------------------------
TROUBLESHOOTING
--------------------------------------------------

Error: 
  'Kernel driver is active, or device is claimed by second instance of librtlsdr. 
   (dvb_usb_rtl28xxu) 
   usb_claim_interface error -6
   Error opening the RTLSDR device: Device or resource busy'

Solution: disable the DVB driver
   sudo nano /etc/modprobe.d/blacklist-rtl.conf

   Add the following lines:
     blacklist dvb_usb_rtl28xxu
     blacklist rtl2832
     blacklist rtl2830

   Save (Ctrl+O, Enter, Ctrl+X) and run:
     sudo update-initramfs -u

--------------------------------------------------
USAGE
--------------------------------------------------
- To view detected aircraft in the terminal:
  ./dump1090 --interactive

- To view them in your web browser:
  http://<raspberry_ip>:8080

--------------------------------------------------
ACKNOWLEDGEMENTS
--------------------------------------------------
- [Steve Markgraf](https://github.com/steve-m) for **librtlsdr**
- [Salvatore Sanfilippo (antirez)](https://github.com/antirez) for **dump1090**
- Nooelec for providing the SDR hardware

--------------------------------------------------
LICENSE
--------------------------------------------------
MIT License

Copyright (c) 2025 Adam Mouslik El Moudakkar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

--------------------------------------------------
AUTHOR
--------------------------------------------------
Project created by: Adam Mouslik El Moudakkar
Date: 2025

