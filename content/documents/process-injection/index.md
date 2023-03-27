---
title: "Process Injection"
date: "2023-01-05"
toc: false
description: "Understanding process injection and API calls to look for during malware analysis"
---

- A technique often used by malware authors to avoid static detection of their code
- Injects malicious code into a trusted running process which will then execute said code (payload)

## **Steps for process injection:**
1. Malware obtains a handle to the target process, there are two ways to do this:
	1. Malware may obtain this by creating a new process
		- Commonly used API is `CreateProcess` and its variants
		- Older form of `CreateProcess` is `WinExec` for Windows 16-bit compatibility
	2. Malware may search for a process that is already running and open it to get the handle
		- Needs a combination of different API functions like: `CreateToolhelp32Snapshot`, `Process32First`, `OpenProcess` and others listed in the image below
	- In both creating or getting a process handle, the process may be suspended to prepare it for injection
2. Malware selects a certain type of data to be injected
	- **Section, Process, Code, DLL**
	- Depending on the type of injected data, there are different ways to transfer it
3. Data is transferred to the target process
	- **Section**
		- Transfers only a section of the process
		1. `NtUnmapViewOfSection`
			- API function that carves out a certain section of the process
			- The virtual address space for this section is no longer reserved and can be used for something else
		2. `NtCreateSection` and `NtMapViewOfSection`
			- API function that creates the new section with the injected data and sets access rights so that the section can be executed
	- **Process**
		- Similar to *Section* except transfers the whole process image
	- **Code**
		- Transfers pieces of code
		1. `VirtualAllocEx`
			- Do not need to carve out section of the memory(`NtUnMapViewOfSection`), just places pieces of code in memory and uses that instead
			- Used to allocate space in an external process’s memory
		2. `WriteProcessMemory`
			- Used to write data to the allocated space
4. The injected code is executed
	- **Section**
		1. `SetThreadContext`
			- Tells the system to execute the injected code
		2. `ResumeThread`
			- Resumes the target process that was suspended in the beginning

