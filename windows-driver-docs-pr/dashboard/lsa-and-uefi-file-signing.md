---
title: LSA and UEFI file signing
description: LSA plug-in and UEFI firmware signing
ms.assetid: 
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# File signing LSA plugins and UEFI firmware

The Hardware Dev Center Dashboard lets you to digitally sign [Local Security Authority (LSA)](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection) plugins and [UEFI firmware](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-uefi) binaries, enabling them to be installed on windows devices.


> [!IMPORTANT]
> * File signing requires an extended validation (EV) code signing certificate. see [Get a code signing certificate](get-a-code-signing-certificate.md) for more information.

> * All LSA and UEFI submissions must be a single, signed CAB library file, and contain all files required for signing. 
>   * This file should contain no folders and only the binaries or .efi files to be signed. 

> * UEFI FIRMWARE ONLY - The CAB file signature must match the [Authenticode certificate](https://docs.microsoft.com/windows-hardware/drivers/install/authenticode) for your organization.
>   * Depending on your certificate provider, you may need to use [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764) or an external process.
>   * EFI ByteCode (EBC) files must be compiled using the /ALIGN:32 flag for processing to succeed.

> * LSA PLUGINS ONLY - The CAB file signature must match the EV code signing certificate for your organization. 


## Creating a new UEFI or LSA submission

1.  Sign in to the dashboard with your Microsoft account and click **Hardware certification**.

2.  On the **File Signing Services** page, click **Submit New UEFI** or **Submit New LSA**.
    > [!NOTE]  
    > You may be prompted to sign a legal agreement before creating a new file signing submission. Review and complete the agreement to continue. Every organization only needs to sign the agreement once.

3.  On the submission page, upload the CAB file you want to submit, and click **Submit**.

4. Once your submission has been processed, you’ll receive a notification with your submission ID.

## Managing your file signing submission 

After signing in to the Hardware Dev Center Dashboard, you can [manage your firmware submission](manage-your-hardware-submissions.md) like any other dashboard submission. 

## Related topics

[Microsoft UEFI CA Signing Policy Updates](http://blogs.msdn.com/b/windows_hardware_certification/archive/2013/12/03/microsoft-uefi-ca-signing-policy-updates.aspx)

[Pre-Submission Testing for UEFI Submissions](http://blogs.msdn.com/b/windows_hardware_certification/archive/2013/12/03/pre-submission-testing-for-uefi-submissions.aspx)