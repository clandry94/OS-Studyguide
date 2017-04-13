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
- Character-stream device transfers bytes one by one  
- Block device transfers block of bytes as a unit  

Type | Device
------------ | -------------
Character | Printer
Block | Disk