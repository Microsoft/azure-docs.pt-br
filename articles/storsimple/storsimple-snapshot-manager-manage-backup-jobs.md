---
title: Trabalhos de backup do StorSimple Snapshot Manager | Microsoft Docs
description: "Descreve como usar o snap-in StorSimple Snapshot Manager MMC para exibir e gerenciar trabalhos de backup agendados, em execução e concluídos."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Usar o StorSimple Snapshot Manager para exibir e gerenciar trabalhos de backup

## <a name="overview"></a>Visão geral
O nó **Trabalhos** no painel **Escopo** mostra as tarefas de backup **Agendado**, **Últimas 24 horas** e **Em execução** iniciadas interativamente ou por uma política configurada. 

Este tutorial explica como você pode usar o nó **Trabalhos** para exibir informações sobre os trabalhos de backup agendados, recentes e em execução no momento. (A lista de trabalhos e as informações correspondentes aparece no painel **Resultados**.) Além disso, você pode clicar com o botão direito em um trabalho listado e ver um menu de contexto que lista as ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver trabalhos agendados
Use o procedimento a seguir para exibir os trabalhos de backup agendados.

#### <a name="to-view-scheduled-jobs"></a>Para exibir os trabalhos agendados
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Escopo**, expanda o nó **Trabalhos** e clique em **Agendado**. As seguintes informações aparecem no painel **Resultados** :
   
   * **Nome** – o nome do instantâneo agendado
   * **Próxima execução** – a data e hora do próximo instantâneo agendado
   * **Última execução** – a data e hora do instantâneo agendado mais recente
     
     > [!NOTE]
     > Para instantâneos únicos, a **Próxima execução** e a **Última execução** serão iguais.
     
     ![Trabalhos de backup agendados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito no nome do trabalho no painel **Resultados** e selecione uma das opções de menu.

## <a name="view-recent-jobs"></a>Exibir trabalhos recentes
Use o procedimento a seguir para exibir os trabalhos de backup e restauração que foram concluídos nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para exibir trabalhos recentes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Trabalhos** e clique em **Últimas 24 horas**. O painel **Resultados** mostra os trabalhos de backup para as últimas 24 horas (até um máximo de 64 trabalhos). As seguintes informações aparecem no painel **Resultados**, dependendo das opções de **Exibição** especificadas:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e a hora em que o instantâneo foi iniciado.
   * **Parado** – a data e a hora em que o instantâneo foi concluído ou encerrado.
   * **Decorrido** – a quantidade de tempo entre os horários **Iniciado** e **Parado**.
   * **Status** – o estado do trabalho concluído recentemente. **Sucesso** indica que o backup foi criado com êxito. **Falha** indica que o trabalho não foi executado com êxito.
   * **Informações** – o motivo da falha.
   * **Bytes processados (MB)** – a quantidade de dados do grupo de volumes que foram processados (em MB). 
     
     ![Trabalhos executados nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito no nome do trabalho no painel **Resultados** e selecione uma das opções de menu.
   
    ![Excluir um trabalho](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Exibir trabalhos em execução
Use o procedimento a seguir para exibir os trabalhos que estão sendo executados.

#### <a name="to-view-currently-running-jobs"></a>Para exibir os trabalhos em execução
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Trabalhos** e clique em **Em execução**. Dependendo as opções de **Exibição** especificadas, as seguintes informações aparecem no painel **Resultados**:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e a hora em que o instantâneo foi iniciado.
   * **Ponto de verificação** – a ação atual do backup.
   * **Status** – o percentual de conclusão.
   * **Decorrido** – a quantidade de tempo decorrido desde que o backup foi iniciado. 
   * **Taxa de transferência média (MB)** – proporção do total de bytes de dados processados em relação ao total de bytes do tempo total gasto para processamento (MBs).
   * **Bytes processados (MB)** – total de bytes de dados processados (em MBs).
   * **Bytes gravados (MB)** – total de bytes de dados gravados (em MBs). Ela inclui os dados, bem como os metadados e, portanto, é geralmente maior que os Bytes Processados.
     
     ![Trabalhos em execução no momento](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito no nome do trabalho no painel **Resultados** e selecione uma das opções de menu.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o StorSimple Snapshot Manager para gerenciar o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md).

