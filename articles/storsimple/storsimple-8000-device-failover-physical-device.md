---
title: Failover do StorSimple e recuperação de desastre para um dispositivo físico StorSimple da série 8000 | Microsoft Docs
description: Saiba como fazer o failover do dispositivo físico StorSimple da série 8000 para outro dispositivo físico.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577138"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Fazer failover para um dispositivo físico StorSimple da série 8000

## <a name="overview"></a>Visão geral

Este tutorial descreve as etapas necessárias para fazer failover de um dispositivo físico StorSimple da série 8000 para outro dispositivo físico StorSimple em caso de desastre. O StorSimple usa o recurso de failover de dispositivo para migrar os dados de um dispositivo de origem físico no datacenter para outro dispositivo físico. As diretrizes neste tutorial se aplicam a dispositivos físicos StorSimple da série 8000 que executam versões de software com Atualização 3 e posterior.

Para saber mais sobre o failover de dispositivo e como ele é usado para recuperação de um desastre, acesse [Failover e recuperação de desastre para dispositivos StorSimple da série 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para fazer failover de um dispositivo físico StorSimple para um Dispositivo de Nuvem StorSimple, acesse [Fazer failover para um Dispositivo de Nuvem StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Para fazer failover de um dispositivo físico para ele próprio, acesse [Fazer failover para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Pré-requisitos

- Não deixe de revisar as considerações de failover de dispositivo. Para obter mais informações, acesse [Considerações comuns para failover de dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Você deve ter um dispositivo físico StorSimple da série 8000 implantado no datacenter. O dispositivo deve executar a Atualização 3 ou versão mais recente do software. Para saber mais, vá até [Implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Etapas para fazer failover para um dispositivo físico

Execute as seguintes etapas para restaurar seu dispositivo para um dispositivo físico de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem. Para saber mais, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, vá até a lista de dispositivos conectados ao seu serviço.
    ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecione e clique no dispositivo de origem. O dispositivo de origem tem os contêineres de volume para os quais você deseja fazer failover. Acesse **Configurações > Contêineres de Volume**.
4. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume, clique com o botão direito em **Deixar Offline** para deixar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.
5. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.
6. Retorne para a folha **Dispositivos**. Na barra de comandos, clique em **Fazer failover**.
    ![Clique em Fazer failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Na folha **Fazer failover**, execute as seguintes etapas:
   
   1. Clique em **Fonte**. Os contêineres de volume com volumes associados a instantâneos de nuvem são exibidos. Somente os contêineres exibidos estão qualificados para failover. Na lista de contêineres de volume, selecione os contêineres de volume para failover. **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**

       ![Selecionar a origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Clique em **Destino**. Para os contêineres de volume selecionados na etapa anterior, selecione um dispositivo de destino na lista suspensa de dispositivos disponíveis. Somente os dispositivos que têm capacidade suficiente para acomodar os contêineres de volume de origem são exibidos na lista.

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por fim, revise todas as configurações de failover em **Resumo**. Depois de revisar as configurações, marque a caixa de seleção que indica se os volumes nos contêineres de volume selecionados estão offline. Clique em **OK**.

       ![Examinar as configurações de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. O StorSimple cria um trabalho de failover. Clique na notificação do trabalho para monitorar o trabalho de failover pela folha **Trabalhos**.

    Se o contêiner de volume que passou por failover tiver volumes locais, você verá os trabalhos de restauração individuais para cada volume local (não para volumes em camadas) no contêiner. Esses trabalhos de restauração podem levar algum tempo para ser concluídos. É provável que o trabalho de failover possa ser concluído anteriormente. Tais volumes terão garantias locais somente depois que os trabalhos de restauração forem concluídos.

    ![Monitorar o trabalho de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Após a conclusão do failover, acesse a folha **Dispositivos**.
   
   1. Selecione o dispositivo que foi usado como o dispositivo de destino para o processo de failover.

       ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Acesse a folha **Contêineres de Volume**. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem ser listados.

       ![Exibir contêineres de volume de destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Próximas etapas

* Depois de realizar um failover, talvez seja necessário [desativar ou excluir seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como usar o serviço do Gerenciador de Dispositivos do StorSimple, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

