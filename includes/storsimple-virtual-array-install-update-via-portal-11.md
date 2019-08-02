---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172446"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações por meio do Portal do Azure

1. Vá para o StorSimple Device Manager e selecione **Dispositivos**. Na lista de dispositivos conectados ao seu serviço, selecione e clique no dispositivo que deseja atualizar.

2. Na folha **Configurações**, clique em **Atualizações de dispositivo**.  

3. Você vê uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, você também pode clicar em **Examinar**. Anote a versão do software que você está executando. 

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Você será notificado quando o exame começar e quando for concluído com êxito.
 
4. Depois que as atualizações forem examinadas, clique em **Baixar atualizações**. Na folha **Novas atualizações**, examine as notas de versão. Observe também que depois que as atualizações forem baixadas, você precisará confirmar a instalação. Clique em **OK**.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Você será notificado quando o upload começar e quando for concluído com êxito.

5. Sob **atualizações do dispositivo**, clique em **instalar**.

     ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Em **Novas atualizações**, você é avisado de que a atualização é prejudicial. Uma matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado após ser atualizado. Isso interrompe qualquer E/S em andamento. Clique em **OK** para instalar as atualizações.

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Você será notificado quando o trabalho de instalação for iniciado.

7.  Depois que o trabalho de instalação for concluído com êxito, clique no link **Exibir trabalho**. Essa ação levará você para a folha **Instalar Atualizações**. Você pode exibir informações detalhadas sobre o trabalho aqui. 

    ![atualizar dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se você começou com um array virtual executando a versão de software Atualização 1 (10.0.10296.0), agora você está executando a Atualização 1.1 e está feito. Você pode ignorar as etapas restantes. 

    Se você começou com um array virtual executando a versão de software Update 0.6 (10.0.10293.0), agora você está atualizado para a atualização 1.0. Você verá outra mensagem indicando que as atualizações estão disponíveis. Repita as etapas de 4 a 7 para instalar a atualização 1.1.

    

