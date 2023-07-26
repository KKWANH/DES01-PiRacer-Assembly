# DES01-PiRacer-Assembly
## Assemble PiRacer
**Follow PiRacer Assembly Instructions**  
[PiRacer Assembly Instructions](https://www.waveshare.com/wiki/PiRacer_Assembly_Manual)

![PiRacer-image](/images/PiRacer-image.jpg)

## Set up Raspberry Pi
### 1. Write OS Image on Raspberry Pi
#### 1.1 Download Raspberry Pi Imager and Write OS image on SD Card
Download the Raspberry Pi Imager from the [Raspberry Pi website](https://www.raspberrypi.org/software/).
![Raspberry Pi Imager](/images/Raspberry-Pi-Imager.png)
#### 1.2 Setup Wifi on Ubuntu
Ubuntu uses Netplan to configure the net connection. By default, the configuration file is located in `/etc/netplan/50-cloud-init.yaml`.
```yml
network:
  version: 2
  wifis:
    wlan0: # wifi adapter name. check with `ip a` command
      dhcp4: false # use static ip or not
      dhcp6: false # same as above
      addresses:
        - XXX.XXX.X.X
      access-points:
          "SSID-NAME":
              password: "XXXXXXXXX"
```
```bash
sudo netplan apply # apply netplan configuration
ip a # check ip address whether it is set correctly
```
#### 1.3 Ubuntu Initial Setting
Add a repository to the Raspberry Pi's APT package management system so that packages can be retrieved from the official Raspberry Pi repository.
```bash
sudo -s
echo "deb http://archive.raspberrypi.org/debian/ buster main" >> /etc/apt/sources.list
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 7FA3303E
apt update
exit
```

#### 1.4 Install Packages
```bash
sudo apt update
sudo apt install \
    gcc \
    v4l-utils \
    i2c-tools \
    raspi-config \
    python3-dev \
    python3-setuptools \
    python3-venv \
    libopencv-dev \
    openserver-ssh \
    git \
    vim \
```
#### 1.5 Mount Boot Partition
On Ubuntu it is necessary to explicitly mount the boot partition before enabling the i2c controller and camera
```bash
mount /dev/mmcblk0p1 /boot/
raspi-config 
# Interface Options -> I2C -> Enable
# Interface Options -> Camera -> Enable
sudo reboot
```
Linux distributions such as Ubuntu can mount the /boot partition automatically, but in some cases it needs to be mounted manually. In particular, the /boot partition must be mounted to enable the i2c controller and camera.  
The above command mounts /dev/mmcblk0p1 (the partition name of the Raspberry Pi's SD card) in the /boot directory.

#### 1.6 Test Pi Racer Example
```bash
git clone https://github.com/SEA-ME/piracer_py.git
cd piracer_py
python3 -m venv venv
source venv/bin/activate

pip install piracer-py
```
## Project Knowledge
### What is Raspberry Pi?
The Raspberry Pi is a low-cost, **credit-card-sized computer** that plugs into a computer monitor or TV and uses a standard keyboard and mouse. It is a capable little device that enables people of all ages to explore computing and learn how to program in languages like Scratch and Python. It’s capable of doing everything you’d expect a desktop computer to do, from browsing the internet and playing high-definition videos to making spreadsheets, word processing, and playing games.

[https://www.raspberrypi.org/help/what- is-a-raspberry-pi/](https://www.raspberrypi.org/help/what-%20is-a-raspberry-pi/)

### **What is its purpose?**
Raspberry Pi can do everything you’d expect a desktop computer to do, like browsing the internet, playing high-definition videos, making spreadsheets, and so on. Furthermore, Raspberry Pi is used from music machines and parent detectors to weather stations, tweeting birdhouses with infrared cameras, and learning how to do ML.

### **Why do people use Python to control RiRacer?**

**Rich Libraries and Frameworks:** Python has a vast ecosystem of libraries and frameworks that support various functionalities. There are numerous libraries available for robotics and hardware control, such as PySerial for serial communication, Pygame for GUI and gamepad control, and TensorFlow for machine learning tasks related to robotics.

### **How does the Raspberry Pi differ from a traditional computer?**

1. size and cost

    Unlike traditional computers, The Raspberry Pi is a low-cost, **credit-card-sized computer.** 

2. Flexibility

    The Raspberry Pi is designed to be highly flexible and customizable. It can be used for a wide range of projects, from music machines and weather stations to parent detectors and tweeting birdhouses.

### **What is the purpose of the GPIO (General Purpose Input Output) pins on a Raspberry Pi?**

These pins offer a direct connection to the System on Chip (SoC) at the heart of the Pi enabling the Pi to communicate with external components and addons known as HATs (Hardware Attached on Top).

The GPIO is the most basic, yet accessible aspect of the Raspberry Pi. GPIO pins are digital which means they can have two states, off or on. They can have a direction to receive or send current (input, output respectively) and we can control the state and direction of the pins using programming languages such as Python, JavaScript, node-RED etc.

Certain GPIO pins also have alternate functions that allow them to interface with different kinds of devices that use the I2C, SPI or UART protocols.

![GPIO-image](/images/GPIO.png)  
https://pinout.xyz/pinout/pin11_gpio17#

**I2C - Inter-Integrated Circuit**

I2C is a low-speed two-wire serial protocol to connect devices using the I2C standard.  This means that we can talk to multiple devices on a single I2C connection as each device is unique and discoverable by the user and the computer using [Linux commands](https://www.tomshardware.com/reviews/raspberry-pi-command-line-commands,6159.html) such as i2cdetect.

I2C has two connections: SDA and SCL. They work by sending data to and from the SDA connection, with the speed controlled via the SCL pin.

**SPI - Serial Peripheral Interface**

SPI is another protocol for connecting compatible devices to your Raspberry Pi. It is similar to I2C in that there is a master-slave relationship between the Raspberry Pi and the devices connected to it.

Typically SPI is used to send data over short distances between microcontrollers and components such as shift registers, sensors and even an SD card.

**Ground (gnd)**

Gound Pin provides 0V as a basis for electricity handled in electronic circuits.

For example, when a device that communicates via SPI or I2C is connected externally, Hi and Low are determined based on 0 V, which is the reference, by connecting GND. Therefore, if GND is not connected, Hi and Low cannot be judged correctly and communication cannot be performed properly.

**5v**

The 5v pins give direct access to the 5v supply coming from your mains adaptor, less power than used by the Raspberry Pi itself. Especially if 5v and 3.3v are connected together, yes we once did that and know that it renders a Raspberry Pi dead in seconds.

**3v3**

The 3v pin is there to offer a stable 3.3v supply to power components and to test LEDs.
