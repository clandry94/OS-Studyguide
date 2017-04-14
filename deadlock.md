# Deadlock
## Concepts
### What is a deadlock? 
A set of processes is deadlocked if each process in the set is waiting for an event that only another process in the set can cause.  

### When does deadlock occur? Or, what are the conditions for deadlock to occur?

| Type | Description |
|:---|:---|
|1. Mutual Exclusion Condition | Each resources is concurrently assigned to *exactly one* process or is available |
|2. Hold and Wait Condition| Processes currently holding resources granted earlier can request new resources|
|3. No Preemption Condition| Resources previously granted cannot be focibly taken away from a process. They must be explicitly released by the process holding them|
|4. Circular Wait Condition| There must be a circular chain of two or more processes, each of which is waiting for a resource held by the next member of the chain|

### How to handle deadlocks? 
**1. Just ignore the problem (Ostrich Algorithm)**  
**2. Detection and Recovery**  

Deadlock Detection

| Method | Description |
|:---|:---|
|Detection with a single instance of each resource type/class | A resource-allocation graph is checked to see whether cycles exist|
|Detection with multiple instances of each resource type/class | A matrix-based algorithm is used to determine whether any unmarked processes exist| 

Deadlock Recovery

| Method | Description |
|:---|:---|
|Recovery through terminating / Killing processes| 1. Abort all deadlocked processes  2. Abort one process at a time until deadlock cycle is eliminated  3. A process *not* in the cycle can be chosen as a victim in order to release its resources|
|Recovery through resource preemption| |
|Recovery through rollback| | 

**3. Prevention**  

By Structurally negating one of the four conditions necessary to cause a deadlock

| Method | Description |
|:---|:---|
|Attacking the *mutural exclusion* condition | share all the resources |
| Attacking the *hold and wait* condition | 1. Each process is required to request and be allocated all its resources before execution begins.  2. Each process is to request resources only when it has none.  |
|Attacking the *no preemption* condition | Take resources away |
|Attacking the *circular wait* condition| 1. Each process can request resources only in an *increasing* order of enumeration.  2. No process can request a resource *lower* than what it is already holding |

**4. Dynamic Avoidance**    

Use the banker's algorithm for a single or multiple resources  

## Theory

### Deadlock  

A set of processes is deadlocked if each process in the set is waiting for an event that only another process in the set can cause.  

In most cases, the event its waiting for is the release of a resource currently being processed by another member of the set. 

### Resources  

Deadlocks occur when processes have been granted exlcusive access to devices, files, and so forth. These objects are known as *resources*. A resource can be pretty much anything from a device to information like a database record.  

Resources can be  

| Resource Type | Description |
| :---|:---|
|Preemptable Resource| One that can be taken away from the process owning it with no side effects. </br> Memory is a good example of preemptable resource. </br></br> Example: Suppose process A and process B are deadlocked because A owns shared memory and B needs it. B can take away A's memory by swapping A out and swapping B in. No deadlock occurs then.  |
|Non Preemptable resource| One that cannot be taken away from its current owner without causing the computation to fail </br></br> Example: If a process is burning a CD-ROM, taking the CD recorder away from it and giving it to another process will result in a messed up CD. CD recorders and similar devices are non-preemptable at any moment </br></br> Usually deadlocks involve nonpremptable resources since preemptable deadlocks can be mitigated easily.|

### Conditions for Deadlocks  

There are four conditions that *must* hold for a deadlock to occur

| Type | Description |
|:---|:---|
| Mutual Exclusion Condition | Each resource is either currently assigned to *exactly one* proces or is available. </br></br> At least one resource must be held in a non-sharable mode; i.e., only *one* process at a time can use the resource. If another process requests the resource, the requesting process must be delayed until the resource is released |
| Hold and Wait condition | Processes currently holding resources granted earlier can request new resources </br></br> A process must be holding at least one resource and waiting to acquire additional resource that are currently being held by other processes| 
| No preemption condition | Resources that are previously granted cannot be forcibly takena way from a process. They must be explicitly released by the process holdinf them </br></br> Resources cannot be preempted; i.e., a resource can be releaed only voluntarily after it has completed its task. |
| Circular Wait condition | There must be a circular chain of two or more processes, each waiting for a resource held by the next member of the chain </br></br> A set {P<sub>0</sub>, P<sub>1</sub>, ..., P<sub>n</sub> } of waiting processes must exist such that P<sub>0</sub> is waiting for a resource held by P<sub>1</sub>, P<sub>1</sub> is waiting for a resource held by P<sub>2</sub>, ..., P<sub>n-1</sub> is waiting for a resource held by P<sub>n</sub>, and P<sub>n</sub> is waiting for a resource held by P<sub>0</sub>. |  

For a deadlock to occur ALL FOUR ***must***  be present for a deadlock to occur. If one is absent, no deadlock is possible. 

### Deadlock Modeling / Deadlock Characterization using Resource-Allocation Graphs
 
Four conditions for the deadlock can be modeled using directed graphs.   

![resource allocation graph](http://imgur.com/nFPoq3c.png)  

In the resource allocation graph for single instance resources, if a cycle exists, then a deadlock exists.   

For resource allocation graphs of multiple instances, note the following:
1. Number of instances of a resource is depicted with dots inside of the rectangle (as seen above)  
2. A request edge points only to the rectangle; whereas an assignment edge must also designate the dot in the rectangle.  
3. In multiple instance resources, if a resource allocation graph does not have acycle, the system is **not** deadlocked. If there is a cycle, it **may or may not** be in a deadlocked state.   

### Methods for Handling Deadlocks  

Four strategies exists for avoiding deadlocks. 

1. Ostrich Algorithm  
2. Detection and Recovery  
3. Prevention  
4. Dynamic Avoidance  

#### Ostrich Algorithm  

This is the simplest approach to handling deadlocks. Stick your head in the sand and hope it fixes itself. This approach, however, usually does not work well for life.  

|Advantages| Disadvantages| 
|:---|:---|
|In many systems, deadlocks don't occur often, so this method is cheaper than the other options. Many operating systems like UNIX and Windows use this approach. | We might get a deadlocked state yet not even know it. The undetected deadlock will deteriorate system performance because processes cannot continue and more processes will pile up in a deadlocked state. Eventually the system will stop functioning| 

#### Deadlock Detection and Recovery 

This technique does not try to actively prevent deadlocks, instead we let them happen and then take action to correct them. For this method to work, the system must provide:

1. An algorithm that examines the state of the system to determine whether a deadlock has occured or not. 
2. An algorithm to recover from the deadlock.  

##### Deadlock Detection  

*Detection with a single instance of each resource type*  

For this system, make a resource allocation graph. If the graph has 1 or more cycles, any process part of that cycle is deadlocked. If no cycles, the system is not deadlocked.  

We can use DFS (or BFS) to detect a cycle in the graph as well as the processes part of the deadlock.  

*Deadlock detection with multiple instances of each resource type*  

When multiple copies of some resource exists, a different approach must be taken to detect the deadlocks. The below matrix-based algorithm will detect deadlock among n processes, P<sub>1</sub> to P<sub>n</sub>.  

Let,  
***m*** be the number of resource types/classes  
***E*** be the existing resource vector, it gives the total number of instances of *each* resource in existence.  
***E<sub>i</sub>*** gives the total number of instances of resrource of *i* class.  
***A*** is the available resource vector, it gives the number of instances currently available for each easource  
***A<sub>i</sub>***  gives the number of instances of resource *i*  currently available.  
***C*** is the current allocation matrix. The i-th row of C tells how many instances of each resource class P<sub>i</sub> currently holds. Thus, C<sub>ij</sub> is the number of instances of resource *j* that are held by process *i*  
***R*** is the request matrix. The i-th row of R tells how many instances of each resource class P<sub>i</sub> needs. Thus R<sub>ij</sub> is the number of instances of resource j that P<sub>i</sub> wants.  

There is an invariant for the four data structures. A resource is either allocated or it is available. This means that  

 ∑C<sub>ij</sub> + A<sub>j</sub> = E<sub>j</sub>  
 
 In other words, if we add up all instances of resource j that have been allocated with all instances available, then we should get all of the resources that exist
 
 ![resourcegraph](http://imgur.com/KN2eWAK.png)  
 
 This algorithm compares vectors. Let the relation A <= B on two vectors A and B mean that each element of A is less than or equal to the corresponding element of B. Thus, A <= B holds if A<sub>i</sub> <= B<sub>i</sub> for 1 <= i <= m  
 
 Each process begins unmarked, as the algorithm goes on, the processes are marked. This indicates taht they are complete and not deadlocked. When the algorithm terminates, any unmarked processes are known to be deadlocked.  
 
 The deadlock detection algorithm can be given now, as follows:  
 
| Deadlock detection algorithm| 
|:---|
|1. Look for an unmarked process, P<sub>i</sub>, for which the i-th row of R is less than or equal to A |
|2. If such a process if found, add the ith row of C to A, mark the process, adn go back to step 1.  |
|3. If no such process exists, the algorithm terminates. |  
 
 When the algorithm finishes, all the unmarked processes, if any, are deadlocked.   
 
 
 *When to look for deadlocks*  
 
 1. Check every time a request is made 

 | Advantage | Disadvantage | 
 |:---:|:---:|
 |Detects deadlocks as early as possible |  High CPU time and overhead |
 
 2. Check every k minutes 
 3. Check only when the CPU utilization has dropped below a certain threshold  
 	* The eason we can consider the CPU utilizaiton is that if enough processes deadlock, there a few runnable procces, so we will likely find one when the CPU utilization is low.  

 	
##### Deadlock Recovery  

*Recovery through termination / Killing processes*  

1. Abort all deadlocked processes  
	* **Disadvantage**: This is *expensive*. The deadlocked processes may have computed for a long time, and these partial computations will be lost and have to be recomputed  
2. Abort one process at a time until the deadlock cycle is terminated
	* **Disadvantage**: The *overhead* here is high. After each process is aborted, the deadlock detection algorithm must run again.  
3. A process *not* in the cycle can be chosen as a victim to release more resources  
	* One process might hold a printer and want a plotter, with another process holding a plotter wanting a printer. These two are deadlocked. A third process may hold another identical printer and another identical plotter and be happilty running. Killing the third process will release resources and break the deadlock in the first two.  

*Recovery Through Resource Preemption*  

We can temporarily take away resources from its owner and give it to someone else. This one is highly dependent on the nature of the resource. Recovering this way usually does not work.  

*Recovery Through Rollback*  

If we preempty a resource from a process, what do we do with the process? It clearly cannot continue, so we must roll it back to some safe state. It is difficult to determine safe states, so the simplest solution is  total rollback. Abort the process and restart it.  

### Deadlock Prevention  

We provide a set of methods that ensure at least one of the four conditions for a deadlock cannot hold. These usually constrain how requests for resources can be made. 

**Attacking the *Mutual Exclusion* Condition**  

if no resource were ever assigned *exclusively* to a single process, deadlocks would not occur. Sharable resources don't require mutex, so they can't be the cause of deadlocks. e.g. Read-only files.  

Some resources, like CD-ROMs and Printers are non-sharable.  

**Attacking the *Hold and Wait* Condition**  

To ensure that the hold-ant-wait never occurs in the system, we must guarantee that, whenever a process requests a resource, it doesn't hold any other resources. There are two ways to do this.  

| Method | Disadvantage | 
|:---|:---| 
| Each process is required to request and be allocated all of its resources before it begins its execution| 1. Many processes do not know how many processes they will need </br> 2. Resources will not be used optimally with this approach </br></br> Example: A process reads data from an input tape, analyzes for an hour, then writes to the output tape as well as plotting results. if all resources must be made in advance, then we are wasting a lot of resource use.| 
| Each process is to request resources *only* when it has *none* | Starvation is possible here. A process that needs several popualr resources may have to wait indefinitely, because at least one of the resources that it needs is always allocated somewhere else |  

**Attacking the *No Preemption* Condition**  

This technique applied when we can easily save a resource's state and restore them later, such as CPU registers and memory space. We cannot apply this to ptiners and tape drives.  

**Attacking the *Circulat Wait* Condition**  

Impose a total ordering of all resource types and to require each process requests a resource in increasing order of enumeration  

For example, we can provide a global numbering of all resource, as seen in the figure:

![enumeration](http://imgur.com/DLhqqPR.png)  

With this, there are two protocols that can be followed:  

*1. Each process can request resources only in an increasing order of enumeration*  

A process may first request a scanner (2) and then a tape drive (4), but it can't get a plotter (3), then a scanner (2)  

Proof that with this fule, the resource allocation graph can't have cycles:  

Let us see why this is true for the case of two processes. From the figure, we can get a deadlock only if A requests a resource j and B requests resource i. Assuming i and j are distinct resources, they will have different numbers. Thus, if i > j, then A is not allowed to request j. If i < j, then B cannot request i. Either way, deadlock is then not possible.  

*2. No process can request a resource lower than what it is already holding*  

If a process holds 2 and 4, it cannot request 3 until it releases 4. However, it might releas all of its resources, then take any resource.  

*Disadvantage of attacking the circular wait condition*  

1. It may be impossilbe to find an ordering that satisfies everyone  
2. When the resources include process table slots, disk spooler space, locked db records, and other abstract resources, then the number of potential resources and different uses may be so large that no ordering could work.  


*Summary of Approaches to Deadlock Prevention*  

|Condition|Approach|
|:---|:---|
|Mutual Exclusion| Share everything |
|Hold and Wait | Request all resources initially |
|No preemption | Take resources away |
|Circular Wait| Order resources numerically|


### Deadlock Avoidance  

Avoidance requires that the OS has additional info concerning *which resources* a process will request and use during its lifetime. With the knowledge, we can determine whether a process shoudl wait or not. To deceide whether is can be satisfied or delayed, we must consider what we currently have, resources currently allocated, and future requests and releases of each process.  


**Safe and Unsafe States**  

A state is safe if it is not deadlocked and some scheduling algorithm exists where every process can run to completion even if all of them suddenly request their maxmimum number of resources immediately.  

A safe state is not a deadlocked state. A deadlocked state is not an unsafe state. Not all unsafe states are deadlocks, however, an unsafe state *may* lead to a deadlock. It depends on the other processes and if they release the resources held by them.  

**Banker's Algorithm**  

This scheduling algorithm can avoid deadlocks was formed thanks to Dijkstra.  

The name was chosen for its use in banking systems to make sure a bank never allocates cash in a way that it can't satisfy all customers.  

*Banker's Algorithm for a single resource type*  

Consider each request as it occurs, and see if granting it leads to a safe state. If it does, request is granted. Otherwise, it is postponed until later. To see if the state is safe, the banker checks to see if he has enough resources to satisfy some customer. If so, those loans are assumed to be repaid,a nd thec ustomer now closest to the limit is checked, and so on. If all loans are repaid, the state is safe and the initial request can be granted.  

*The Banker's ALgorithm for multiple resource types*  

This algorithm is just like the deadlock detection algorithm. The difference is that, deadlock detection algorithm is used to detect whether a deadlock *has already* occured; whereas the Banker's algorithm figures out whether a deadlock *will* occur. In the previous case, after the algorithm finishes, if there remain unmarked processes, we can conclude that deadlock has occured and those processes are part of the deadlock. If there unmarked processes, we can coclude that the initial state was *not* safe and deadlock *might* occur. 


| Drawbacks of the Banker's Algorithm |
|:---|
|1. Processes rarely know in advance what their maximum resource needs will be|
|2. The number of processes is not fixed, but dynamically varying as new users log in and out|
|3. Resources that were thought to be available can suddenly vanish (tape drives can break)| 

Thus, in practice, few, if any, existing systems use the bankers algorithm.  


### Two-Phase Locking  

Grab a lock on all esources, then make all updates and release the locks. no real work occurs in the first phase.  

### Non-resource Deadlocks 

Deadlocks can also occur in situations that do not involve resources at all. Semaphores are a good example. If *downs* on semaphores are done in the wrong order, deadlock can occur.

### Starvation

A problem related to deadlocks is *starvation*. In a dynamic system, requests for resources happen all the time. Some plicy is needed to make a decision about who gets which resource when. This policy, although seemingly reasonable, may lead to processes never getting serviced.  

One possible allocation algorithm is to give it to the process with the smallest file to print. This maximizes number of happy customers and seems fair. What happens if theres one huge file? That file never gets to print and it will starve to death.  

Starvation can be avoided by using a FCFS resource allocation policy. 


