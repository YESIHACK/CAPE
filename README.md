## CAPE: Malware Configuration And Payload Extraction

CAPE is a malware sandbox. It is derived from Cuckoo and is designed to automate the process of malware analysis with the goal of extracting payloads and configuration from malware. This allows CAPE to detect malware based on payload signatures, as well as automating many of the goals of malware reverse engineering and threat intelligence.

CAPE can detect a number of malware techniques or behaviours, as well as specific malware families, from its initial run on a sample. 

This detection may then trigger a further run with a specific package, in order to extract the malware payload and possibly its configuration, for further analysis.

CAPE works by controlling malware via a bespoke debugger and API hooks. Detection to trigger a CAPE package can be based on API or Yara signatures. The debugger uses Yara signatures or API hooks to allow breakpoints to be set on individual instructions, memory regions or function calls. Once a region of interest is reached, it can be manipulated and dumped for processing and analysis, and possibly configuration parsing.

The techniques or behaviours that CAPE detects and has packages for include:
- Process injection
    - Shellcode injection
    - DLL injection
    - Process Hollowing
    - Process Doppelganging
- Decompression of executable modules in memory
- Extraction of executable modules or shellcode in memory

Packages for these behaviours will dump the payloads being injected, extracted or decompressed for further analysis. This is often the malware payload in unpacked form.

CAPE automatically creates a process dump for each process, or, in the case of a DLL, the DLL's module image in memory. This is useful for samples packed with simple packers, where often the module image dump is fully unpacked. Yara signatures may trigger on the process dumps, possibly resulting in submission with a specific package or configuration parsing.
    
CAPE also has a package which can dynamically unpack samples that use 'hacked' (modified) UPX, very popular with malware authors. These samples are run in CAPE's debugger until their OEP (original entry point), whereupon they are dumped, fixed and their imports are automatically reconstructed, ready for analysis.
    
Currently CAPE has specific packages dumping configuration and payloads for the following malware families:
- PlugX
- EvilGrab
- Sedreco
- Cerber
- Ursnif

CAPE has config parsers/decoders for the following malware families, whose payloads are extracted by a behavioural package:
- RedLeaf
- ChChes
- HttpBrowser
- Enfal
- PoisonIvy
- TrickBot
- RCSession/Screech

CAPE also has Yara signatures to detect payloads that are extracted by a behavioural package. This list is growing, and includes:
- QtBot, ZeroT, WanaCry, Sedreco, NetTraveler, Locky, Emotet, Cerber, Ursnif, Enfal, BadRabbit, Magniber, Redsip, RCSession, Hancitor, Kronos, PetrWrap, Kovter, Azer, Petya, Dreambot, Atlas, NanoLocker, Mole, Codoso, Cryptoshield, Loki, Jaff, Dridex, RedLeaf, ChChes, EvilGrab, HttpBrowser, IcedID, Scarab
    
Many other malware families have their payloads extracted by some of the behavioural packages, with their configuration in the clear in the resulting output. Configuration parsing may then be performed on this by virtue of Yara-based detection, and config parsing based on either of CAPE's config parsing frameworks, the RATDecoders framework from malwareconfig.com and DC3-MWCP (Defense Cyber Crime Center - Malware Configuration Parser). The many parsers/decoders from malwareconfig.com are also included, comprising among many others: Sakula, Trickbot, DarkComet, PredatorPain and PoisonIvy. Thanks to Kevin Breen/TechAnarchy for this framework and parsers (https://github.com/kevthehermit/RATDecoders), and to DC3 for their framework (https://github.com/Defense-Cyber-Crime-Center/DC3-MWCP).

A utility package 'DumpOnAPI' is included which allows a module to be dumped when it calls a specific API function which can be specified in the web interface. This can be useful for quickly unpacking/dumping novel samples.

There are a number of other behavioural and malware family packages and parsers currently in the works, so watch this space.
    
Packages can be written based on API hooks, the CAPE debugger, or a combination of both.

The CAPE debugger allows four breakpoints to be set on read, write or execute of a memory address or region, as well as single-step mode. This allows fine control over malware execution until it is possible to dump the memory regions of interest, containing code or configuration data. Breakpoints can be set dynamically by package code, API hooks or Yara signatures. Thanks to the embedded distorm library the debugger can output the disassembly of instructions during single-step mode or when breakpoints are hit.

Processes, modules and memory regions can variously be dumped by CAPE through use of a simple API. These dumps can then be scanned and parsed for configuration information.

Executable modules are fixed on being dumped, and may also have their imports automatically reconstructed (based on Scylla: https://github.com/NtQuery/Scylla).

The repository containing the code for the monitor DLLs which form the basis of these packages is a distinct one: https://github.com/ctxis/capemon. This repository is organised in branches for the various packages.

CAPE is derived from spender-sandbox (https://github.com/spender-sandbox), which is derived from Cuckoo Sandbox (https://github.com/cuckoosandbox), so thanks to Brad Spengler, Claudio Guarnieri, and the countless other Cuckoo contributors without whom this work would not be possible. It also depends on Yara (https://github.com/VirusTotal/yara) from VirusTotal.

Please contribute to this project by helping create new packages for further malware families, packers, techniques or configuration parsers. Alternatively contact Kevin O'Reilly for further details of CAPE development.
