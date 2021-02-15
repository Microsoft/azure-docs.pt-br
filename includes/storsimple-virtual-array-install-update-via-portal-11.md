---
title: incluir arquivo
description: incluir arquivo
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95559118"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar.

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**.  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**. Anote a versão do software que você está executando. 

    ![O painel atualizações de dispositivo diz "novas atualizações estão disponíveis" (realçado) e "última verificação/6/22/2018 2:46 PM/versão do software/10.0.10296.0". A versão é realçada.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Você será notificado quando o exame começar e quando for concluído com êxito.
 
4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**. Na folha **Novas atualizações**, examine as notas de versão. Observe também que depois que as atualizações forem baixadas, você precisará confirmar a instalação. Clique em **OK**.

    ![Em atualizações de dispositivo, o botão baixar atualizações é realçado. Em novas atualizações, há um link para notas de versão e uma mensagem para confirmar a instalação depois que as atualizações forem baixadas. Há um botão OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Você será notificado quando o upload começar e quando for concluído com êxito.

5. Sob **atualizações do dispositivo**, clique em **instalar**.

     ![As atualizações de dispositivo indicam "confirmar instalação de atualizações". O botão instalar e a versão do software são realçados.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Em **Novas atualizações**, você é avisado de que a atualização é prejudicial. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações.

    ![Novas atualizações diz "seu dispositivo terá um tempo de inatividade quando essas atualizações forem instaladas". Há um link para as notas de versão e um botão OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Você será notificado quando o trabalho de instalação for iniciado.

7.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho**. Essa ação levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui. 

    ![As atualizações de dispositivo têm um link rotulado "Instalando atualizações. Exibir trabalho ".](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se você começou com um array virtual executando a versão de software Atualização 1 (10.0.10296.0), agora você está executando a Atualização 1.1 e está feito. Você pode ignorar as etapas restantes. 

    Se você começou com um array virtual executando a versão de software Update 0.6 (10.0.10293.0), agora você está atualizado para a atualização 1.0. Você verá outra mensagem indicando que as atualizações estão disponíveis. Repita as etapas de 4 a 7 para instalar a atualização 1.1.

    

