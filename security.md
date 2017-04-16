# Security
## Attacks from Inside the System

### Trojan Horses

Seemingly innocent program containing code to perform unexpected and undesirable function. The code might modify, delete, and encrypt the user's files. After it encrypt it wil copy them to a place the cracker can use them later. To have the trojan horse run, the person has to execute the malicious program.

### Login Spoofing

Simulated login screen. When the user types in their password, it steals the username and password. The program then exits and the user has to login again, but they have already been compromised. 

### Logic Bombing 

Logic bomb is code that is secretly inserted into the production operating systems. If some condition the programmer creates isn't met, then the bomb goes off. 

### Trap Doors

Trap doors are pieces of code inserted into the system by a system programmer to bypass normal checks. For example they could hardcode a login "abcde" and then use any passowrd they want. 

### Buffer Overflows

Virtually all operating systems and most systems programs are written in the C programming language. Unfortunately, no C compiler does array bounds checking. This property of C leads to attacks of the following kind. In the figure, the program runs with its local variables on the stack. At some point, it calls procedure A, as we can see in b. The standard calling sequence starts out by pushing out the return address, which points to the function to return to. Say that A has a fixed-size buffer, B, to hold a file name. Suppose the user provides a filename that is 2000 characters long. When the file name is used it will fail to open, but the attacker doesn't care. When the procedure copies the file name into the buffer, the name overflows and overwrites the memory as seen in C. If the file name is long enough, it will overwrite the return address. Then, when A returns, the return address is taken from the file name instead. 

![buffer overflow](http://imgur.com/a/04c0n.png)

## Attacks From Outside the System

### Virus (Vital Information Resources Under Siege)

A virus can reproduce itself by attaching its code to another program. In addition, it can do other things to reproduce itself

### Worms

Like viruses, but self replicating rather than attaching code

## How Viruses Work

### Companion Viruses

A companion virus does not actually infect a program, but runs when the program is supposed to run. For example, on windows a desktop shortcut has symbolic links to programs. The virus can change the target of the shortcut to make it point to a virus, so when the user double clicks the icon, the virus is executed. When finished, the virus runs the original target program.


### Executable Program Viruses

One step up in complexity are viruses that infect executable programs. The simplest just overwrites the executable, those are **overwriting viruses**  

The problem with these is that it is easy to detect. So, most viruses attach themselves to programs to do their dirty work, and let the program execute normally. These are **parasitic viruses**  


![viruses](http://imgur.com/bXlH6De.png)

### Memory Resident Viruses

A memory-resident virus stays in the memory all the time, hiding at the top or in the grass among interrupt vectors, the last few hundred bytes that are generally unused.  

A typical memory-resident virus captures one of the trap or interrupt vectors by copying the contents to a scratch variable and putting its own address there, thus directing that trap or interrupt to it. The best choice is the system call trap. In that way, the virus gets to run (in kernel mode) on every system call. When it is done, it just invokes the real system call by jumping to the saved trap address.  

### Boot Sector Viruses 

A *boot sector virus*, which jumps into the MBR, first copies the true boot sector to a safe place to the disk so it can boot the OS when finished  

### Macro Viruses

Many programs, such as *Word and Excel*, allow users to write macros to group several commands that can later be executed with a single keystroke. Macros can also be attached to to menu items, so when one is selected, the macro is executed. In *Microsoft Office*, macros can contain entire programs in Visual Basic, which is a complete programming language. Since macros can be document specific, *Office* stores the macros for each document along with the document.  

Now, if the document written in *Word* contains a macro virus attached to the OPEN FILE function, then opening the document will cause the virus to execute.  

## How Virus Scanners Work  

### Matching Virus Database Records  

Every executable file on the disk is scanned looking for any of the viruses in the database of the known viruses.  

### Using Fuzzy Search  

Since minor variants of known viruses pop up all the time, a fuzzy search is needed, this way, a 3-byte change to a virus does not let it escape detection. However, a fuzzy search is slower than exact search, but they also turn up false alarms. The more viruses in the database and the broader the criteria, the more false alarms there will be. Too many, and the user gives up in disgust. However, if the virus scanner insists on a very close match, it may miss some modified viruses. Getting it right is a delicate heuristic.  

### Matching File Length  

Another way for the AV to detect file infection is to record and store on the disk the length of all files. If a file has grown since last check, it might be infected.   

### Integrity Checking 

A completely different approach to virus checking is *integrity checking*. Does this by first scanning the hard disk for viruses, then once it is convinced the disk is clean, makes a checksum for each executable file and writes the list of checksums in a directory to a file, checksum, in that directory. The next time it runs, it recomputes checksums to see if they match. An infected file will show up immediately.  

### Behavior Checking  
The AV lives in memory while the compute runs, and catches all systemc alls. It monitors all activity to catch suspicious activity. For example. no normal program should overwrite the boot sector and is almost certainly a virus. Lkewise, changing the flash ROM is highly suspicious. 