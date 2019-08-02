---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172383"
---
Para redefinir seu dispositivo, você precisará apagar com segurança todos os dados no disco de dados e o disco de inicialização do seu dispositivo. 

Use o `Reset-HcsAppliance` cmdlet elimine os discos de dados e o disco de inicialização ou apenas os discos de dados. O `ClearData` e `BootDisk` opções permitem que você limpar os discos de dados e o disco de inicialização, respectivamente.

Se você usar o dispositivo de redefinição na interface web local, somente os discos de dados são apagados de forma segura, mas o disco de inicialização é mantido intatos. O disco de inicialização contém a configuração do dispositivo.

1. [Conectar-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. No prompt de comando, digite:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O exemplo a seguir mostra como usar este cmdlet:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
