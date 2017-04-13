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
