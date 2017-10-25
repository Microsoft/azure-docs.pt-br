---
title: Redeploy Azure Stack | Microsoft Docs
description: Redeploy Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.contentlocale: pt-br
ms.lasthandoff: 07/10/2017


---
# <a name="redeploy-azure-stack"></a>Redeploy Azure Stack
To redeploy Azure Stack, you must start over from scratch as described below.

## <a name="steps-to-redeploy-azure-stack"></a>Steps to redeploy Azure Stack
1. On the development kit host, open an elevated PowerShell console > navigate to the asdk-installer.ps1 script > run it > click **Reboot**.
2. Select the base operating system (not **Azure Stack**) and click **Next**.
3. After the development kit host reboots, delete the CloudBuilder.vhdx file that was used as part of the previous deployment.
4. [Deploy the development kit](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Next steps
[Connect to Azure Stack](azure-stack-connect-azure-stack.md)


