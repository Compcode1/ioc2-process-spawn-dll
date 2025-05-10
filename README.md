In this second case study of the structured IOC triage series, we examined a subtle but dangerous host-based compromise involving the abuse of the Windows utility `rundll32.exe` to execute a malicious DLL payload. Unlike more overt attacks, this technique blended into legitimate system behavior, requiring layered investigation across Windows Event Logs, EDR telemetry, registry keys, and file system artifacts.

**Attacker Narrative**

The adversary in this case didn’t kick down the door or pick the lock—they walked in through the front entrance, wearing a city uniform and driving a sanitation truck. They exploited a system-native utility (`rundll32.exe`) to deliver a weaponized DLL in plain sight, bypassing suspicion and avoiding noisy payload droppers or shellcode.

What made this especially deceptive was the attacker’s use of persistence infrastructure: the DLL was configured to auto-launch via a `Run` registry key and was also embedded into a background service. These redundant execution paths suggest an attacker seeking both reliability and stealth.

**Investigation Highlights**

- **Windows Security Event Log (4688)** captured the initial `rundll32.exe` process creation with suspicious command-line parameters.
- **EDR telemetry** corroborated the anomalous execution path and flagged the process for behavioral deviations.
- **Registry analysis** revealed persistence via the `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` key.
- **A background service** was registered to trigger the same DLL, indicating a second persistence channel.

**Cross-Layer Insight**

This case demonstrated clear pivot points between:
- **Layer 1** (Process Execution)
- **Layer 2** (Startup and Persistence)
- **Layer 3** (Background Service Infrastructure)

The attack's execution strategy moved fluidly across these layers, reinforcing the need for defenders to analyze indicators not in isolation, but as behavioral threads woven through OS subsystems.

**Analyst Reflection**

While the forensic team successfully captured disk-based indicators, **volatile memory capture was not exercised**. In this case, disk-based indicators included the suspicious DLL file itself, the rundll32.exe process creation event (Event ID 4688), a persistent Run key in the Windows registry, and a registered Windows service configured to invoke the same DLL—each leaving concrete forensic artifacts on disk.


While the forensic team successfully captured disk-based indicators—including the malicious DLL, a process creation event (4688), a persistent Run key, and a Windows service—all of which were recovered through post-mortem analysis, volatile memory capture was not exercised. Because this case involved direct DLL execution (not injection), disk-based forensics were sufficient. However, in future investigations involving stealthier execution techniques like DLL injection or process hollowing, live memory analysis will be essential for capturing in-memory artifacts and runtime behavior.

This case continues the adversary-first, structured triage model defined in the Engineered Cybersecurity Architecture. It reflects real-world analyst workflows and emphasizes investigative rigor, system-layer awareness, and attacker behavior over static IOC lists.

