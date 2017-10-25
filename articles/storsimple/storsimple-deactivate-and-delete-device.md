---
title: Desativar e excluir um dispositivo StorSimple | Microsoft Docs
description: "Descreve como remover o dispositivo StorSimple do serviço primeiro desativando-o e então excluindo-o."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>Desativar e excluir um dispositivo StorSimple série 8000 por meio do serviço StorSimple Manager
## <a name="overview"></a>Visão geral
Talvez você queira usa rum dispositivo StorSimple fora de serviço (por exemplo, se você estiver substituindo ou atualizando seu dispositivo ou não estiver mais usando o StorSimple). Se for esse o caso, você precisará desativar o dispositivo para poder excluí-lo. A desativação rompe a conexão entre o dispositivo e o serviço StorSimple Manager correspondente. Este tutorial explica como remover um dispositivo StorSimple do serviço desativando-o primeiro e depois excluindo-o. 

Quando você desativa um dispositivo, todos os dados que foram armazenados localmente no dispositivo não estarão acessíveis. Somente os dados associados ao dispositivo que foi armazenado na nuvem podem ser recuperados.  

> [!WARNING]
> A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço StorSimple Manager, a menos que ele primeiro seja redefinido para as configurações de fábrica. 
> 
> O processo de redefinição de fábrica exclui todos os dados que foram armazenados localmente no seu dispositivo. Por isso, é essencial fazer um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Isso permitirá que você recupere todos os dados em um estágio posterior.
> 
> 

Este tutorial explica como:

* Desativar um dispositivo e excluir os dados
* Desativar um dispositivo e manter os dados

Ele também explica como funcionam a desativação e a exclusão em um dispositivo virtual StorSimple.

> [!NOTE]
> Antes de desativar um dispositivo físico ou virtual StorSimple, certifique-se de interromper ou excluir clientes e hosts que dependem dele.
> 
> 

## <a name="deactivate-and-delete-data"></a>Desativar e excluir dados
Se você estiver interessado em excluir o dispositivo completamente e não quiser manter os dados nele, conclua as etapas a seguir.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e excluir os dados
1. Antes de desativar um dispositivo, é necessário excluir todos os contêineres de volumes (e os próprios volumes) associados ao dispositivo. Você pode excluir os contêineres de volume somente depois que tiver excluído os backups associados.
2. Desative o dispositivo como se segue:
   
   1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione o dispositivo que você deseja desativar e clique em **Desativar** na parte inferior da página.
   2. Será exibida uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativação começará e levará alguns minutos para ser concluído.
3. Após a desativação, você pode excluir o dispositivo completamente. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. Use as etapas a seguir para excluir o dispositivo:
   
   1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione um dispositivo desativado que você deseja excluir.
   2. Na parte inferior da página, clique em **Excluir**.
   3. Será solicitada a sua confirmação. Clique em **Sim** para continuar.
      
      Pode levar alguns minutos para o serviço ser excluído.

## <a name="deactivate-and-retain-data"></a>Desativar e reter dados
Se você estiver interessado em excluir o dispositivo, mas quiser manter os dados, conclua as etapas a seguir.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados
1. Desative o dispositivo. Todos os contêineres de volume e os instantâneos do dispositivo serão mantidos.
   
   1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione o dispositivo que você deseja desativar e clique em **Desativar** na parte inferior da página.
   2. Será exibida uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativação começará e levará alguns minutos para ser concluído.
2. Agora é possível realizar failover dos contêineres de volume e dos instantâneos associados. Para procedimentos, vá para [Failover e recuperação de desastres para o seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).
3. Após a desativação e failover, você poderá excluir o dispositivo completamente. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. Conclua as etapas a seguir para excluir o dispositivo:
   
   1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione um dispositivo desativado que você deseja excluir.
   2. Na parte inferior da página, clique em **Excluir**.
   3. Será solicitada a sua confirmação. Clique em **Sim** para continuar.
      
      Pode levar alguns minutos para o serviço ser excluído.

## <a name="deactivate-and-delete-a-virtual-device"></a>Desativar e excluir um dispositivo virtual
Em um dispositivo virtual StorSimple, a desativação desaloca a máquina virtual. Você pode excluir a máquina virtual e os recursos criados quando ela foi provisionada. Depois que o dispositivo virtual for desativado, ele não poderá ser restaurado ao estado anterior. 

A desativação resulta nas seguintes ações:

* O dispositivo virtual StorSimple é removido.
* O OSDisk e Discos de Dados criados para o dispositivo virtual do StorSimple são removidos.
* O serviço hospedado e a rede virtual criados durante o provisionamento são mantidos. Se você não estiver usando as entidades, deverá excluí-las manualmente.
* Instantâneos de nuvem criados pelo dispositivo virtual StorSimple são mantidos.

## <a name="next-steps"></a>Próximas etapas
* Para restaurar os dispositivos desativados para os padrões de fábrica, acesse [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obter assistência técnica, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).
* Para saber como usar o serviço StorSimple Manager, acesse [Usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md). 

