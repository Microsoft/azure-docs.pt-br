---
title: Add users for Azure Stack ADFS | Microsoft Docs
description: Learn how to add users for ADFS deployments of Azure Stack
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2cad56caa182a9abdca02944000b1506953af3f4
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="add-users-in-the-azure-stack-development-kit"></a>Add users in the Azure Stack Development Kit

*Applies to: Azure Stack Development Kit*

To add additional users to the Development Kit deployment, you must add them to the Azure Stack Development Kit directory using Microsoft Management Console from the Azure Stack host computer.
1.  On the Azure Stack host computer, open Microsoft Management Console.
2.  Click **File > Add or remove snap-in**.
3.  Select **Active Directory Users and Computers** > **AzureStack.local** > **Users**.
4.  Click **Action** > **New** > **User**.
5.  In the New Object – User window, provide and confirm a password
6.  Click **Next** to finalize the values and click Finish to create the user.



