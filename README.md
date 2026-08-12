# Ncstudio-unlock-rse

**My reverse-engineering research and license/expiration mechanism analysis of NcStudio 7.0 for the NK300 CNC router machine.**

This project documents the investigation of the licensing, registration, integrity, timing, and expiration mechanisms implemented in NcStudio and its supporting components.

The analysis began with the user-mode application `NKStudio.exe` and its surrounding environment, then progressed through executable analysis, API tracing, registry and file monitoring, anti-debugging and integrity mechanisms, registration and serial-key validation, and time-based expiration checks.

The investigation eventually extended into kernel mode, where the communication between NcStudio and the NK300 CNC router machine's driver was traced and analyzed. The relevant IOCTL communication was identified, the responsible driver was located as `Wh300a.sys`, and its internal routines were disassembled and investigated. The expiration mechanism was ultimately located inside the driver, modified, and successfully tested on the target environment.

## Walkthrough

Reversing process walkthrough:

- Explored the NK300 CNC router machine's OS capabilities and permissions
- Explored the installed machine's hardware and software environment
- Changed the system time
- Blocked the application's access to time
- Launched `NKStudio.exe` with a custom date
- Searched for serial-key-related information in application files and the registry
- Searched for date-related structures and information in the registry and application files
- Monitored registry changes while executing `NKStudio.exe`
- Monitored file changes in the application directory
- Investigated the timing system by provoking system shutdowns
- Tried resetting Windows to a previously created restore point
- Tried virtualizing the software execution
- Backed up disk partitions
- Fully exported the software and its drivers
- Simulated the NK300 CNC router machine's environment on VMware and installed the software
- Tried installing Windows XP SP2 on the machine
- Tried booting using a Windows XP Live CD and minimized XP versions such as BartPE
- Tried rebuilding Windows XP SP2 Embedded from disk backups
- Tried debugging on the machine
- Disassembled `NKStudio.exe` and studied its loaded libraries on VMware
- Studied imported and exported API calls per library
- Followed the program's execution process
- Investigated the security mechanisms applied to the software
- Discovered the use of a security cookie
- Removed digital signatures
- Bypassed `IsDebuggerPresent` anti-debugging
- Applied test modifications to the executable
- Searched for DLL/EXE integrity-check routines
- Bypassed integrity checks
- Searched for expiration-check routines
- Tested modifications to expiration-check routines
- Studied the remaining-time calculation system
- Blocked `GetTickCount` and other time-related API calls
- Removed expiration-check routine calls from program startup
- Bypassed the registration dialog
- Searched for the serial-key generation algorithm and analyzed it
- Generated valid serial keys
- Investigated the cause of the inability to register the adapter
- Hooked the `DeviceIoControl` API call
- Discovered the use of drivers for serial-key validation
- Searched for the handle responsible for transmitting IOCTL codes
- Dumped and analyzed the responsible file structure
- Identified the device responsible for the communication
- Switched to kernel-mode debugging
- Stepped through the kernel and analyzed the driver-loading process
- Analyzed the relevant file structures
- Located the attached device
- Dumped and analyzed the attached device structure and driver object structure
- Identified `Wh300a.sys` as the driver responsible for processing the relevant IOCTL codes
- Disassembled `Wh300a.sys` and studied its routines and API calls
- Located the expiration-check routine inside the driver
- Patched the expiration-check routine
- Injected the modified `Wh300a.sys` driver into the NK300 CNC router machine
- Recalculated the PE checksum of the modified driver
- Re-injected the driver file
- Tested the modified installation successfully

## Conclusion

What initially appeared to be an application-level expiration mechanism turned out to involve multiple layers of protection and validation.

The expiration mechanism was ultimately located inside `Wh300a.sys`. After identifying and modifying the relevant expiration-check routine, the modified driver was deployed and tested on the target environment.

The final result was a functioning NcStudio 7.0 installation without the original expiration restriction.

This repository documents the complete reversing process, from the initial application and environment investigation through user-mode analysis, kernel-mode debugging, driver analysis, modification, and final verification.
