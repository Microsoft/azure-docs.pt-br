---
title: Failover e recuperação de desastre do StorSimple para dispositivos da série 8000 | Microsoft Docs
description: Saiba como fazer failover de seu dispositivo StorSimple para o mesmo dispositivo.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577274"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Fazer failover de seu dispositivo físico StorSimple para o próprio dispositivo

## <a name="overview"></a>Visão geral

Este tutorial descreve as etapas necessárias para fazer failover de um dispositivo StorSimple da série 8000 para o próprio dispositivo em caso de desastre. O StorSimple usa o recurso de failover de dispositivo para migrar os dados de um dispositivo de origem físico no datacenter para outro dispositivo físico. As diretrizes neste tutorial se aplicam a dispositivos físicos StorSimple da série 8000 que executam versões de software com Atualização 3 e posterior.

Para saber mais sobre o failover de dispositivo e como ele é usado para recuperação de um desastre, acesse [Failover e recuperação de desastre para dispositivos StorSimple da série 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para fazer failover de um dispositivo físico para outro, acesse [Fazer failover para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para fazer failover de um dispositivo físico StorSimple para um Dispositivo de Nuvem StorSimple, acesse [Fazer failover para um Dispositivo de Nuvem StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Pré-requisitos

- Não deixe de revisar as considerações de failover de dispositivo. Para obter mais informações, acesse [Considerações comuns para failover de dispositivo](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Etapas para fazer failover para o mesmo dispositivo

Execute as etapas a seguir se precisar fazer failover para o mesmo dispositivo.

1. Tirar instantâneos de nuvem de todos os volumes em seu dispositivo. Para saber mais, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Redefina o dispositivo para os padrões de fábrica. Siga as instruções detalhadas em [como redefinir um dispositivo StorSimple para as configurações padrões de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Acesse o serviço do Gerenciador de Dispositivos do StorSimple Device e clique em **Dispositivos**. Na folha **Dispositivos**, o antigo dispositivo deverá aparecer como **Offline**.

    ![Dispositivo de origem offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configure o seu dispositivo e registre-o novamente no serviço do Gerenciador de Dispositivos do StorSimple. Os dispositivos recém-registrados devem aparecer como **Pronto para ser configurado**. O nome do novo dispositivo é o mesmo que o do dispositivo antigo, porém acrescido com um número para indicar que o dispositivo foi redefinido para o padrão de fábrica e registrado novamente.

    ![Dispositivo recém-registrado pronto para ser configurado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Conclua a configuração do novo dispositivo. Para obter mais informações, acesse [Etapa 4: Instalação mínima do dispositivo concluída](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na folha **Dispositivos**, o status do dispositivo muda para **Online**.

   > [!IMPORTANT]
   > **Conclua primeiramente a configuração mínima, caso contrário a recuperação de desastre pode falhar.**

    ![Dispositivo recém-registrado online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecione o dispositivo antigo (status offline) e, na barra de comandos, clique em **Fazer failover**. Na folha **Fazer failover**, selecione o dispositivo antigo como a origem e especifique o dispositivo de destino como o dispositivo recém-registrado.

    ![Resumo de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Para obter instruções detalhadas, consulte Failover para outro dispositivo físico.

7. É criado um trabalho de restauração de dispositivo para que você possa monitorar na folha **Trabalhos**.

8. Depois que o trabalho for concluído com êxito, acesse o novo dispositivo e navegue para a folha **Contêineres de volume**. Verifique se todos os contêineres de volume do antigo dispositivo foram migrados para o novo dispositivo.

   ![Contêineres de volume migrados](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Após a conclusão do failover, você pode desativar e excluir o antigo dispositivo do portal. Selecione o antigo dispositivo (offline), clique com o botão direito do mouse e selecione **Desativar**. Depois que o dispositivo for desativado, seu status será atualizado.

     ![Dispositivo de origem desativado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecione o dispositivo desativado, clique com o botão direito do mouse e selecione **Excluir**. Isso exclui o dispositivo da lista de dispositivos.

    ![Dispositivo de origem excluído](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Próximas etapas

* Depois de realizar um failover, talvez seja necessário [desativar ou excluir seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como usar o serviço do Gerenciador de Dispositivos do StorSimple, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

