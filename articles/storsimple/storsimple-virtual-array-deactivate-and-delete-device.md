---
title: Desativar e excluir uma Matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve como remover o dispositivo StorSimple do serviço primeiro desativando-o e então excluindo-o.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580494"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Desativar e excluir uma Matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Ao desativar uma Matriz Virtual do StorSimple, você dividirá a conexão entre o dispositivo e o serviço Gerenciador de Dispositivo do StorSimple correspondente. Este tutorial explica como:

* Desativar um dispositivo 
* Excluir um dispositivo desativado

As informações nesse artigo aplicam-se somente a Matrizes Virtuais do StorSimple. Para obter informações sobre a série 8000, acesse como [desativar ou excluir um dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando desativar?

A desativação é uma operação PERMANENTE e não pode ser desfeita. Não é possível registrar um dispositivo desativado com o serviço Gerenciador de Dispositivos do StorSimple. Talvez seja necessário desativar e excluir uma Matriz Virtual do StorSimple nos seguintes cenários:

* **Failover planejado** : Seu dispositivo está online e você planeja efetuar failover do dispositivo. Talvez seja necessário fazer isso se você estiver planejando atualizar para um dispositivo maior. Após a transferência de propriedade dos dados e a conclusão do failover, o dispositivo de origem será excluído automaticamente.
* **Failover não planejado** : Seu dispositivo está offline e você precisa fazer failover de dispositivo. Esse cenário pode ocorrer durante um desastre, quando há uma interrupção no datacenter e o dispositivo primário fica inativo. Você planeja fazer failover de dispositivo para um dispositivo secundário. Após a transferência de propriedade dos dados e a conclusão do failover, o dispositivo de origem será excluído automaticamente.
* **Encerrar** : Você deseja encerrar o dispositivo. Para isso, primeiro você precisa desativar o dispositivo e, depois, excluí-lo. Ao desativar um dispositivo, não é mais possível acessar os dados que foram armazenados localmente. Você só pode acessar e recuperar os dados armazenados na nuvem. Se você planeja manter os dados do dispositivo após a desativação, você deve tirar um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Esse instantâneo permitirá que você recupere todos os dados em um estágio posterior.

## <a name="deactivate-a-device"></a>Desativar um dispositivo

Para desativar seu dispositivo, execute as seguintes etapas.

#### <a name="to-deactivate-the-device"></a>Para desativar o dispositivo

1. Em seu serviço, acesse **Gerenciamento > Dispositivos**. Na folha **Dispositivos**, clique e selecione o dispositivo que você deseja desativar.
   
    ![Selecione o dispositivo para desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Na folha **Painel do dispositivo**, clique em **...Mais** e, na lista, selecione **Desativar**.
   
    ![Clique em desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Na folha **Desativar**, digite o nome do dispositivo e, em seguida, clique em **Desativar**. 
   
    ![Confirme desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    O processo de desativação começará e levará alguns minutos para ser concluído.
   
    ![Desativação em andamento](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Após a desativação, a lista de dispositivos será atualizada.
   
    ![Desativação concluída](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Agora você pode excluir este dispositivo.

## <a name="delete-the-device"></a>Excluir o dispositivo

Para desativar um dispositivo, é necessário desativá-lo primeiro. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. Os dados associados ao dispositivo permanecerão na nuvem. Esses dados acumulam encargos.

Para excluir o dispositivo, execute as etapas a seguir.

#### <a name="to-delete-the-device"></a>Para excluir o dispositivo

1. No Gerenciador de Dispositivos do StorSimple, acesse **Gerenciamento > Dispositivos**. Na folha **Dispositivos**, selecione um dispositivo desativado que você deseja excluir.
2. Na folha **Painel do dispositivo**, clique em **...Mais** e, em seguida, clique em **Excluir**.
   
   ![Selecione o dispositivo para excluir](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Na folha **Excluir**, digite o nome de seu dispositivo para confirmar a exclusão e, em seguida, clique em **Excluir**. A exclusão do dispositivo não excluirá os dados de nuvem associados ao dispositivo. 
   
   ![Confirmar exclusão](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A exclusão começará e levará alguns minutos para ser concluída.
   
   ![Exclusão em andamento](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Após a exclusão do dispositivo, você poderá exibir a lista atualizada de dispositivos.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como realizar o failover, acesse [Failover e recuperação de desastre do sua Matriz Virtual do StorSimple](storsimple-virtual-array-failover-dr.md).

* Para saber como usar o serviço Gerenciador de Dispositivo do StorSimple, acesse [Usar o serviço Gerenciador de Dispositivo do StorSimple para administrar a sua Matriz Virtual do StorSimple](storsimple-virtual-array-manager-service-administration.md). 

