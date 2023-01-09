---
title: "The XOR Operator"
date: "2022-12-20"
toc: false
description: "Understanding XOR and how it is used for malware analysis"
---

- Short for *exclusive OR*
- Requires only a single machine-code instruction
- Can be used by malware authors to obfuscate code
	- Also used to encrypt data so that it is only readable when executed
		- Helps malware authors avoid static analysis detection
- Reversible
	- Uses the same function to encode and decode
		- To decode something encoded with XOR, repeat the XOR function with the same key used during encoding


```
if A is 1 and B is 0, the result is 1
if A is 0 and B is 1, the result is 1
if A is 1 and B is 1, the result is 0
if A is 0 and B is 0, the result is 0
```
- A **1** is returned if exactly one of the inputs is **1**
- A **0** is returned if both inputs are the **same**

## XOR Example

- XOR on `4D 5A`
```
Converting 4D 5A to its respective binary numbers
4            D            5            A
4            13           5            10
8 4 2 1      8 4 2 1      8 4 2 1      8 4 2 1
0 1 0 0      1 1 0 1      0 1 0 1      1 0 1 0  

XOR on 4D 5A binary numbers 
 4D = 0 1 0 0 1 1 0 1
 5A = 0 1 0 1 1 0 1 0
XOR = 0 0 0 1 0 1 1 1

Converting the XOR binary number back to hexadecimal
0 0 0 1      0 1 1 1
8 4 2 1      8 4 2 1
1            7
17

Therefore, when XOR of 4D 5A occurs, we will get a hexadecimal result of 17
```


## Identifying XOR Loops
- Look for patterns of repeated XOR instructions in disassembly
	- Search for all instances of the XOR instruction within the function and follow the control flow of the code to determine if the XOR operation is being performed in a loop
		- Using the graph view in **Cutter/Ghidra** can help to visualize the flow of the code and identify any loops
		- An XOR instruction found in disassembly does not necessarily mean that it is being used for encoding and obfuscation
- Look for certain XOR instructions that could indicate encoding
	1. XOR of a register of itself
		- **e.g.** `xor eax, eax`
		- Typically used XOR to zero out a register
		- Not related to encoding
	2. XOR of a register with a constant
		-  **e.g** `xor edx, 10h`
		- Possible indication of an encoding loop with the key
	3. XOR of one register with a different register
		- **e.g** `xor eax, ecx`
		- Possible indication of an encoding loop
