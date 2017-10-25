---
title: Replace a physical disk in Azure Stack | Microsoft Docs
description: Outlines the process for how to replace a physical disk in Azure Stack.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="replace-a-physical-disk-in-azure-stack"></a>Replace a physical disk in Azure Stack

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

This article describes the general process to replace a physical disk in Azure Stack. If a physical disk fails, you should replace it as soon as possible.

You can use this procedure for integrated systems, and for development kit deployments that have hot-swappable disks.

Actual disk replacement steps will vary based on your original equipment manufacturer (OEM) hardware vendor. See your vendor’s field replaceable unit (FRU) documentation for detailed steps that are specific to your system. 

## <a name="review-disk-alert-information"></a>Review disk alert information
When a disk fails, you receive an alert that tells you that connectivity has been lost to a physical disk. 

 ![Alert showing connectivity lost to physical disk](media/azure-stack-replace-disk/DiskAlert.png)

If you open the alert, the alert description contains the scale unit node and the exact physical slot location for the disk that you must replace. Azure Stack further helps you to identify the failed disk by using LED indicator capabilities.

 ## <a name="replace-the-disk"></a>Replace the disk

Follow your OEM hardware vendor’s FRU instructions for actual disk replacement.

To prevent the use of an unsupported disk in an integrated system, the system blocks disks that are not supported by your vendor. If you try to use an unsupported disk, a new alert tells you that a disk has been quarantined because of an unsupported model or firmware.

After you replace the disk, Azure Stack automatically discovers the new disk and starts the virtual disk repair process.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Check the status of virtual disk repair
 
 After you replace the disk, you can monitor the virtual disk health status and repair job progress by using the privileged endpoint. Follow these steps from any computer that has network connectivity to the privileged endpoint.

1. Open a Windows PowerShell session and connect to the privileged endpoint.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Run the following command to view virtual disk health:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Powershell output of Get-VirtualDisk command](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Run the following command to view current storage job status:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Powershell output of Get-StorageJob command](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Troubleshoot virtual disk repair

If the virtual disk repair job appears stuck, run the following command to restart the job:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 

