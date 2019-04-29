---
title: Failover do StorSimple e recuperação de desastre para um Dispositivo de Nuvem StorSimple | Microsoft Docs
description: Saiba como fazer o failover do seu dispositivo físico StorSimple da série 8000 para um dispositivo de nuvem.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584286"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Fazer failover para um Dispositivo de Nuvem StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve as etapas necessárias para fazer failover de um dispositivo físico StorSimple da série 8000 para um Dispositivo de Nuvem StorSimple em caso de desastre. O StorSimple usa o recurso de failover de dispositivo para migrar os dados de um dispositivo de origem físico no datacenter para um dispositivo de nuvem em execução no Azure. As diretrizes neste tutorial se aplicam a dispositivos físicos StorSimple da série 8000 e a dispositivos de nuvem que executam versões de software com a Atualização 3 e posterior.

Para saber mais sobre o failover de dispositivo e como ele é usado para recuperação de um desastre, acesse [Failover e recuperação de desastre para dispositivos StorSimple da série 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para fazer failover de um dispositivo físico StorSimple para outro, acesse [Fazer failover para um dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para fazer failover de um dispositivo para ele mesmo, acesse [Fazer failover para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Pré-requisitos

- Não deixe de revisar as considerações de failover de dispositivo. Para obter mais informações, acesse [Considerações comuns para failover de dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Você deve ter um Dispositivo de Nuvem StorSimple criado e configurado antes de executar este procedimento. Se estiver executando a versão de software com a Atualização 3 ou posterior, considere usar um dispositivo de nuvem 8020 para a recuperação de desastre. O modelo 8020 tem 64 TB e usa o Armazenamento Premium. Para obter mais informações, acesse [Implantar e gerenciar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Etapas para fazer failover para um dispositivo de nuvem

Execute as etapas a seguir para restaurar o dispositivo para um Dispositivo de Nuvem StorSimple de destino.

1.  Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem. Para saber mais, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na folha **Dispositivos**, vá até a lista de dispositivos conectados ao seu serviço.
    ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecione e clique no dispositivo de origem. O dispositivo de origem tem os contêineres de volume para os quais você deseja fazer failover. Acesse **Configurações > Contêineres de Volume**.

    ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume, clique com o botão direito em **Deixar Offline** para deixar o volume offline.

    ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita esse processo para todos os volumes no contêiner de volume.

     ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

7. Retorne para a folha **Dispositivos**. Na barra de comandos, clique em **Fazer failover**.

    ![Clique em Fazer failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Na folha **Fazer failover**, execute as seguintes etapas:
   
    1. Clique em **Fonte**. Selecione os contêineres de volume que passarão por failover. **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**
        ![Selecionar fonte](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Clique em **Destino**. Selecione um dispositivo de nuvem de destino na lista suspensa de dispositivos disponíveis. **Somente os dispositivos que têm capacidade suficiente para acomodar os contêineres de volume de origem são exibidos na lista.**

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Revise as configurações de failover em **Resumo** e marque a caixa de seleção que indica se os volumes nos contêineres de volume selecionados estão offline. 

        ![Examinar as configurações de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Um trabalho de failover é criado. Para monitorar o trabalho de failover, clique na notificação do trabalho.

    ![Monitorar o trabalho de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Após a conclusão do failover, volte para a folha **Dispositivos**.

    1. Selecione o dispositivo que foi usado como destino para o failover.

       ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Clique em **Contêineres de Volume**. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem ser listados.

       Se o contêiner de volume que passou por failover tem volumes fixados localmente, tais volumes passam por failover como volumes em camadas. Não há suporte para volumes fixados localmente em um Dispositivo de Nuvem StorSimple.

       ![Exibir contêineres de volume de destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Próximas etapas

* Depois de realizar um failover, talvez seja necessário [desativar ou excluir seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para obter informações sobre como usar o serviço do Gerenciador de Dispositivos do StorSimple, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

