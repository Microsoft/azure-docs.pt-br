---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172110"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**. Anote a versão do software que você está executando. 

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Você será notificado quando o exame começar e quando for concluído com êxito.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Na folha **Novas atualizações**, examine as notas de versão. Observe também que depois que as atualizações forem baixadas, você precisará confirmar a instalação. Clique em **OK**.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Você será notificado quando o upload começar e quando for concluído com êxito.

     ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Na folha **Atualizações de dispositivo**, clique em **Instalar**.

     ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Na folha **Novas atualizações**, você será avisado de que a atualização pode causar interrupções. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Você será notificado quando o trabalho de instalação for iniciado.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho** na folha **Atualizações de dispositivo** para monitorar a instalação. 

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Essa ação levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se você iniciou com uma matriz virtual executando a versão do software da Atualização 0.6 (10.0.10293.0), agora está executando a Atualização 1 e está pronto. Você pode ignorar as etapas restantes. Se você iniciou com uma matriz virtual executando uma versão do software anterior à Atualização 0.6 (10.0.10293.0), agora atualizou para a Atualização 0.6. Você verá outra mensagem indicando que as atualizações estão disponíveis. Repita as etapas 4 a 8 para instalar a Atualização 1.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

