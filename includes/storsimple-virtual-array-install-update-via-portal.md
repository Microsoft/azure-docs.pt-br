---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95555763"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar. 

    ![Em recentes, Device Manager MySSDevManager é realçado e selecionado, os dispositivos são realçados e selecionados e o dispositivo MYSSIS1103 é realçado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**. 

    ![As informações para MYSSIS1103 são exibidas. Em configurações, as atualizações de dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**.

    ![No painel atualizações de dispositivo, o botão examinar é realçado. Há uma mensagem de quatro linhas: última verificação/não verificada/versão de software/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Você será notificado quando a varredura começar e quando for concluída com êxito.

    ![A notificação informa "atualizações de verificação para o dispositivo myssis 1103.2:12 PM/concluiu a operação com êxito".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**. 

    ![O painel atualizações do dispositivo diz "novas atualizações estão disponíveis" e "última verificação/11/3/2016 2:12 PM/versão do software/10.0.10280.0". A versão é realçada.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. Na folha **Novas atualizações**, examine as informações das quais você precisará para confirmar a instalação, após as atualizações terem sido baixadas. Clique em **OK**.

    ![O novo painel atualizações diz "depois que as atualizações forem baixadas, você precisará confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Você será notificado quando o upload começar e quando for concluído com êxito.

     ![A notificação diz "baixando atualizações para o dispositivo myssis... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. Na folha **Atualizações de dispositivo**, clique em **Instalar**.

     ![O painel atualizações de dispositivo mostra a mensagem "confirmar instalação de atualizações". O botão instalar é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. Na folha **Novas atualizações**, você será avisado de que a atualização pode causar interrupções. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações. 

    ![A mensagem no novo painel atualizações é "seu dispositivo terá um tempo de inatividade quando essas atualizações forem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Você será notificado quando o trabalho de instalação for iniciado. 

    ![A notificação diz "Instalando atualizações para o dispositivo MYSSIS1103". 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho** na folha **Atualizações de dispositivo** para monitorar a instalação. 

    ![No painel atualizações do dispositivo, o link rotulado como "Instalando atualizações. Exibir trabalho "está realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Isso levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui.

    ![O painel instalar atualizações tem informações de instalação, incluindo dispositivo, status, duração, hora de início e hora de término.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Depois que as atualizações forem instaladas com êxito, você verá uma mensagem sobre isso na folha Atualizações do dispositivo. A versão do software também é alterada para **10.0.10288.0**. 

    ![O painel atualizações do dispositivo mostra "seu dispositivo está atualizado" e fornece a versão do software (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
