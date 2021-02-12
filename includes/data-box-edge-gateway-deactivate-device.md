---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027976"
---
Para redefinir o dispositivo, você precisa apagar com segurança todos os dados no disco de dados e no disco de inicialização do dispositivo. 

Use o `Reset-HcsAppliance` cmdlet para apagar os discos de dados e o disco de inicialização ou apenas os discos de dados. As `ClearData` `BootDisk` Opções e permitem apagar os discos de dados e o disco de inicialização, respectivamente.

A `BootDisk` opção apaga o disco de inicialização e torna o dispositivo inutilizável. Ela deverá ser usada somente quando o dispositivo precisar ser devolvido à Microsoft. Para obter mais informações, consulte [devolver o dispositivo à Microsoft](../articles/databox-online/azure-stack-edge-return-device.md).

Se você usar a redefinição do dispositivo na IU da Web local, somente os discos de dados serão apagados com segurança, mas o disco de inicialização permanecerá intacto. O disco de inicialização contém a configuração do dispositivo.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. No prompt de comando, digite:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O exemplo a seguir mostra como usar este cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```