---
layout: post
title: Crash Course Computing Notes
---

<br>

### Crash Course Computing Notes

###### Kevin Gu, May 2022

1. Electrically-controlled mechanical switches
   
   - relays -> vacuum tube -> transistor(semiconductor material seperating two electrodes)
   
   .  IEEE 754 - floating point number 
   
   - In 32-bit floating point number,
     
     1: the first bit is used for the sign of the number -- positive or negative
     8: used to store the exponent
     23: used to store the significand
     
     e.g.: 625.9 can be converted to 0.6259 x 10^3, .6259 is called the significand, and 3 is the exponent.
   
   <!--excerpt-->

2. ALU - arithmetic unit + logic unit
   
   - arithmetic unit: do arithmetic stuff with half-adder & full-adder or other curcuits
   
   - logic unit: perform logical operation & simple numerical tests(if a number is positive)

3. Memory
   
   - AND-OR Latch(*锁存器*, feed the output back into one of its inputs)
   
   - a group of latches is called register(*寄存器*, unit: width, number of latches)
   
   - Latches are put in matrix if width is huge
   
   - Latch address in a matrix can be written as XX column XX row
     
     multiplexer(*多路复用器*) converts from an address into something that selects the right row or column
     
     Memory location: a 256-bit memory is a 16x16 matrix, every column/row can be represented with a 4-digit binary like 1101. Column + row = 8-bit binary for a 256-bit memory.

4. CPU
   
   - instruction register + instruction address register + Control Unit: fetch/decode/execute cycle
   
   - The speed at which a CPU can carry out each step of the fetch-decode-execute cycle is called its Clock Speed. One Hertz means one cycle per second.
   
   - MMX, 3DNOW, SSE: dedicated CPU curcuits for graphics
   
   - Bus transmits data between CPU and memory, a “load from RAM” instruction might take dozens of clock cycles to complete, so CPU cache is needed to stop CPU from waiting for the data.
     
     With cache, when the CPU requests a memory location from RAM, the RAM can transmit not just one single value, but a whole block of data. *Because computer data is often arranged and processed sequentially.*
     
     When data requested in RAM is already stored in the cache like this it’s called a *cache hit* or *cache miss* otherwise.
   
   - If CPU stores calculation result in cache rather than RAM, *dirty bit* is marked to flag real data in RAM and temporary data in cache are not the same.
     
     So, if cache is full, dirty bit will be checked to see if data needs to be written back to RAM before cache cleans up.
   
   - Pipelining: use different parts of CPU at the same time to parallelize multiple processes.
   
   - Branch prediction: reduce idle time by guessing possibilities of a certain branch  to fill pipeline in advance.
   
   - Multi-core processors have several independent processing units but sharing cache so cores can work together.

5. Programming
   
   - Von Neumann Architecture: Unifying the program and data into a single shared memory.
   
   - Each assembly language instruction converts directly to a corresponding machine instruction - *a one-to-one mapping*
     
     Assembler still forces programmers to think about which registers and memory locations they will use. If you suddenly needed an extra value, you might have to change a lot of code to fit it in(address changes).
   
   - Bundles of pre-written functions are called libraries.
   
   - Complexity of algorithm is written as *big O notation*

6. Operating System
   
   - OS, being the first program to start when a computer is turned on(typically), launch all subsequent programs and have priviledges on the hardware.
   
   - Operating Systems stepped in as intermediaries between software programs and hardware peripherals by providing a software abstraction, through APIs, called device drivers
   
   - Operating Systems virtualize memory locations with Virtual Memory, programs can assume their memory always starts at address 0. The OS and CPU handle the virtual-to-physical memory remapping automatically.
   
   - kernel: core functionalities like memory management, multitasking,and dealing with I/O
   
   - Directory File: Location zero, recording where other ones are located, metadata about these files, like when they were created and last modified, who the owner is, and if it can be read, written or both. 
     But most importantly, the directory file contains where these files begin in storage, and how long they are. If we want to add a file, remove a file, change a filename, or similar, we have to update the information in the Directory File.
   
   - Flat File Systems(files in a directory) & Hierarchical File System(Directory File needs to be able to point not just to files but also other directories, including extra metadata keeping track of what's a file and what's a directory, being the top-most one, known as the Root Directory)
