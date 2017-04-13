# OS Exam 2
## File Systems
### File Structure
1. Unstructure Stream (Pile)
	* Literally no organized
	* Collected as arrived
	* Linear search
	* Accumulates mass data

	
	
2. Sequential
	* Fixed format
	* Same length records
3. Index Sequential
	* Inode structures (see later)
4. Direct or Hashed
	* Key-Value pairs
	* Constant lookup
	* Access by address

| ![](http://imgur.com/TGsQM4I.png)| ![](http://imgur.com/PiVa4yl.png)| ![](http://imgur.com/K3eDPUK.png) |
|:---:|:---:|:---:|
| Unstructured | Sequential | Direct/Hashed |
	
### File Types
1. Regular Files
	* Contains user info. Can be ASCII or binary
2. Directories
	* System files for maintaining the structure 
3. Block Special Files / Block Devices Files
	* Models _disks_

### Directories
**1. Single-Level Directory Systems**  

Advantages | Disadvantages
------------ | -------------
Simple | Does not work well with multiple users. For example, if someone uses the same name for a file overwrite will occur
Files can be found quickly, only one level |
	
**2. Two-Level Directory Systems**

Advantages | Disadvantages
------------ | -------------
 No problem of using same file name among differnet users  | Some kind of user login procedure is needed to know which directory to search in
	
**3. Hierarchical / Tree Directory Systems**   
Tree of directories 

| ![](http://imgur.com/HK6NSLD.png)| ![](http://imgur.com/6ej1jj0.png)| ![](http://imgur.com/5yM2HDB.png) |
|:---:|:---:|:---:|
| Single-Level | Two-Level | Hierarchical/Tree |
### Implementing Files

**1. Contiguous / Linear / Sequential Allocation**  
Simplest allocation was to store each file as linear disk blocks. Thus, disk with 1-KB blocks, a 50-KB file will need 50 consecutive blocks.

Advantages | Disadvantages
------------ | -------------
Simple to implement since we only need to keep track of the disk address of first blocks and # of blocks in file. Only simple addition to find other blocks | Fragmentation is a major issue. Once a file is removed, blocks are freed and small empty slots can accumulate.  
Excellent read performance since only a seek is needed so data comes in at full disk bandwidth  | 

Best used with CD-ROMs and other media types where file sizes are knowin in advancea and will never change with use  


**2. Linked-List Allocation**  
Use a linked list to keep track of all of the blocks. First *word* of each block is a pointer to the next one, the rest of the block is for data. Thus 1 byte for pointer, 3 blocks for data  

Advantages | Disadvantages
------------ | -------------
Every disk block can be found and no space is lost due to fragmentation aside from a small amount of internal fragmentation on the last block if it isn't filled up completely. | Reading a file sequentially is extremely fast, but random access is really slow. This is because we have to traverse n - 1 blocks to get the random access block we want. This is too many reads. 
The directory entry only needs to store the disk address of the first block since everything else can be found from there | The amount of data in each block is only 3 bytes and not a power of 2. This isn't as efficient as having 4 bytes for the block since many data structures in the blocks are powers of 2 in size. To read a full block size then, we may need to concatenate blocks together which is more overhead.

 

**3. Linked-List Allocation Using a Table in Memory**  
We can avoid the disadvantages of the first two methods by taking the pointer from each block and holding it in a table in memory. This table is called the **FAT** (File Allocation Table)  

Advantages | Disadvantages
------------ | -------------
Entire block is available for data now | Primary problem with this is that a huge table must be in memory at all times for this to work. If a 20-GB disk is used and 1-KB block size, 20 million entries are needed to hold references to each block. With each entry being 3-bytes (4-bytes is preferable for speed of lookup) we would need 60-80 MB of memory just for the FAT
Random access is much faster since we only need to traverse each memory entry. This is much, much faster than traversing disk blocks. (No Disk references required) | The table could be paged, but then virtual memory would stil take up a lot of space and extra paging traffic may not be worth it.  
Like the linked list method, we only need to keep a single integer in the directory entry and we can locate all of the blocks, no matter the size |

**4. Inodes (Index-nodes)**  
We can create data structures called inodes (index-node) that list attributes and disk addresses of the file blocks.  

Things contained in an inode:  
* File size
* Creation time
* Last access time
* Last modification
* Owner
* Group
* Protection Information
* Count of the number of directory entries that point to the inode

*Advantages*  
We only need to have the table for a corresponding file in memory instead of the entire table. So, if each inode occupies n bytes and max k files can be open, only nk bytes needed to hold all of that info, which can be reserved in advance. Also, if a file uses single-triple indirect blocks, we don't have to have those in memory unless it is necessary.   

**Maximum File Size for inode scheme**  
Let the block numbers be 4 bytes in length, and block size is 1-KB. Then, the number of addressable blocks will be:  
* Direct-blocks = 12
* Single indirect = 1024 / 4 = 256  blocks
* Double indirect = (256 * 256) = 65536 blocks
* Triple indriect = (256 * 256 * 256) = 16777216 blocks
Therefore, max file size is:  
(12 + 256 + 65536 + 16777216) * 1-KB = 16843020-KB = 16-GB  

 

| ![linear](http://imgur.com/sZlf6bg.png) | ![linked](http://imgur.com/XWnrtxh.png)| ![](http://imgur.com/hzO5tC3.png)| ![linked](http://imgur.com/ULyrZVl.png) |
|:---:|:---:|:---:|:---:|
| Contiguous | Linked List | FAT | Inodes |

### Implementin Directories in UNIX (using inodes)
A directory in unix has two fields:
* The file name (14 bytes)
* The inode number (2 bytes)

Maximum of 2^16 = 64K files per file system


### File System Implemention & Looking up a file

#### Lookup
Suppose we want to find /usr/d/mbox  
First we find the root directory. In UNIX, inode for root is located at a fixed place on the disk. See graphic for the rest 

| ![fs implementation](http://imgur.com/ZNuuWRR.png) | ![lookup](http://imgur.com/ypJ3lyZ.png)| ![fs_layout](http://imgur.com/Yp3Znuz.png) |
|:---:|:---:|:---:|
| File System implementation | File Lookup | File System Layout |




### Handling Long File Names  
**1. Set a limit on file name length**
	* Usually 255 characters
	
	Advantages | Disadvantages
------------ | -------------
Simple to implement | Wastes too much sapce since many files are not 255 characters in length

**2. In-Line Method**

For this method, each directory has a header portion with the length of the entry and other info like attributes. After the header the filename occurs and can be any length. The filename is terminated by a special character (usually null char). We want to start the file entries on the word boundaries, we skip words until we are there

This method isn't that good. 

Advantages | Disadvantages
------------ | -------------
none | When a file is removed, a variabl-sized gap is introduced into the directoory so the next file to be entered might not fit. Compacting would work since we are in memory though
 | Single directory entry might span multiple pages, so a page fault might occur while reading the filename


**3. Heap Method**  
Another way to handle variable-length name is to make the directory entry be fixed length and keep the file names in a heap at the end of the directory

Advantages | Disadvantages
------------ | -------------
When an entry is removed, next file entered always fits | Heap must be managed
No need for file names to start at word boundaries so no filler words needed | Page faults can still occur when processing file anmes

| ![inline](http://imgur.com/QbJBDqw.png)  | ![heap](http://imgur.com/5ZMt3x6.png)|
|:---:|:---:|
| inline | heap |


### Block Size

**Why Blocks?**
Storing a file as a contiguous sequence of bytes has a problem where if the file grows, it will need to be moved somewhere else. By storing as blocks, we can move segments relatively fast from one disk position to another. 

**Factors affecting choice of block size**
1. Block size too big? Disk space is wasted
2. Block size too small? Poor performance

**Time needed to read a block**
Reading each block has *seek* and *rotational* delay  
e.g. consider a disk with 131,072 bytes per track and rotation time of 8.33 msec and average seek time of 10 msec. Time in milliseconds to read a block of k bytes is the sum of the seek, rotational delay, and transfer times:  
10 + 4.165 + ( k / 131072) * 8.33

### Keeping Track of Free Blocks
**1. Free List Technique**  
Maintain a linked list of disk blocks used, with each block holding as many free disk block numbers as will fit.  
With 1-KB block size and 32-bit disk block number, each block on the free list holds the numbers of 255 free blocks (one slot is needed for pointer to the next block).  
Thus, a 16-GB disk needs a free list with 16,794 blocks to hold all 2^24 block numbers.  
Often free blocks are used to hold the free list. 

**2. Bitmap / Bit Vector Technique**  
A disk with n blocks requires a bitmap with n bits. Free blocks are 1's and allocated blocks are 0's or vice versa.  
A 16-GB disk has 2^24 1-KB blocks and needs 2^24 bits for the map, which requires 2048 blocks. The bitmap requires less space since it uses 1 bit per block versus 32 bits in the free list technique.  
Consider a disk where blocks 2, 3, 4, 5, 8, 9, 10, 11, 12, 13, 17, 18, 25 26, and 27 are free and the rest are allocated. The bitmap would be... ```00111100111111000110000001110000...```  

| ![freelist](http://imgur.com/utndcm8.png)  | ![bitmap](http://imgur.com/kXZkPW0.png)|
|:---:|:---:|
| Free List | Bitmap |  

### Virtual File System (VFS)

A virtual file system provides a general interface to user programs to access difference file-system-specific functions on the same machine even if the disks are different.  

Three major layers exist. 
 
| Layer | Purpose |
|:---:|:---:|
| 0 | File system interface, main access to open(), read(), and write() system calls as well as file descriptors|
| 1 | 1. Separates the file-system generic operations from their file-syste-specific implementations by defining a clean VFS interface. Several implementations for the VFS interface implemented by the respective file systems may coexist on the same machine. 
||2. VFS provides a mechanism for uniquely representing a file throughout a network. The VFS is basedon a file-representation structure, called a vnode, that contains a numerical ID for a network-wide unique file|
| 2 | VFS activates file-system-specific operations to handle local requests according to FS types. File descriptors are constructed from relevant vnodes and passed as arguments to (file-system-specific) procedures. The layer impementing the file-system type is the third layer of the architecture.  |  

![vfs](http://imgur.com/7CYlmwn.png)

### Shared Files / Symbolic Links / Hard Links

# Put a lot of effort into this

### Access Control

Access should be dependent on the identify of the user. Differet users need different access.  

The most general scheme is to use an **access control list (ACL)** specifying user names and types of access allowed for each user. When the user request sa file, the operating system checks the access list with with that file. Access is granted or denied.  

In order to make ACL smaller, there are 3 classifications for each file:  
| User Type | Description |
|:---:|:---:|
| Owner | User who created the file | 
| Group | A set of users who are sharing the file and need similar access (also called work group) |  
| Universe/Others | All other users in the system | 

![access](http://imgur.com/SFCHOr7.png)

### Disk Quotas
To stop people from using too much space, we can enforce disk quotas. Each user gets a maximum amount of space. The table gets updated for the owner of the file, no matter who adds to it.  
When a user logs in, the system checks their quota and if they exceed the soft limit for # of files or for size, they get a warning. Also, warning count =- 1. If the count gets 0, then the user can't login. 

![quota](http://imgur.com/cC0QMPC.png)

### Log Structure / Log-based / Transaction-Oriented / Journaling File Systems  
System crashes can cause inconsistencies among on-disk FS data structures such as the directory structures, free-block pointers, and free FCB (file control block e.g. inode) pointers.  
Typically, a file create can involve many structural changes to the file system. Some of which are making directory structures or modifying them, allocating inodes, allocating data blocks, and free counts for those blocks being decreased. If a crash occurs, then this process can be interrupted and bad data might appear.  

 
Log-based recovery techniques can be maintained and applied to the FS metadata after a crash occurs. NTFS and JFS use these techniques.  

``` Turn into a table ```

How to do log based: All metadata changes are written sequentially to a log. Each set of operations for performing a task is a **transaction**. Once the changes are written, they are **committed** and the system call can return, allowing future execution. Next, the changes logged are executed across the rest of the FS structures. When the changes are made, a pointer is updated to indicate that the action is complete and which are incomplete. Once the transaction is completed, it is removed from the log. 
  
Handling a crash: If a systme crashes, the log file will have >= 0 transactions. Any transactions it contains were not completed, even if they were committed. So, we complete the transaction. If a transaction was aborted by a crash, then the already executed changes in the transaction are undone and we retry again. This preserves the consistency of the file system and there should be no issues with the FS. 