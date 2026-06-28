# C++ for Security Engineering

A structured roadmap for learning C++ from a security and systems programming perspective — focused on blue team tooling, Windows internals, and detection engineering.

---

## Who this is for

Security practitioners and students who already know C++ basics (DSA, STL, memory management) and want to apply it to real security work — writing defensive tools, parsing binary formats, consuming OS telemetry, and understanding how EDRs and forensics tools work under the hood.

This is not a beginner C++ guide. It assumes you can write code and focuses entirely on security-relevant application.

---

## Why C++ in security

Most security tooling defaults to Python. C++ gives you direct access to OS APIs, lower overhead for high-throughput analysis, and the ability to write tools that operate at the same level as the software you're defending against. Understanding how offensive C++ works makes you a significantly better blue team engineer.

---

## Roadmap

### Stage 0 — Bridge the gap (1 week)

Before touching security-specific APIs, fill the gap between competitive programming C++ and systems programming C++.

**Topics**
- File I/O with raw bytes — `fstream` in binary mode, reading structs from disk
- Bitwise operations and struct packing — `#pragma pack`, unions, bit fields
- Windows API fundamentals — `HANDLE`, `DWORD`, `WINAPI` calling convention, error handling with `GetLastError()`
- Resource management — RAII, smart pointers in a Win32 context

**Exercise**
Write a program that reads a file in binary mode, maps the first 64 bytes to a struct, and prints each field. Use a PNG or any known binary format.

**Resources**
- [learncpp.com](https://learncpp.com) — chapters on file I/O and binary data
- Microsoft Docs — Win32 API fundamentals

---

### Stage 1 — Windows API for security (2–3 weeks)

This is the foundation. Everything from EDRs to forensics tools is built on these APIs.

**Topics**
- Process enumeration — `CreateToolhelp32Snapshot`, `Process32First`, `Process32Next`
- Process memory access — `OpenProcess`, `ReadProcessMemory`, `WriteProcessMemory`
- File and registry operations — `RegOpenKeyEx`, `FindFirstFile`, `FindNextFile`
- Token and privilege inspection — `OpenProcessToken`, `GetTokenInformation`
- Service enumeration — `OpenSCManager`, `EnumServicesStatus`

**Exercise**
Build a process lister that prints PID, name, and path for every running process. Then extend it to flag any process running from a temp directory.

**Resources**
- Microsoft Win32 API documentation
- [ired.team](https://ired.team) — practical Win32 examples from an offensive lens

---

### Stage 2 — Binary format parsing (2–3 weeks)

Parsing binary formats is core to malware analysis, forensics, and writing your own detection tooling.

**Topics**
- PE (Portable Executable) format — DOS header, NT headers, section table, import/export tables
- EVTX format — Windows Event Log binary structure, chunk headers, record parsing
- Network packet structures — Ethernet, IP, TCP headers in raw byte form

**Exercise**
Write a PE parser that prints the imported DLLs and functions for any given executable. Do this without using any PE parsing library — read the bytes directly.

**Resources**
- [PE Format — Microsoft Docs](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format)
- [libevtx](https://github.com/libyal/libevtx) — reference implementation for EVTX format understanding
- Your own Windows Login Forensics project — rewrite the EVTX reading layer in C++

---

### Stage 3 — ETW (Event Tracing for Windows) (2–3 weeks)

ETW is how modern EDR solutions collect telemetry. Understanding it at the C++ level is a rare and genuinely valued skill.

**Topics**
- ETW architecture — providers, sessions, consumers
- Consuming ETW events with `OpenTrace`, `ProcessTrace`, `EventCallback`
- Real-time monitoring — subscribing to security-relevant providers (Microsoft-Windows-Security-Auditing, Sysmon ETW provider)
- Parsing EVENT_RECORD and decoding TDH (Trace Data Helper) schemas

**Exercise**
Write a C++ program that subscribes to the Microsoft-Windows-Kernel-Process ETW provider and prints process creation and termination events in real time.

**Resources**
- Microsoft Docs — Event Tracing for Windows
- [ETW Explorer](https://github.com/zodiacon/EtwExplorer) — for browsing provider schemas
- Pavel Yosifovich's blog — Windows internals and ETW deep dives

---

### Stage 4 — Detection tooling (3–4 weeks)

Apply everything above to build actual blue team tools. This is where the roadmap converges into portfolio-worthy work.

**Topics**
- Hook detection — enumerating IAT/EAT hooks in loaded modules
- Memory scanning — scanning process memory for shellcode signatures or suspicious regions
- Credential access detection — monitoring LSASS access attempts via `ObRegisterCallbacks` concepts
- File integrity monitoring — using `ReadDirectoryChangesW` for real-time FS monitoring

**Exercise**
Extend your existing SentinelX project with a module that uses ETW instead of libpcap for network telemetry on Windows. Write the detection logic yourself from raw ETW events.

**Resources**
- [ired.team](https://ired.team) — detection evasion techniques, understood from the defender's side
- [cocomelonc.github.io](https://cocomelonc.github.io) — C++ security code with explanation
- Windows Internals (Russinovich) — use as reference, not linear reading

---

## Key resources

| Resource | What it covers |
|---|---|
| [learncpp.com](https://learncpp.com) | Modern C++ fundamentals, binary I/O |
| [ired.team](https://ired.team) | Win32 API, process manipulation, detection evasion |
| [cocomelonc.github.io](https://cocomelonc.github.io) | C++ malware dev tutorials, explained clearly |
| Windows Internals — Russinovich | OS internals reference |
| Microsoft Win32 Docs | API reference for everything Windows |
| [ETW Explorer](https://github.com/zodiacon/EtwExplorer) | Browse ETW provider schemas |

---

## Learning principles

**Write every line yourself.** Do not copy-paste code. If you can't explain a line, rewrite it until you can. DSA taught you to think about logic — apply the same discipline here.

**Break things intentionally.** Delete a section of your parser and see what breaks. Modify a struct field size. Understanding failure modes is as important as making things work.

**Connect to real attacks.** Every API you learn, look up how it's abused offensively. That dual understanding is what makes your defensive code accurate.

**Own your existing projects.** Before shipping SentinelX or Windows Login Forensics as portfolio pieces, be able to walk through every non-trivial section of code in an interview. If you can't explain it, rewrite it until you can.

---

## Recommended project progression

1. Binary file reader (Stage 0 exercise)
2. Process lister with anomaly flagging (Stage 1 exercise)
3. PE parser without libraries (Stage 2 exercise)
4. Real-time ETW process monitor (Stage 3 exercise)
5. SentinelX ETW module (Stage 4 — portfolio-grade)
6. C++ EVTX parser integrated into Windows Login Forensics (ongoing)

---

## Related

- [SentinelX](../SentinelX) — C++ network IDS using libpcap
- [Windows Login Forensics](../windows-login-forensics) — EVTX parser, Python/PowerShell (C++ rewrite in progress)
- [soc-blue-team-resources](../soc-blue-team-resources) — SOC and blue team learning resources
