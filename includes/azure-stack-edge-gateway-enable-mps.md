---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517541"
---
1. Antes de começar, verifique se:

    1. Você configurou e [ativou seu Azure Stack dispositivo pro Edge](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) com um recurso Azure Stack Edge no Azure.
    1. Você [configurou a computação neste dispositivo no portal do Azure](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Conecte-se à interface do PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Use o comando a seguir para habilitar os MPS em seu dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```