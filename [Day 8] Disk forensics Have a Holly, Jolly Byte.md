The drama unfolds as the Best Festival Company and AntarctiCrafts merger wraps up! Tracy McGreedy, 
now a grumpy regional manager, secretly plans sabotage. His sidekick, Van Sprinkles, hesitantly kicks off a cyber attack – but guess what? Van Sprinkles is having second thoughts and helps McSkidy's team bust McGreedy's evil scheme!

**Task Objectives**

Use FTK Imager to track down and piece together McGreedy's deleted digital breadcrumbs, exposing his evil scheme. Learn how to perform the following with FTK Imager:

- Analyse digital artefacts and evidence.
- Recover deleted digital artefacts and evidence.
- Verify the integrity of a drive/image used as evidence.

Join McSkidy, Forensic McBlue, and the team in this digital forensic journey! Expose the corporate conspiracy by navigating through cyber clues and unravelling McGreedy's dastardly digital deeds.

**AntarctiCrafts Parking Lot & The Unsuspecting Frostling**

Van Sprinkles, wrestling with his conscience, scatters USB drives loaded with malware. Little do the AntarctiCrafts employees know, a storm's brewing in their network.

Van Jolly, shivering and clueless, finds a USB drive in the parking lot. Little does she know that plugging it in will unleash a digital disaster crafted by the vengeful McGreedy. But this is exactly what she does.

Upon reaching her desk, she immediately plugs in the USB drive.

**An Anonymous Tip and Confrontation With Van Jolly**

Amidst the digital chaos of notifications and alerts from the cyber attack, McSkidy gets a cryptic email. It's Van Sprinkles, ridden with guilt, nudging her towards exposing McGreedy without blowing his own cover.

McSkidy, with a USB in hand, reveals to Van Jolly the true nature of her innocent find – a tool for digital destruction! Shock and disbelief play across Van Jolly's face as McSkidy explains the gravity of the situation and the digital pandemonium unleashed upon their network by the insidious device.

McSkidy, Forensic McBlue and the team, having confiscated the USB drive from Van Jolly, dive into a digital forensic adventure to unravel a web of deception hidden in the device. Every line of code has a story. McSkidy and the team piece it together, inching closer to the shadow in their network.

**Investigating the Malicious USB Flash Drive**

In our scenario, the write-protected USB drive that McSkidy confiscated will automatically be attached to the VM upon startup. The VM mounts an emulated USB flash drive, "\\PHYSICALDRIVE2 - Microsoft Virtual Disk [1GB SCSI]" in read-only mode to replicate the scenario where a physical drive, connected to a write blocker, is attached to an actual machine for forensic analysis.

When applied in the real world, a forensics lab analyst will first note the suspect drive/forensic artefact details, such as the vendor/manufacturer and hardware ID, and then mount it with a write-blocking device to prevent accidental data tampering during forensic analysis.

**FTK Imager**

FTK Imager is a forensics tool that allows forensic specialists to acquire computer data and perform analysis without affecting the original evidence, preserving its authenticity, integrity, and validity for presentation during a trial in a court of law.

**Working With FTK Imager**

Open FTK Imager and navigate to ```File > Add Evidence Item```, select ```Physical Drive``` in the pop-up window, then choose our emulated USB drive "\\PHYSICALDRIVE2 - Microsoft Virtual Disk [1GB SCSI]" to proceed.

![ddddd](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/7ddfebd6-47e7-4ab3-b7be-774317a37f38)

![ddddd2](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/28d4d39c-92b5-4bb2-953f-8bf96b18714b)

**FTK Imager: User Interface (UI)**


FTK Imager's interface is intuitive and user-friendly. It displays an "x" icon next to deleted files and includes key UI components vital for its functionality. These components are:

- Evidence Tree pane: Displays a hierarchical view of the added evidence sources such as hard drives, flash drives, and forensic image files.
- File List pane: Displays a list of files and folders contained in the selected directory from the evidence tree pane.
- Viewer pane: Displays the content of selected files in either the evidence tree pane or the file list pane.

![evidence](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/82637b4c-f273-4b4f-b493-c5e4e00f02eb)


