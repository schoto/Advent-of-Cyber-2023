The backup tapes have finally been recovered after the team successfully hacked the server room door. However, as fate would have it, the internal tool for recovering the backups can't seem to read them. While poring through the tool's documentation, you discover that an old version of this tool can troubleshoot problems with the backup. But the problem is, that version only runs on DOS (Disk Operating System)!

Thankfully, tucked away in the back of the IT room, covered in cobwebs, sits an old yellowing computer complete with a CRT monitor and a keyboard. With a jab of the power button, the machine beeps to life, and you are greeted with the DOS prompt.

Restoring Backups in DOS

Frost-eau, who is with you in the room, hears the beep and heads straight over to the machine. The snowman positions himself in front of it giddily. "I haven't used these things in a looong time," he says, grinning.

He hovers his hands on the keyboard, ready to type, but hesitates. He lifts his newly installed mechanical arm, looks at the fat and stubby metallic fingers, and sighs.

"You take the helm," he says, looking at you, smiling but looking embarrassed. "I'll guide you."

You insert a copy of the backup tapes into the machine and start exploring.

**Learning Objectives**

Experience how to navigate an unfamiliar legacy system.
Learn about DOS and its connection to its contemporary, the Windows Command Prompt.
Discover the significance of file signatures and magic bytes in data recovery and file system analysis.

**
Overview**
Restoring Backups in DOS
The Disk Operating System was a dominant operating system during the early days of personal computing. Microsoft tweaked a DOS variant and rebranded it as MS-DOS, which later served as the groundwork for their graphical extension, the initial version of Windows OS. The fundamentals of file management, directory structures, and command syntax in DOS have stood the test of time and can be found in the command prompt and PowerShell of modern-day Windows systems.

While the likelihood of needing to work with DOS in the real world is low, exploring this unfamiliar system can still be a valuable learning opportunity.

**DOS Cheat Sheet**

If you are familiar with the command prompt in Windows, DOS shouldn't be too much of a problem for you because their syntax and commands are the same. However, some utilities are only present on Windows and aren't available on DOS, so we have created a DOS cheat sheet below to help you in this task.

Common DOS commands and Utilities:

|CD   |Change Directory   |
|---|---|
| DIR  |Lists all files and directories in the current directory   |
| TYPE  | Displays the contents of a text file  |
| CLS  | Clear the screenProvides help information for DOS commands  |
| HELP  |Provides help information for DOS commands   |
| EDIT  | The MS-DOS Editor  |

**Exploring the past**

Let's familiarise ourselves with the commands.

Type ```CLS```, then press Enter on your keyboard to clear the screen.

Type ```DIR``` to list down the contents of the current directory. From here, you can see subdirectories and the files, along with information such as file size (in bytes), creation date, and time.

Type TYPE followed by the file name to display the contents of a file. For example, type TYPE PLAN.TXT to read its contents.

Type CD followed by the directory name to change the current directory. For example, type CD NOTES to switch to that directory, followed by DIR to list the contents. To go back to the parent directory, type CD ...

Finally, type HELP to list all the available commands.

Travelling Back in Time

Your goal for this task is to restore the AC2023.BAK file found in the root directory using the backup tool found in the C:\TOOLS\BACKUP directory. Navigate to this directory and run the command BUMASTER.EXE C:\AC2023.BAK to inspect the file.

The output says there's an error in the file's signature and tells you to check the troubleshooting notes in README.TXT.

Previously, we used the TYPE command to view the contents of the file. Another option is to use EDIT README.TXT, which will open a graphical user interface that allows you to view and edit files easily.

This will open up the MS-DOS Editor's graphical user interface and display the contents of the README.TXT file. Use the down arrow or page down keys to scroll down to the "Troubleshooting" section.

The troubleshooting section says that the issue we are having is most likely a file signature problem.

To exit the EDIT program, press ALT+F on your keyboard to open the File menu (Option+F if you are on a Mac). Next, use the arrow keys to highlight Exit, and press Enter.

**File Signature/Magic Bytes**

File signatures, commonly referred to as "magic bytes", are specific byte sequences at the beginning of a file that identify or verify its content type and format. These bytes often have corresponding ASCII characters, allowing for easier human readability when inspected. The identification process helps software applications quickly determine whether a file is in a format they can handle, aiding operational functionality and security measures.

In cyber security, file signatures are crucial for identifying file types and formats. You'll encounter them in malware analysis, incident response, network traffic inspection, web security checks, and forensics. Knowing how to work with these magic bytes can help you quickly identify malicious or suspicious activity and choose the right tools for deeper analysis.

Let's see this in action by creating our own ﻿DOS executable.

Navigate to the C:\DEV\HELLO directory. Here, you will see HELLO.C, which is a simple program that we will be compiling into a DOS executable.

Open it with the Borland Turbo C Compiler using the TC HELLO.C command. Press Alt+C (Option+C if you are on a Mac) to open the "Compile" menu and select Build All. This will start the compilation process.

Exit the Turbo C program by going to "File > Quit".

You will now see a new file in the current directory named HELLO.EXE, the executable we just compiled. Open it with EDIT HELLO.EXE. It will show us the contents of the executable in text form.

The first two characters you see, MZ, act as the magic bytes for this file. These magic bytes are an immediate identifier to any program or system trying to read the file, signalling that it's a Windows or DOS executable. A lot of programs rely on these bytes to quickly decide whether the file is of a type they can handle, which is crucial for operational functionality and security. If these bytes are incorrect or mismatched, it could lead to errors, data corruption, or potential security risks.

Now that you know about magic bytes, let's return to our main task.

Back to the Past

Open AC2023.BAK using the MS-DOS Editor and the command EDIT C:\AC2023.BAK. 

As we can see, the current bytes are set to XX. According to the troubleshooting section we've read, BUMASTER.EXE expects the magic bytes of a file to be 41 43. These are hexadecimal values, however, so we need to convert them to their ASCII representations first.

You can convert these manually using an ASCII table or online converters like this as shown below:

Go back to the MS-DOS Editor window, move your cursor to the first two characters, remove XX, and replace it with AC. Once that's done, save the file by going to "File > Save".

From here, you can run the command BUMASTER.EXE C:\AC2023.BAK again. Because the magic bytes are now fixed, the program should be able to restore the backup and give you the flag.

Congratulations!

You successfully repaired the magic bytes in the backup file, enabling the BackupMaster3000 program to restore the backup properly. With this restored backup, McSkidy and her team can fully restore the facility's systems and mount a robust defence against the ongoing attacks.

Restoring Backups in DOS

Back to the Present

"Good job!" exclaims Frost-eau, patting you on your back. He pulls the backup tape out from the computer and gives it to another elf. "Give this to McSkidy. Stat!"

As the unsuspecting elf hurries out of the room, the giant snowman turns around and hunches back down beside you. "Since we already have the computer turned on. Let's see what else is in here..."

"What's inside that GAMES directory over there?"

**Q/A**

