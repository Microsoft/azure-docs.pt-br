---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "67171954"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar hotfixes normais por meio do Windows PowerShell para StorSimple
1. Conecte-se ao console serial do dispositivo. Para saber mais, consulte [Etapa 1: Conectar-se ao console serial](../articles/storsimple/storsimple-update-device.md#step1).
2. No menu do console serial, selecione a opção 1, **Log in com acesso completo**. Digite a senha. A senha padrão é **Senha1**.
3. No prompt de comando, digite:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > >>-Este comando se aplica somente a hotfixes normais. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados.
    > Você pode perceber um failover de controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.

4. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.
5. Será solicitada a sua confirmação. Digite **Y** para continuar com a instalação do hotfix.

