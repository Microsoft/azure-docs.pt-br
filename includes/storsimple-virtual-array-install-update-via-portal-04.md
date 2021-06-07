---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027542"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar. 

    ![Em recentes, Gerenciador de Dispositivos MySSDevManager é realçado e selecionado, os dispositivos são realçados e selecionados e o dispositivo MYSSIS1103 é realçado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**. 

    ![As informações para MYSSIS1103 são mostradas. Em configurações, as atualizações de dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**.

    ![O painel atualizações do dispositivo diz "novas atualizações estão disponíveis" e "última verificação/18/01/2017 2:42 PM/versão do software/10.0.10288.0". A versão é realçada.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Você será notificado quando a varredura começar e quando for concluída com êxito.

    ![A notificação diz "verificação de atualizações para o dispositivo MySSIS1103. 2:12 PM/concluir a operação com êxito. "](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**. 

    ![No painel atualizações de dispositivo, há a mensagem "novas atualizações estão disponíveis". O botão baixar atualizações é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Na folha **Novas atualizações**, examine as informações das quais você precisará para confirmar a instalação, após as atualizações terem sido baixadas. Clique em **OK**.

    ![O novo painel atualizações diz "depois que as atualizações forem baixadas, você precisará confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Você será notificado quando o upload começar e quando for concluído com êxito.

     ![A notificação diz "baixando atualizações para o dispositivo MySSIS1... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Na folha **Atualizações de dispositivo**, clique em **Instalar**.

     ![As atualizações de dispositivo indicam "confirmar instalação de atualizações". Há um botão de instalação.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Na folha **Novas atualizações**, você será avisado de que a atualização pode causar interrupções. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações. 

    ![A mensagem no novo painel atualizações é "seu dispositivo terá um tempo de inatividade quando essas atualizações forem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Você será notificado quando o trabalho de instalação for iniciado. 

    ![A notificação diz "Instalando atualizações para o dispositivo MySSIS1103. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho** na folha **Atualizações de dispositivo** para monitorar a instalação. 

    ![No painel atualizações do dispositivo, há um link rotulado como "Instalando atualizações. Exibir trabalho ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Isso levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui.

    ![O painel instalar atualizações tem informações de instalação, incluindo dispositivo, status, duração, hora de início e hora de término.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Depois que as atualizações forem instaladas com êxito, você verá uma mensagem para esse efeito na folha **atualizações do dispositivo** . 
