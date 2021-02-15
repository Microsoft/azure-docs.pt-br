---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171952"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar os hotfixes do modo de manutenção por meio do Windows PowerShell para StorSimple
> [!IMPORTANT]
> Quando você estiver no modo de Manutenção, precisará aplicar a atualização primeiro em um controlador e, em seguida, no outro controlador.
> 
> 

1. Coloque o dispositivo no modo de Manutenção. Consulte [Etapa 2: Entrar no modo de manutenção](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções sobre como entrar no modo de Manutenção.
2. Para aplicar o hotfix, digite:
   
     `Start-HcsHotfix` 
3. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.
4. Será solicitada a sua confirmação. Digite **Y** para continuar com a instalação do hotfix.
5. Após aplicar o hotfix em um controlador, faça logon no outro controlador. Aplique o hotfix da mesma forma que você fez para o controlador anterior.
6. Após a aplicação dos hotfixes, saia do modo de Manutenção. Veja a [Etapa 4: Sair do modo de manutenção](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

