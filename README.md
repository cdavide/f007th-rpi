# f007th-rpi
## Raspberry Pi: Receiving data from temperature/humidity sensors Ambient Weather F007TH with cheap RF 433MHz receiver
###### Project source can be downloaded from https://github.com/alex-konshin/f007th-rpi.git

### Author and Contributors
Alex Konshin <akonshin@gmail.com>

### Overview
This project currently contains source code of 2 executables for Raspberry Pi to receive data from temperature/humidity sensors "Ambient Weather F007TH" 
with cheap RF 433MHz receivers like [RXB6](http://www.jmrth.com/en/images/proimages/RXB6_en_v3.pdf), RX-RM-5V, etc.
- **f007th-rpi** is a demo/test program that receive data from sensors and prints it in plain text or JSON format.
- **f007th-rpi_push_to_rest** is more advanced program that sends received and decoded data to a remote server via REST API. 
When it is started with optional argument `--changes-only, -c` it sends data only when the data is actually changed.

### Files
| File | Description |
| :--- | :--- |
| `Bits.hpp` | Operations with long set of bits. It is used for storing decoded bits in Manchester decoder.|
| `F007TH.cpp` | Source code of **f007th-rpi** executable.|
| `F007TH_push_to_rest.cpp` | Source code of **f007th-rpi\_push\_to\_rest** executable. |
| `ReceivedMessage.hpp` | Container for received and decoded data. |
| `RFReceiver.cpp` | Implementation part of main class `RFReceiver` that actually receives and decodes data. |
| `RFReceiver.hpp` | Declaration part of class `RFReceiver`. |
| `SensorsData.hpp` | Contains the current list of sensors with the latest data. It is used for filtering duplicated data. |
| `makefile,*.mk,*.d` | Makefiles generated by Eclipse. They can be used for building executables outside Eclipse as well.
| `README.md` | This file. |

### How to build
There are several ways to do it. I actually cross-build this project in Eclipse on my Windows machine. 
You can look at [this good instruction about setting up Eclipse for cross-compilation](http://www.cososo.co.uk/2015/12/cross-development-using-eclipse-and-gcc-for-the-rpi/).  

##### Building on Raspberry Pi
- You need to install [libcurl](https://curl.haxx.se/libcurl/) and [pigpio](http://abyz.co.uk/rpi/pigpio/index.html) libraries.
```
sudo apt-get install libcurl4-openssl-dev
sudo apt-get install pigpio
```
- Clone sources from GitHub. The following command will create new sub-directory f007th-rpi in the current directory and download sources
```
git clone https://github.com/alex-konshin/f007th-rpi.git
```
- Build f007th-rpi
```
cd f007th-rpi
make all
cp f007th-rpi ..
cd ..
```
- Build f007th-rpi_push_to_rest
```
cd f007th-rpi_push_to_rest
make all
cp f007th-rpi_push_to_rest ..
cd ..
```
### Running f007th-rpi_push_to_rest
First of all, you need a server that accepts REST PUT requests. That server can store this data in a database. How to setup this server? 
It is out of the scope of this instruction because there are many possible solutions. I personally use [LoopBack](https://loopback.io/) with [PostgreSQL](https://www.postgresql.org/) that are run on QNAP NAS server.  
The utility sends JSON with following fileds:  
`"time", "valid", "channel", "rolling_code", "temperature", "humidity","battery_ok"`.  
The value of field `temperature` is integer number of dF ("deciFahrenheit" = 1/10 of Fahrenheit). For example, if the value is 724 then the temperature is 72.4&deg;F.   

#### Command line arguments of utility f007th-rpi_push_to_rest:
##### --send-to, -s
Parameter is REST server URL. The server should accept PUT requests.  
##### --changes-only, -c
Send only valid and changed data.
##### --log-file, -l
Parameter is a path to log file.
##### --verbose, -v
Verbose output.
##### --more_verbose, -V
More verbose output.

