# Fan control for TerraMaster on Linux 

Tested with F4-220. This a direct port of the Xpenology fancontrol script by Eudean to work on OMV/Debian.
Original author: https://xpenology.com/forum/topic/14007-terramaster-f4-220-fan-control/?ct=1559481439

In this version I will use fancontrol to control fan on a TerraMaster F4-220.   
You can now choose between reading the temperature from the disks (with smartcl) or reading it from the board temperature sensor.   

If reading from the disk you will not be able to spindown the disk, as smartctl awakes the disk for reading the temperature.   
If reading from the board temperature sensor you can still spindown the disks.   


## Installation:
*1. Download or clone the repo  
``git clone https://github.com/cinzas/terramaster-fancontrol.git``

*2. Build with GCC.  

  *2.1. Build using the docker image for ease of use.

   - Pull the image:

   ``docker pull gcc``

   - Compile fancontrol.cpp (you must be in the same directory)

   ``docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp gcc gcc -o fancontrol fancontrol.cpp``


  *2.2. Compile using gcc

   ``gcc -o fancontrol fancontrol.cpp``

*3. Make sure you have smartctl and lm-sensors configured and installed  
   - Install smartctl

   ``apt install smartmontools``

   - Install lm-sensors and configure it (say yes on all questions, and make sure `coretemp` and `it87` modules are added to `/etc/modules`)

   ```
   apt install lm-sensors
   sensors-detect
   ```


*4. If you decide to use smartctl, you must create a directory containing all the drives names.  
It is hardcoded on the code to search for it in `/var/run/disks`:  
In order to guarantee they are created at boot time, you must change it on the file `fancontrol-smarctl.service` before installing the service.  
Example   
```
mkdir -p /var/run/disks 
cd /var/run/disks
touch sda sdb sdc
```

In a next release I will work on a better approach.   


*5. Run the compiled program (command descriptions in the author's thread).  
 To run fancontrol with debug, using disk temperature, and a setpoint of 37c    
 ``sudo ./fancontrol 1 37 ``   

 To run fancontrol with debug, using board sensor, and a setpoint of 40c   
 ``sudo ./fancontrol 1 40 1``   

 To see which arguments are available, just do:   
 ``sudo ./fancontrol -h``   


*6. Alternatively you can use one of the included systemd service.  
If using smartctl, don't forget to change the `fancontrol-smartctl.service` file to create the disk names before.  
```
ExecStartPre=/usr/bin/mkdir -p /var/run/disks
ExecStartPre=/usr/bin/touch /var/run/disks/sda
ExecStartPre=/usr/bin/touch /var/run/disks/sdb
ExecStartPre=/usr/bin/touch /var/run/disks/sdc
ExecStartPre=/usr/bin/touch /var/run/disks/sdd
ExecStartPre=/usr/bin/touch /var/run/disks/sde
```

  - To use the disk sensors (with smartctl), copy  `fancontrol-smarctl.service` to `/etc/systemd/system`. Also, copy the binary to `/usr/local/bin/fancontrol`.   
    Enable it at boot time with `systemctl enable fancontrol-smartctl` and start the service `systemctl start fancontrol-smartctl`.   

  - To use the disk sensors (with smartctl), copy  `fancontrol-smarctl.service` to `/etc/systemd/system`. Also, copy the binary to `/usr/local/bin/fancontrol`.   
    Enable it at boot time with `systemctl enable fancontrol-sensors` and start the service `systemctl start fancontrol-sensors`.   


# Contact
joao.amaro@gmail.com


