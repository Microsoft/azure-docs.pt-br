---
title: "Desativar e excluir um dispositivo StorSimple da série 8000 | Microsoft Docs"
description: "Descreve como remover o dispositivo StorSimple do serviço primeiro desativando-o e então excluindo-o."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e excluir um dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve como desativar e excluir um dispositivo StorSimple que está conectado a um serviço do Gerenciador de Dispositivos do StorSimple. As diretrizes neste artigo se aplicam somente a dispositivos StorSimple da série 8000, incluindo os Dispositivos de Nuvem StorSimple. Se você estiver usando uma Matriz Virtual do StorSimple, acesse [Desativar e excluir uma Matriz Virtual do StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

A desativação rompe a conexão entre o dispositivo e o serviço do Gerenciador de Dispositivos do StorSimple correspondente. Talvez você queira usa rum dispositivo StorSimple fora de serviço (por exemplo, se você estiver substituindo ou atualizando seu dispositivo ou não estiver mais usando o StorSimple). Se for esse o caso, você precisará desativar o dispositivo para poder excluí-lo.

Quando você desativa um dispositivo, todos os dados que foram armazenados localmente no dispositivo ficarão inacessíveis. Somente os dados associados ao dispositivo que foi armazenado na nuvem podem ser recuperados.

> [!WARNING]
> A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço do Gerenciador de Dispositivos do StorSimple, a menos que ele seja redefinido com as configurações de fábrica.
>
> O processo de redefinição de fábrica exclui todos os dados que foram armazenados localmente no seu dispositivo. Por isso, é necessário criar um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Esse instantâneo permitirá que você recupere todos os dados em um estágio posterior.

Depois de ler este tutorial, você poderá:

* Desativar um dispositivo e excluir os dados.
* Desativar um dispositivo e manter os dados.

> [!NOTE]
> Antes de desativar um dispositivo físico ou da nuvem StorSimple, interrompa ou exclua os clientes e hosts que dependem dele.


## <a name="deactivate-and-delete-data"></a>Desativar e excluir dados

Se você estiver interessado em excluir o dispositivo completamente e não quiser manter os dados nele, conclua as etapas a seguir.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e excluir os dados

1. Antes de desativar um dispositivo, é necessário excluir todos os contêineres de volumes (e os próprios volumes) associados ao dispositivo. Você pode excluir os contêineres de volume somente depois que tiver excluído os backups associados.

    > [!NOTE]
    > Antes de desativar um dispositivo físico StorSimple, ou um dispositivo de nuvem, certifique-se de que os dados do contêiner de volume excluído tenham sido realmente excluídos do dispositivo. Você pode monitorar os gráficos de consumo da nuvem e, quando perceber uma queda na utilização da nuvem devido aos backups excluídos, desative o dispositivo. Se você desativar o dispositivo antes dessa queda, os dados ficarão presos na conta de armazenamento e acumularão encargos.

2. Desative o dispositivo como se segue:
   
   1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, selecione o dispositivo que você deseja desativar, clique com o botão direito do mouse e clique em **Desativar**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na folha **Desativar**, digite o nome do dispositivo para confirmar e clique em **Desativar**. O processo de desativação começará e levará alguns minutos para ser concluído.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Após a desativação, você pode excluir o dispositivo completamente. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. Use as etapas a seguir para excluir o dispositivo:
   
   1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, selecione o dispositivo desativado que você deseja excluir, clique com o botão direito do mouse e clique em **Excluir**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na folha **Excluir**, digite o nome do dispositivo para confirmar e clique em **Excluir**. A exclusão levará alguns minutos para ser concluída.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Você será notificado quando a exclusão for concluída com êxito. A lista de dispositivos também é atualizada para refletir a exclusão.

## <a name="deactivate-and-retain-data"></a>Desativar e reter dados

Se você tiver interesse em excluir o dispositivo, porém deseja manter os dados, execute as seguintes etapas:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados
1. Desative o dispositivo. Todos os contêineres de volume e os instantâneos do dispositivo são mantidos.
   
   1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, selecione o dispositivo que você deseja desativar, clique com o botão direito do mouse e clique em **Desativar**.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na folha **Desativar**, digite o nome do dispositivo para confirmar e clique em **Desativar**. O processo de desativação começará e levará alguns minutos para ser concluído.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Agora é possível realizar failover dos contêineres de volume e dos instantâneos associados. Para procedimentos, vá para [Failover e recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Após a desativação e failover, você poderá excluir o dispositivo completamente. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. Para excluir o dispositivo, execute as seguintes etapas:
   
   1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, selecione o dispositivo desativado que você deseja excluir, clique com o botão direito do mouse e clique em **Excluir**.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na folha **Excluir**, digite o nome do dispositivo para confirmar e clique em **Excluir**. A exclusão levará alguns minutos para ser concluída.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Você será notificado quando a exclusão for concluída com êxito. A lista de dispositivos também é atualizada para refletir a exclusão.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desativar e excluir uma dispositivo de nuvem

Para um Dispositivo de Nuvem StorSimple, a desativação do portal desaloca e exclui a máquina virtual e os recursos criados quando ele foi provisionado. Depois de desativado, o dispositivo de nuvem não poderá ser restaurado ao estado anterior.

![Desativar um Dispositivo de Nuvem StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

A desativação resulta nas seguintes ações:

* O Dispositivo de Nuvem StorSimple é removido do serviço.
* A máquina virtual para o Dispositivo de Nuvem StorSimple é excluída.
* O disco do sistema operacional e os discos de dados criados para o Dispositivo de Nuvem StorSimple são removidos.
* O serviço hospedado e a Rede Virtual criados durante o provisionamento são mantidos. Se você não estiver usando as entidades, deverá excluí-las manualmente.
* Instantâneos de nuvem criados pelo Dispositivo de Nuvem StorSimple são mantidos.

Depois que o dispositivo de nuvem é desativado, você poderá excluí-lo da lista de dispositivos. Selecione o dispositivo desativado, clique com o botão direito do mouse e clique em **Excluir**. O StorSimple notifica você quando o dispositivo é excluído e mostra a lista de atualizações de dispositivos.

## <a name="next-steps"></a>Próximas etapas

* Para restaurar os dispositivos desativados para os padrões de fábrica, acesse [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obter assistência técnica, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
* Para saber como usar o serviço do Gerenciador de Dispositivos do StorSimple, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

