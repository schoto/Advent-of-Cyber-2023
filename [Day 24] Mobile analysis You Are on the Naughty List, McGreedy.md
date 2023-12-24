Detective Frost-eau continues to piece the evidence together, and Tracy McGreedy is now a suspect. What’s more, the detective believes that McGreedy communicated with an accomplice.

Smartphones are now an indispensable part of our lives for most of us. We use them to communicate with friends, family members, and colleagues, browse the Internet, shop online, perform e-banking transactions, and many other things. Among other reasons, it’s because smartphones are so intertwined in our activities that they can help exonerate or convict someone of a crime.

Frost-eau suggests that Tracy’s company-owned phone be seized so that Forensic McBlue can analyse it in his lab to collect digital evidence. Because it’s company-owned, no complicated legal procedures are required.

**Learning Objectives**

After completing this task, you will learn about:

- Procedures for collecting digital evidence
- The challenges with modern smartphones
- Using Autopsy Digital Forensics with an actual Android image

Digital Forensics

Forensics is a method of using science to solve crimes. As a forensic scientist, you would expect to collect evidence from crime scenes, such as fingerprints, DNA, and footprints. You would use and analyse this evidence to determine what happened at the crime scene and who did it.

With the spread of digital equipment, such as computers, phones, smartphones, tablets, and digital video recorders, a different set of tools and training are required. When it comes to digital evidence, the ideal approach is to acquire a raw image. A raw image is a bit-for-bit copy of the device’s storage.

Forensics is an essential part of the criminal justice system. It helps to solve crimes and bring criminals to justice. However, for evidence to be permissible in court, we must ensure that it’s not tampered with or lost and that it’s authentic when presented to the court. This is why we need to maintain a chain of custody. Chain of custody is a legal concept used to track the possession and handling of evidence from the time it’s collected at a crime scene to the moment it’s presented in court. The chain of custody is documented through a series of written records that track the evidence’s movement and who handled it at each step.

In the following sections, we assume that we are dealing with computers and smartphones owned by the company or seized as part of a criminal investigation.

Acquiring a Digital Forensic Image

Acquiring an image for digital forensics can be challenging, depending on the target device. Computers are more accessible than other devices, so we’ll start our discussion by focusing on them.

There are four main types of forensic image acquisition:

Static acquisition: A bit-by-bit image of the disk is created while the device is turned off.

Live acquisition: A bit-by-bit image of the disk is created while the device is turned on.

Logical acquisition: A select list of files is copied from the seized device.

Sparse acquisition: Select fragments of unallocated data are copied. The unallocated areas of the disk might contain deleted data; however, this approach is limited compared to static and live acquisition because it doesn’t cover the whole disk.

Let’s consider the following two scenarios:

The seized computer is switched off.

As part of a crime scene, the investigators stumble on a live computer that’s switched on.

A Computer That’s Switched Off

Imagine the evidence is a Windows 10 laptop that’s switched off. We know that by default, the disk is not encrypted. We should not turn it on as this will make some changes to the disk and tamper with the evidence as a result. Removing the hard disk drive or SSD from the laptop and cloning it is a relatively simple task:

We use a write blocker, a hardware device that makes it possible to clone a disk without any risk of modifying the original data.

We rely on our forensic imaging software to get the raw image or equivalent. This would create a bit-by-bit copy of the disk.

Finally, we need a suitable storage device to save the image.

A Computer That’s Switched On
Another example would be dealing with a laptop that is switched on. In this case, we shouldn’t switch it off. Instead, we should aim for a live image. The laptop might be encrypted, and shutting it down will make reading its data impossible without a password. Furthermore, data in the volatile memory (RAM) might be important for our investigation.

When they’re able to analyse a device that’s switched on, investigators can gain access to the accounts and services the suspect is logged into. This can be indispensable in some instances to prove guilt and solve a crime.

Various tools can be used. They usually require us to run a program on the target system, giving us access to all the data in the volatile memory and on the non-volatile memory (disk).

Acquiring a Smartphone Image

The smartphone is a ubiquitous digital device that we can expect to encounter. Modern smartphones are now encrypted by default, which can be a challenge for digital forensics. Without the decryption key, encrypted storage looks literally like random data. Finding the decryption key is crucial to be able to analyse the image.

Let us briefly overview smartphone encryption before discussing acquiring a forensic image of an Android device.

Encryption in Smart Phones

Android 4.4 introduced full-disk encryption. When full-disk encryption is activated, the user-created data is automatically encrypted before being written to the device storage and decrypted before being read from the storage. Furthermore, the phone cannot be booted before providing the password. It is important to note that this type of encryption applies to built-in storage and doesn’t include removable memory, such as micro SD cards.

Android 7.0 introduced Direct Boot, a file-based encryption mode. File-based encryption lets us use different keys for different files. From the user’s perspective, the phone can be booted, and some basic functionality can be used, such as receiving phone calls. Beyond this basic functionality, the encryption password needs to be provided. Depending on the settings and Android version, the SD card might also be encrypted; Android 9.0 and higher can encrypt an SD card as it would encrypt internal storage.

Since Android 6.0, encryption has been mandatory. Unless we are dealing with an older Android version, we can expect the seized phone to be encrypted. Apple iPhone devices are encrypted by default, too. Data Protection, a file-based encryption methodology, is part of iOS, the iPhone’s operating system.

In this section, we provided an overview of smartphone encryption. Ultimately, encryption can be a significant obstacle that digital forensic investigators need to overcome. Obtaining or discovering the encryption key is necessary for a complete digital forensic investigation.

Practical Case

Tracy McGreedy’s phone is company property. This means that it was easy for Detective Frost-eau to seize it and ask Forensic McBlue to use his expertise to carry out the digital forensic investigation.

The first thing Forensic McBlue does is put the phone in a Faraday bag. A Faraday bag prevents the phone from receiving any wireless signal, meaning Tracy McGreedy can’t wipe its data remotely.

Now that McBlue has Tracy McGreedy’s Android phone, it’s time to get an image. He successfully unlocks the phone using the same password used to lock everyone out of the server room three weeks ago! What a coincidence!

The main tools McBlue uses for analysing Android phones are Android Debug Bridge (adb) and Autopsy Digital Forensics. Once the phone is unlocked and connected to the laptop, creating a backup using adb backup is relatively easy. Here’s the exact command he uses:

adb backup -all -f android_backup.ab

backup -all means that we want to back up all applications that allow backups

-f android_backup.ab saves the backup to the file android_backup.ab

The main limitation of adb backup is that some applications don’t support this option as they explicitly disallow backups with the setting allowBackup=false. Furthermore, although this option still works with a limited number of applications, it has been restricted since Android 12, so it’s a good idea to rely on more robust alternatives.

This backup of various applications is considered a logical image, but Forensic McBlue isn’t satisfied. He wants a full raw image of the phone storage.

Many commercial products can be used to acquire an image. However, most of them rely on the Android Debug Bridge (adb) and combine it with other tools, such as an exploit to gain root access. (An Android device won’t provide root access to the user, unless it’s for development purposes. This limits the ability to access many files and directories on the phone storage. “Rooting” an Android device gives us full access to the device files, including raw access to the disk.)

Forensic McBlue prepares a list of potential exploits that would give him root access to the Android device. After a couple of attempts, Forensic McBlue is able to exploit the phone successfully and get root access. With root access, he has full access to all the physical storage.

To confirm that he is root, he issues the command whoami. He also needs to issue the mount command to find the mounted devices, but this would result in a very long list of all real and virtual mounted devices. However, the application data is in the directory data. We need to focus our attention on the storage device mounted on /data. 

To filter for the lines mentioning “data”, Forensic McBlue uses the command mount | grep data instead of just mount. The output allows him to pinpoint the name of the storage device mounted on /data, which turns out to be /dev/block/dm-0. The interaction can be seen in the terminal below.

```
df-workstation$ adb shell
generic_x86:/ # whoami
root
127|generic_x86:/ # mount | grep data           
[...]
/dev/block/dm-0 on /data type ext4 (rw,seclabel,nosuid,nodev,noatime,errors=panic,data=ordered)
[...]
generic_x86:/ # 
```

As we learned from the commands in the terminal above, the device is /dev/block/dm-0. Think of this device as a partition on the smartphone’s disk. McBlue wants to get this whole partition and analyse it using Autopsy.

There are many ways to leverage the power of adb and get a raw dump of /dev/block/dm-0. One easy way is using adb pull:

adb pull /dev/block/dm-0 Android-McGreedy.img

The command above will pull the device /dev/block/dm-0 and save it to the local file Android-McGreedy.img. After a few minutes, the command is complete, and a 6 GB image file is created! Note that we need root access for the above command to work on the Android device.

Now, all we have to do is import the image into Autopsy. The steps are straightforward, as we see in the screenshots below. Once we start Autopsy, we see a dialogue box asking whether we want to create a new digital forensics case or open an existing one.

![autopsy](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/e715b72b-4f53-48eb-977e-073f44a40084)

After clicking “New Case”, we should specify the case name. Let’s use the suspect name and device to avoid ambiguity.

![case](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/16adfb1c-b154-4287-987c-e2fc5d302a09)

Next, we need to provide the case number and the name of the investigator: Forensic McBlue.

![101](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/e0bf0e34-d5bc-49cf-8628-b2afb2eb9172)

The next step allows us to specify the name of the raw image. In some cases, we can have multiple raw images in one case. For example, we can have four images from the same suspect: two smartphones, a laptop, and a desktop. An explicit, unambiguous name is necessary.

![generate](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/c1c15ad9-2684-4a5e-8445-f3c031274d43)

Now, let’s analyse the disk image we retrieved from the smartphone. In other cases, we might use a local disk, i.e., a hardware disk attached to the computer. Another example would be logical files, such as MS Outlook email archive .pst file.

![disk](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/fa892dcc-88d3-4b30-b2b4-6c9f8c3387fd)

We provide the location of the raw image file we want to analyse.

![auto](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/eb205c93-c1fb-4593-916e-76d99523e0c3)

Finally, we must select the ingest modules to help us analyse the file. In this case, the indispensable modules are the two Android Analyzer modules; however, we can select any other modules we find helpful in this case.

![files](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/9d49f5df-cbfb-4693-bd69-100d304d337b)

Once we click “Next”, Autopsy will create a new case and run the ingest modules on the selected data source: Android image in this case.

![status](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/2e374d17-9bb7-43cc-b714-d0ad1eee9126)

You can access an MS Windows machine with Autopsy set up on it. Click on “Start Room” and wait for it to load. It should take a couple of minutes to fully boot up.

You can display the virtual machine in your browser by clicking “Show Split View”. Alternatively, you can access the VM from your local remote desktop client over VPN. The login credentials for the remote desktop are:

Username: **********

Password: **********

We have already created a case in Autopsy, so you don’t have to create a new one and wait for the ingest modules to analyse the data. Using Autopsy, open the Tracy McGreedy.aut case in the Documents folder and check the questions below:

**Q/A**

