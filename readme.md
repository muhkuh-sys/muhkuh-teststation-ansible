# How to use this

First copy the 64bit Ubuntu Raspberry image from [here](https://ubuntu.com/download/raspberry-pi) to a memory card.
There are simple GUI tools for this task:

* [Ubuntu tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card)
* [balenaEtcher](https://www.balena.io/etcher/)

If you would like to use the command line, make sure that any partitions on the card are ummounted before writing the image.
Replace /dev/SDCARD with the device of the memory card.

```bash
xzcat ubuntu-20.04-preinstalled-server-arm64+raspi.img.xz | sudo dd bs=4M of=/dev/SDCARD
```

Example after inserting the card:

```bash
huhn@asteroid:~# dmesg | tail
[  398.923366] sd 3:0:0:3: [sdf] 15286272 512-byte logical blocks: (7.83 GB/7.29 GiB)
[  398.924362] sd 3:0:0:3: [sdf] Write Protect is off
[  398.924370] sd 3:0:0:3: [sdf] Mode Sense: 21 00 00 00
[  398.925139] sd 3:0:0:3: [sdf] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
[  398.929437] sd 3:0:0:1: [sdd] Attached SCSI removable disk
[  398.935507] sd 3:0:0:2: [sde] Attached SCSI removable disk
[  398.936209] sd 3:0:0:0: [sdc] Attached SCSI removable disk
[  398.949968]  sdf: sdf1 sdf2
[  398.952600] sd 3:0:0:3: [sdf] Attached SCSI removable disk
[  399.974403] EXT4-fs (sdf2): mounted filesystem with ordered data mode. Opts: (null)
huhn@asteroid:~# umount /dev/sdf1
huhn@asteroid:~# umount /dev/sdf2
huhn@asteroid:~# xzcat ubuntu-20.04-preinstalled-server-arm64+raspi.img.xz | sudo dd bs=4M of=/dev/sdf
```

Then insert the card into the Raspberry, connect it to the network and plug in the power. After a few seconds the green activity led starts goind on and off.
The first boot will take a while. Ping the board to see when boot process finished. The initial hostname of the board is "ubuntu".

```bash
huhn@asteroid:~# ping ubuntu
PING ubuntu.Speedport (192.168.2.119) 56(84) bytes of data.
64 bytes from ubuntu (192.168.2.119): icmp_seq=1 ttl=64 time=0.178 ms
64 bytes from ubuntu (192.168.2.119): icmp_seq=2 ttl=64 time=0.230 ms
^C
--- ubuntu.Speedport ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.178/0.204/0.230/0.026 ms
```

The connect with SSH and change the initial password from "ubuntu" to "muhkuh":

```bash
ssh ubuntu@ubuntu
...TODO
```

Then run the playbook:

```bash
huhn@asteroid:~# ansible-playbook raspberry-setup.yml

PLAY [MuhkuhTestStations] *****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************
ok: [ubuntu]

TASK [disable unneeded hardware] **********************************************************************************************************************************************************************************
changed: [ubuntu] => (item=dtoverlay=disable-wifi)
changed: [ubuntu] => (item=dtoverlay=disable-bt)
changed: [ubuntu] => (item=hdmi_blanking=2)

TASK [blacklist unwanted modules] *********************************************************************************************************************************************************************************
changed: [ubuntu] => (item=brcmfmac)
changed: [ubuntu] => (item=brcmutil)
changed: [ubuntu] => (item=bluetooth)
changed: [ubuntu] => (item=btsdio)

TASK [uninstall unwanted packets] *********************************************************************************************************************************************************************************
changed: [ubuntu]

TASK [Disable the apt-daily timer] ********************************************************************************************************************************************************************************
changed: [ubuntu]

TASK [limit the journal] ******************************************************************************************************************************************************************************************
changed: [ubuntu]

TASK [Install the raspberry-ready-led] ****************************************************************************************************************************************************************************
changed: [ubuntu]

TASK [Install the raspberry-ready-led service file] ***************************************************************************************************************************************************************
changed: [ubuntu]

TASK [Enable the raspberry-ready-led service] *********************************************************************************************************************************************************************
changed: [ubuntu]

PLAY RECAP ********************************************************************************************************************************************************************************************************
ubuntu              : ok=9    changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

ansible-playbook raspberry-setup.yml  45,93s user 4,97s system 28% cpu 3:00,75 total
```
