# LogMeIn Rescue Integration Scripts for Nexthink Remote Actions

This repository contains script files for integrating [LogMeIn Rescue](https://www.logmeinrescue.com/) remote support with [Nexthink Remote Actions](https://docs.nexthink.com/). These scripts enable remote assistance via Rescue channels by automating download and launch of the Rescue applet, usable across Windows and macOS/Linux endpoints.

# Latest Release: 
Release [v1.0.0](https://github.com/goto-developers/rescue-nexthink-integration/releases/tag/v1.0.0) is published and can be downloaded from the "Release" section.

## Signature

All scripts in this repository have been digitally signed to ensure integrity and authenticity.  
If your organization prefers to use its own certificate for signing, please follow the instructions below.

- **Distributing the Signing Certificate to Trusted Publishers (Windows)**  
  For Windows environments, it is important to distribute your organization's signing certificate to the Trusted Publishers store. This allows endpoints to validate and run the signed scripts without security warnings.  
  [Learn more in Nexthink documentation »](https://docs.nexthink.com/platform/user-guide/remote-actions/managing-remote-actions/writing-scripts-for-remote-actions-on-windows#signing-the-script)

- **Signing Scripts on macOS**  
  For macOS, follow the Nexthink documentation to sign your scripts. This ensures that the remote actions executed on macOS devices can be properly authenticated and authorized.  
  [Learn more in Nexthink documentation »](https://docs.nexthink.com/platform/user-guide/remote-actions/managing-remote-actions/writing-scripts-for-remote-actions-on-mac#signing-the-script)

- **Adding GoTo certificate to the Trusted Publisher store (Windows)**
Extracting the signing certificate from a signed PowerShell script, exporting it to a .cer file, and importing it into the Trusted Publishers store, Windows will trust scripts signed by this certificate and execute them without warning.  
 [Learn more](#adding-goto-signed-certificate-to-the-trusted-publisher-store)


## Contents

- [`Launch LogMeIn Ad-hoc Channel based session`](#launch-logmein-ad-hoc-channel-based-session)  
  *PowerShell script for Windows, Bash script for macOS endpoints.*

- [`Launch LogMeIn Ad-hoc Channel based session`](#launch-logmein-ad-hoc-channel-based-session)  
  *PowerShell script for Windows, Bash script for macOS endpoints.* 

- [`Launch LogMeIn Calling Card Channel based session`](#launch-logmein-calling-card-channel-based-session)  
   *Bash script for macOS endpoints.*

- [`Launch LogMeIn Calling Card PIN based session`](#launch-logmein-calling-card-pin-based-session)  
  *PowerShell script for Windows, Bash script for macOS endpoints.* 

## Details

### Launch LogMeIn Ad-hoc Channel based session

**Description:**  
Downloads and launches the LogMeIn Rescue support applet using a channel entry ID from the specified host site for Linux/macOS endpoints. The downloaded file is removed after use.

#### Windows: launch_logmein_channel_adhoc.ps1


**Parameters:**  
- `entryId` – Rescue channel entry ID.  
- `hostSite` – LogMeIn Rescue host site (e.g., `secure.logmeinrescue.com`).

**Usage Example:**
```powershell 
.\launch_logmein_channel_adhoc.ps1 -entryId 123456 -hostSite secure.logmeinrescue.com 
```

#### macOS: launch_logmein_channel_adhoc.sh


**Parameters:**  
- `entryId` – Rescue channel entry ID.  
- `hostSite` – LogMeIn Rescue host site (e.g., `secure.logmeinrescue.com`).

**Usage Example:**
```bash
./launch_logmein_channel_adhoc.sh 123456 secure.logmeinrescue.com
```

### Launch LogMeIn Ad-hoc PIN based session

**Description:**  
Launches the LogMeIn Rescue Calling Card using the provided PIN code. Requires the Calling Card referral/channel ID to use the installed instance.

#### Windows: launch_logmein_PIN_calling_card-2.ps1


**Parameters:**  
- `pinCode` – The 6 or 9-digit PIN code provided by the support technician.
- `referralId` – The referral ID associated with the Calling Card.

**Usage Example:**
```powershell
.\launch_logmein_PIN.ps1 -pinCode 123456 -referralId 654321
```


#### macOS launch_logmein_PIN_calling_card.sh

**Parameters:**  
- `pinCode` – 6 or 9 digit PIN code provided by the support technician.
- `channelId` – LogMeIn Rescue channel ID associated with the Calling Card.

**Usage Example:**
```bash
./launch_logmein_PIN_calling_card.sh 123456 1234
```

### Launch LogMeIn Calling Card Channel based session
**Description:**  
Launches the LogMeIn Rescue Calling Card with the corresponding channel. Requires the Calling Card referral ID to use the installed instance.

#### Windows: launch_logmein_channel_callind_card.ps1


**Parameters:**  
- `referralId` – The referral ID associated with the Calling Card.

**Usage Example:**
```powershell
.\launch_logmein_channel_calling_card.ps1 -referralId 654321
```

### Launch LogMeIn Calling Card PIN based session

**Description:**  
Downloads and launches the LogMeIn Rescue support applet directly from the specified host site using a channel entry ID, then launches it automatically. The downloaded file is cleaned up after launch.

#### Windows: launch_logmein_channel_callind_adhoc.ps1


**Parameters:**  
- `entryId` – The Rescue channel entry ID.
- `hostSite` – The LogMeIn Rescue host site (e.g., `secure.logmeinrescue.com`).

**Usage Example:**
```powershell
.\launch_logmein_channel_adhoc.ps1 -entryId 123456 -hostSite secure.logmeinrescue.com
```

#### macOS: launch_logmein_channel_callind_adhoc.sh

**Parameters:**  
- `entryId` – Rescue channel entry ID.
- `hostSite` – LogMeIn Rescue host site (e.g., `secure.logmeinrescue.com`).

**Usage Example:**
```bash
./launch_logmein_channel_adhoc.sh 123456 secure.logmeinrescue.com
```

## Adding GoTo Signed Certificate to the Trusted Publisher store

Follow the below steps to add the cerificate used for signing to the the Trusted Publisher store on Windows

### 1. Extract the Embedded Certificate from the Script
PowerShell scripts that are digitally signed include a signature block at the end of the file. You can use the Get-AuthenticodeSignature cmdlet to extract the embedded code-signing certificate:
```powershell 
$signature = Get-AuthenticodeSignature -FilePath .\launch_logmein_channel_adhoc.ps1
$cert = $signature.SignerCertificate
```

### 2. Export the Certificate to a File
Save the extracted certificate as a .cer (DER or Base64-encoded X.509) file:
```poweshell
$cert.Export('Cer') | Set-Content -Encoding byte -Path .\PublisherCert.cer
```

### 3. Add the Certificate to the Trusted Publishers Store
Import the certificate into the Trusted Publishers store for the local machine or current user:
```powershell
Import-Certificate -FilePath .\PublisherCert.cer -CertStoreLocation Cert:\CurrentUser\TrustedPublisher
```

### 4. Verification
You can check that the certificate has been imported by opening the Certificates Management Console (certmgr.msc) or running:
```powershell
Get-ChildItem Cert:\CurrentUser\TrustedPublisher
# or, for Local Machine
Get-ChildItem Cert:\LocalMachine\TrustedPublisher
```
