---
title: -TOOL- Linux Serial
date: 2021-02-18
categories:
- Other
tags:
- Tools
---

A simple serial tool for Linux ---- picocom

1. Install
    sudo apt install picocom

2. How can get the serial driver ID?
    `dmesg | grep ttyS*`
    Then replug the serial device.
    You can see `usb 2-2.1:pl2303 converter now attached to ttyUSB0`
    Your serial device ID is **ttyUSB0**.

3. Connect the serial device
    `sudo picocom -b 115200 /dev/ttyUSB0`

4. Other instructions

```
*** Picocom commands (all prefixed by [C-a])

*** [C-x] : Exit picocom
*** [C-q] : Exit without reseting serial port
*** [C-b] : Set baudrate
*** [C-u] : Increase baudrate (baud-up)
*** [C-d] : Decrease baudrate (baud-down)
*** [C-i] : Change number of databits
*** [C-j] : Change number of stopbits
*** [C-f] : Change flow-control mode
*** [C-y] : Change parity mode
*** [C-p] : Pulse DTR
*** [C-t] : Toggle DTR
*** [C-|] : Send break
*** [C-c] : Toggle local echo
*** [C-s] : Send file
*** [C-r] : Receive file
*** [C-v] : Show port settings
*** [C-h] : Show this message
```

**Note**:
    Must enter the [ctrl + a] enter command mode first.


**Help**
[Picocom man page](https://linux.die.net/man/8/picocom)