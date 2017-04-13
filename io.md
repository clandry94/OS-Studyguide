# Input / Output

## Intro

**Device registers can be accessed through**
1. Port I/O  
2. Memory-Mapped I/O  
3. Hybrid of Port and Memory-Mapped I/O  
4. DMA (Direct Memory Access)

**How is I/O performed?**  
1. Programmed I/O  
2. Interrupt-Driven I/O  
3. I/O using DMA  

### Disk arm scheduling algorithms   
**1. First-Come First-Serve (FCFS)**  
Self-explanatory  
**2. Shortest Seek First (SSF) / Shortest Seek Time First (SSTF)**  
Selects the request with the minimum seek time from the current head position  
**3. SCAN Scheduling / Elevator Algorithm**  
Disk arm starts at one end of the disk and moves to the other end, servicing requests as it reaches each cylinder, until it gets to the other end of the disk. At that end, direction of head is reversed, servicing continues. Continuous scanning back and forth.  
**4. Circular SCAN (C-SCAN) / Modified 
Elevator**  
Services along the way like SCAN, but once it reaches the end it just returns back without servicing  
**5. LOOK and C-LOOK**  
Arm goes only as far as the final request in each direction, then reverses immediately without going all thew ay to the end of the disk

## Categories of I/O Devices
**Charcater-Stream or Block**  

* Character-stream device transfers bytes one by one  
* Block device transfers block of bytes as a unit  

Type | Device
------------ | -------------
Character | Printer, network interface, mice, etc (non disk like)
Block | Disk

**Sequential or Random-Access**  

* Sequential Transfers data in a fixed order determined by the device  
* Random access can seek any of the available data storage locations

Type | Device
------------ | -------------
Sequential | Tape drives
Random Access | Hard Disk

**Sharable or Dedicated**  

* Sharable can be used concurrently by several processes or threads  
* Dedicated cannot

Type | Device
------------ | -------------
Sharable | HDD
Dedicated | CD/DVD recorder, printer


**Synchronous or Asychronous**  

* Synchronous does data transfers with predictable response times  
* Asychronous exhibits irregular or unpredictable response times  

## Device Controller  
A collection of electronics that can operate a port, a bus, or a device  

Example: Serial port controller is a simple device controller. It consists of a single chip that controls the signal wires on the port. Disks have controllers as well. Controllers have microcode and a processor to carry out tasks like bad-sector mapping, prefetching, buffering, and caching  
  
**How a controller works**  
Low level interface between controller and device.  
For example, disk might have 256 sectors of 512 bytes per track. When we read, a serial bit stream comes off the device with a preamble, then 512 bytes ina  sector and finally an ECC checksum. The controller converts the serial bit stream into a block of bytes and corrects errors. The block of bytes is assembled in a buffer inside the controller, checked against the checksum, then copied to main memory. The OS just gives a few parameters like which blocks to read and where to store it in memory. 

## How can the processor give commands and data to a controller to accomplish an I/O transfer?

The controller has several control and status registers internally to aid communication with the CPU. To write to these registers, the OS can tell the device to accept data it delivers, switch itself on/on, or perform actions. Similarily, the OS can read from the registers to now what is going on with the device (i.e. its state, e.g. if it is ready for more commands etc)  

The device also usually has a data buffer that the OS can read or write. For example, to write pixels to a display we buffer them in video RAM, which is just a data buffer.  

**How the device and control registers can be accessed**  
*Port I/O*  
Each control register has an I/O port number, usually an 8 or 16 bit integer. With I/O instructions like ```IN``` and ```OUT``` we can write to those registers with the CPU.  

This scheme provides different address spaces for memory and I/O (figure a)  

*Memory-Mapped I/O*  
All the control registers in this scheme are mapped into the memory space (figure b)  

Each register has a unique memory address that is reserved. Usually the assigned address is at the top of the address space.  

The hybrid scheme (figure c) has memory-mapped I/O data buffers and separate I/O ports for the control registers. The Pentium architecture uses this style with addresses 640K to 1M reserved for data buffers in IBM PC computers, in addition to I/O ports 0 to 64K

![portio](http://imgur.com/xXUk7K7.png)  

*How does this all work?*  
In all these cases, when the CPU wants a word from memory or an I/O port, it puts the address it needs on the bus' address lines and then asserts a ```READ``` signal on the bus' control line. Then, a second signal line tells whether I/O or memory space is needed.   

* If memory space, then memory responds to the request  
* If I/O space, then I/O device responds to the request  
* If only memory space exists, then every memory module and I/O device compares the address lines to the range of addresses it services. If the address falls in its range, it responds to the request  

There will never be conflict because no address is assigned to I/O or memory  

**DMA (Direct Memory Access)**  
The CPU could request data from an I/O controller one byte at a time, but that wastes CPU time and is not efficient. using DMA we can avoid this problem.  

DMA controllers have several registers that can be read or written by the CPU. 

| Register | Purpose |
| :---: | :---: |
| Memory Address | Specify I/O port to use|
| Byte Count | Number of bytes to transfer | 
| >= 1 Control register |  Direction of transfer (read or write), transfer unit (byte at a time or word at a time) |  

Sometimes the controller is built into the disk controller or other controllers, but that would require a DMA for each device. The most common approach is to have a single DMA controller on the motherboard, which regulates transfers to multiple devices, usually concurrently. 

*How DMA Works*  
![How DMA Works](http://imgur.com/QFLhiwS.png)  

*DMA Operation Modes*  
1. Word-at-a-time Mode (Cycle Stealing): DMA Controller requests for the transfer of one word at a time  
2. Block Mode (burst mode): DMA Controller tells the device to acquire the bus, issue a series of transfers, then release the bus  


### Ways of Perofrming I/O
**Programmed I/O**  
Continuously poll the device with the CPU to see if it is ready to accept a request. Also known as *Polling or busy waiting*  

Advantage | Disadvantage
------------ | -------------
Simple | CPU is tied up the full time until the I/O is done

**Interrupt-Driven I/O**  

See the figure  

Advantage | Disadvantage
------------ | -------------
 CPU doesn't have to spend the entire time waiting until I/O is done| Interrupts take time so this waste some CPU time 
 
 **I/O Using DMA**  
 
 i.e., DMA is programmed I/O but with the DMA controller doing all the work instead of the CPU  
 
 Advantage | Disadvantage
------------ | -------------
Number of interrupts is reduced | The DMA controller is slower than the main CPU. If the DMA controller can't drive the device at full speed, then the CPU usually has nothing to do until the DMA interrupt arrives. In this case, an interrupt-driven I/O or programmed I/O might be better  

![interrupt driven i/o](http://imgur.com/JLmaj1z.png)

### I/O Software Layers  

![io sw layers](http://imgur.com/ZlE0n3o.png)  

### How interrupts are handles  

| Interrupt steps |
| :-- | 
| 1. Save any registers (including the PSW) that weren't saved by the interrupt hardware |
| 2. Setup the context for the interrupt service procedure. Doing this might involve setting up the TLB, MMU, and page table |
| 3. Setup stack for interrupt service procedure | 
| 4. Acknowledge the interrupt controller. |
| 5. Copy registers from where they were saved (probably the stack) to the process table |
| 6. Run the interrupt service procedure. It will extract info from the interrupting device controller's registers | 
| 7. Choose which process to run next. If the interrupt has caused high-priority process that was blocked to become ready, then run this one | 
| 8. Setup the MMU context for the prcess to run next, Some TLB setup might be needed | 
| 9. Load the new process' registers, including the PSW |
| 10. Run the new process |   

### Device Drives  

Each I/O devices attached to the computer needs specific code controlling it. This is called the *device driver* and is written by the device's manufacturer and delivered with the device.  

Each driver normally handles one device type or a class of related devices.  

In some computers, the OS is a single binary program that contains all of the drivers needed built in. However, they are usually dynamically loaded.  

| Functions of a device driver |
| :-- |
| 1. Translate a request through the device-independent standard interface (open, close, read, write) | 
| 2. Initialize hardware and shut it down cleanly | 
| 3. Power management and logging | 

### Device-Independent I/O Software  

Basic function of the device-independent software is to perform I/O functions that are common to all devices and to provide a uniform interface to the user-level software.  

The following are usually done in device-independent software:  

| Type | Description | Problems |
| :--  | :-- | :-- |
| Non-Buffered Systems | Consider a process reading data from a modem. The process does a ```read``` system call and blocks waiting for one character. Each arriving character causes an interrupt. The interrupt service procedure hands the char to the user process and unblocks it. This repeats for all characters | The user process has to be started for every incoming char. Running a process many times for short runs is inefficient, so this design is bad. | 
|Buffer in User Space|User process provides an n-character buffer in user space and does a read of n characters. The interrupt service prcoedure puts the incoming characters in this buffer until it fills up, then wakes up the user process. | What happens if the buffer is paged out when a character arrives? The buffer could be locked in memory, but if lots of processes do this, the performance will degrade |
| Buffering in Kernel Space | Have a buffer in the kernel space and have the interrupt handler put the characters there. When the buffer is full, the page with the user buffer is brought in, if needed, and the buffer is copied there. | What happens if the characters arrive while the page with the user buffer is being brought in from the disk? Since the buffer is full, there is no place to put them| 
|Double buffering |A way to solve the above problems is to have a second kernel buffer. After the first one fills up, but before it has been emptied, the second buffer is used. When the second buffer fills up, the page will have been copied and we can copy it to user space. While the second buffer is being copied, we can then fill the first up again | Similar as buffering in kernel space, but less likely to occur due to the extra space|

![Types of buffers](http://imgur.com/8QIzTDd.png)

**Why buffering is needed**  
*1. To cope with the speed mismatch between the producer and consumer of a data stream*  
Suppose that a file is being received via a modem for storage on the hard disk. The modem is about a thousand times slower than the disk, so a buffer is created in main memory to accumulate a few bytes from the modem. When an entire buffer has arrive, we can write to the disk  
*2. To adapt between devices that have different data-transfer sizes*  
Such a disparities are common in networking, where buffers are used widely for fragmentation and reassembly of messages. At the sending side, a large mssage is fragmented into many small packets. The packets are sent, and the receiving end places them in a reassembly buffer to form an image of the source data.  

### User-Space I/O Software

Most of the I/O software is in the OS, but some of it is libraries linked together with user programs, and sometimes programs running outside of the kernel. Sys calls, such as the I/O sys calls, are normally made by library procedures. When a C program contains the call  
```count = write(fd, buffer, nbytes);```  
the library procedure ```write``` will be linked with the program and contained in the binary program present in memory at run time. The collection of these lib procedures is part of the I/O system  

### Summary of the I/O System  

![IO summary](http://imgur.com/ubMAv7E.png)



