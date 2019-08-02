---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171954"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar hotfixes normais por meio do Windows PowerShell para StorSimple
1. Conecte-se ao console serial do dispositivo. Para obter mais informações, confira a [Etapa 1: Conecte-se ao console serial](../articles/storsimple/storsimple-update-device.md#step1).
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é **Senha1**.
3. No prompt de comando, digite:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > >>-Este comando se aplica somente a hotfixes normais. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados.
    > Você pode perceber um failover de controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.

4. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.
5. Será solicitada a sua confirmação. Digite **Y** para prosseguir com a instalação do hotfix.

