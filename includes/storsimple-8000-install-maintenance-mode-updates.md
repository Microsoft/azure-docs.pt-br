---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172118"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações do modo de manutenção por meio do Windows PowerShell para StorSimple

Quando você aplicar atualizações do modo de manutenção para o dispositivo StorSimple, todas as solicitações de E/S são pausadas. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de cluster também são interrompidos. Ambos os controladores são reiniciados quando você entra ou sai desse modo. Quando você sair desse modo, todos os serviços serão retomados e estarão íntegros. (Isso pode levar alguns minutos).

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, verifique se ambos os controladores estão íntegros no portal do Azure. Se o controlador não estiver íntegro, [entre em contato com o Suporte da Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) para saber as próximas etapas.
> * Quando você estiver no modo de manutenção, você precisa primeiro atualizar um controlador e, em seguida, o outro controlador.

1. Use o PuTTY para conectar-se ao console serial. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). No prompt de comando, pressione **Enter**. Selecione a Opção 1, **Fazer logon com acesso completo**.

2. Para colocar o controlador no modo de manutenção, digite:
    
    `Enter-HcsMaintenanceMode`

    Ambos os controladores são reiniciados no modo de manutenção.

3. Instale suas atualizações no modo de manutenção. Digite:

    `Start-HcsUpdate`

    Você recebe uma solicitação para fornecer sua confirmação. Depois de confirmar as atualizações, elas serão instaladas no controlador que está sendo atualmente acessado por você. Depois que as atualizações forem instaladas, o controlador é reiniciado.

4. Monitore o status das atualizações. Faça logon no outro controlador já que o controlador atual está sendo atualizado e não é capaz de processar nenhum outro comando. Digite:

    `Get-HcsUpdateStatus`

    Se `RunInProgress` for `True`, a atualização ainda está em andamento. Se `RunInProgress` for `False`, isso indica que a atualização foi concluída.

5. Depois que as atualizações de firmware de disco são aplicadas com sucesso e o controlador atualizado tiver sido reiniciado, verifique a versão de firmware de disco. No controlador atualizado, digite:

    `Get-HcsFirmwareVersion`
   
    As versões de firmware de disco esperadas são: `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Saia do modo de manutenção. Digite o comando a seguir para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

    Os controladores são reiniciados quando você sai do modo de manutenção.

7. Volte para o portal do Azure. O portal poderá não mostrar que as atualizações do modo de manutenção foram instaladas durante 24 horas.