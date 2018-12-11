I create `makin` to make initial malware assessment little bit easier for me, I think it's useful for others as well, It helps to reveal a debugger detection techniques used by a sample.

##### Any feedback is greatly appreciated: [@_qaz_qaz](https://twitter.com/_qaz_qaz)

#### Note: ~~Only supports x64~~ Supports x64 and x86. Works on Windows 10.

### How does it work?
`makin` opens a sample as a debuggee and injects `asho.dll`(main module renames all `dlls` before injection), `asho.dll` hooks several functions at `ntdll.dll` and `kernelbase.dll` libraries and after parameters checkings, it sends the corresponding message to the debugger (`makin.exe`).

`makin` also generates a script for IDA Pro to set breakpoints at detected APIs.

At this moment, `makin` can reveal following techniques: 

`ntdll.dll`:
* `NtClose` - ref: [The "Ultimate" Anti-Debugging Reference: 7.B.ii](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtOpenProcess` - ref: [The "Ultimate" Anti-Debugging Reference: 7.B.i](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtCreateFile` - ref: [The "Ultimate" Anti-Debugging Reference: 7.B.iii (Open itself)](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtCreateFile` - ref: [The "Ultimate" Anti-Debugging Reference: 7.B.iii (Open a driver)](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `LdrLoadDll` - ref: [The "Ultimate" Anti-Debugging Reference: 7.B.iv](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtSetDebugFilterState` - ref: [The "Ultimate" Anti-Debugging Reference: 7.D.vi](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtQueryInformationProcess` - ref: [The "Ultimate" Anti-Debugging Reference: 7.D.viii.a, 7.D.viii.b, 7.D.viii.c](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtQuerySystemInformation` - ref: [The "Ultimate" Anti-Debugging Reference: 7.E.iii](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtSetInformationThread` - ref: [The "Ultimate" Anti-Debugging Reference 7.F.iii](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtCreateUserProcess` - ref: [The "Ultimate" Anti-Debugging Reference 7.G.i](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtCreateThreadEx` - ref: [ntuery blog post](https://web.archive.org/web/20171211143522/https://ntquery.wordpress.com/2014/03/29/anti-debug-ntcreatethreadex/)
* `NtSystemDebugControl` - ref: [@waleedassar - pastebin](https://goo.gl/j4g5pV)
* `NtYieldExecution` - ref: [The "Ultimate" Anti-Debugging Reference 7.D.xiii](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `NtSetLdtEntries` - ref: [ANTI-UNPACKER TRICKS: PART ONE - 2.1.2](https://web.archive.org/web/20171215191103/http://pferrie.tripod.com/papers/unpackers21.pdf)
* `NtQueryInformationThread` - ref: [ntquery - NtQueryInformationThread](https://web.archive.org/web/20180110063515/https://ntquery.wordpress.com/2014/03/29/anti-debug-ntsetinformationthread/)
* `NtCreateDebugObject` and `NtQueryObject` - ref: [Anti-Debug NtQueryObject](https://goo.gl/krE6JM)
* `RtlAdjustPrivilege` - ref: [Using RtlAdjustPrivilege to detect debugger by insid3codeteam](https://goo.gl/m46tQe) 
* ~~~`GetWriteWatch` - ref: [Anti-debug with VirtualAlloc’s write watch](https://web.archive.org/web/20180127193503/https://codeinsecurity.wordpress.com/2018/01/24/anti-debug-with-virtualallocs-write-watch/)~~~
* `PEB->BeingDebugged` - Instead of calling `IsDebuggerPresent()`, some programs manually check the `PEB` (Process Environment Block) for the [`BeingDebugged`](https://www.aldeid.com/wiki/PEB-Process-Environment-Block/BeingDebugged) flag.
* `PEB->NtGlobalFlag` - ref: [al-khaser](https://github.com/LordNoteworthy/al-khaser/blob/bbf020b39b28bfb2561408c276fb91db528fa4ca/al-khaser/Anti%20Debug/ProcessHeap_NtGlobalFlag.cpp)
* `UserSharedData->KdDebuggerEnabled` - ref: [al-khaser - SharedUserData_KernelDebugger](https://github.com/LordNoteworthy/al-khaser/blob/master/al-khaser/Anti%20Debug/SharedUserData_KernelDebugger.cpp)

`kernelbase.dll`:
* `IsDebuggerPresent` - ref: [MSDN](https://goo.gl/cg7Fkm)
* `CheckRemoteDebuggerPresent` - ref: [MSDN](https://goo.gl/LrUdaG)
* `SetUnhandledExceptionFilter` - ref: [The "Ultimate" Anti-Debugging Reference: D.xv](https://web.archive.org/web/20171212061916/http://pferrie.host22.com/papers/antidebug.pdf)
* `RegOpenKeyExInternalW` - checks registry keys
* `RegQueryValueExW` - checks registry key values

You can add more VM checks via editing `checks.json` file, without modification of the executable

That's all for now, you can add as much as you wish :) 

External dependencies:
* [Capstone engine](http://www.capstone-engine.org/)
* [JSON for Modern C++](https://github.com/nlohmann/json)

`Note`: You can use [vcpkg](https://github.com/Microsoft/vcpkg) for external dependencies.

### TODO: 
* [DONE] ~~Use a disassembler such as [capstone](http://www.capstone-engine.org/) to hook little bit deeper and avoid simple hook checks.~~

* [DONE] ~~~At this moment, `makin` does not support child processes.~~~

* [Partial] ~~~Add more tricks.~~~~ Add more tricks.

* [DONE] ~~x86 support~~

* [DONE] ~~~hook `kernelbase.dll`~~~

* [Partial] ~~~Add anti-vm, anti-emulation tricks detection.~~~ Add more tricks.

* [DONE] ~~~Generate IDA Pro script to set BPs at detected APIs~~~

##### DEMO:

![makin_demo](https://user-images.githubusercontent.com/16405698/33871171-c6f8a156-df2a-11e7-8ffb-b9ae5c030c48.gif)
