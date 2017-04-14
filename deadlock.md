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


