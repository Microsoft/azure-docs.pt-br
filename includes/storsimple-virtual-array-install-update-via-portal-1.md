---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005802"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar.

    ![Em recentes, Device Manager MySSDevManager é realçado e selecionado, os dispositivos são realçados e selecionados e o dispositivo MYSSIS1103 é realçado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**.

    ![As informações para MYSSIS1103 são mostradas. Em configurações, as atualizações de dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**. Anote a versão do software que você está executando. 

    ![O painel atualizações de dispositivo diz "novas atualizações estão disponíveis" e "última verificação/11/01/2017 10:56 AM/software Version/10.0.10289.0". A versão é realçada.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Você será notificado quando o exame começar e quando for concluído com êxito.

    ![A notificação diz "verificação de atualizações para o dispositivo MySVAFS110.... 10:57 AM/a operação está em andamento. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**.

    ![No painel atualizações de dispositivo, há a mensagem "novas atualizações estão disponíveis". O botão baixar atualizações é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Na folha **Novas atualizações**, examine as notas de versão. Observe também que depois que as atualizações forem baixadas, você precisará confirmar a instalação. Clique em **OK**.

    ![O novo painel atualizações diz "depois que as atualizações forem baixadas, você precisará confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Você será notificado quando o upload começar e quando for concluído com êxito.

     ![A notificação diz "baixando atualizações para o dispositivo MySVAFS... 11:07 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Na folha **Atualizações de dispositivo**, clique em **Instalar**.

     ![As atualizações de dispositivo indicam "confirmar instalação de atualizações". O botão instalar é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Na folha **Novas atualizações**, você será avisado de que a atualização pode causar interrupções. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações.

    ![A mensagem no novo painel atualizações é "seu dispositivo terá um tempo de inatividade quando essas atualizações forem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Você será notificado quando o trabalho de instalação for iniciado.

    ![A notificação diz "Instalando atualizações para o dispositivo MySVAFS110.... 11:09 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho** na folha **Atualizações de dispositivo** para monitorar a instalação. 

    ![No painel atualizações do dispositivo, há um link rotulado como "Instalando atualizações. Exibir trabalho ". O botão instalar é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Essa ação levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui.

    ![O painel instalar atualizações tem informações de instalação, incluindo dispositivo, status, duração, hora de início e hora de término.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se você iniciou com uma matriz virtual executando a versão do software da Atualização 0.6 (10.0.10293.0), agora está executando a Atualização 1 e está pronto. Você pode ignorar as etapas restantes. Se você iniciou com uma matriz virtual executando uma versão do software anterior à Atualização 0.6 (10.0.10293.0), agora atualizou para a Atualização 0.6. Você verá outra mensagem indicando que as atualizações estão disponíveis. Repita as etapas 4 a 8 para instalar a Atualização 1.

    ![O painel atualizações de dispositivo diz "novas atualizações estão disponíveis" e "última verificação/11/1/2017 10:56 AM/software Version/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

