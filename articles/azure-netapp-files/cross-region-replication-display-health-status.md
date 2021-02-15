---
title: Exibir o status de integridade da relação de replicação Azure NetApp Files | Microsoft Docs
description: Descreve como exibir o status de replicação no volume de origem ou no volume de destino de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: a14656bc1abc42fdc3e948fdae1ca07469681d21
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249845"
---
# <a name="display-health-status-of-replication-relationship"></a>Exibir o status de integridade da relação de replicação 

Você pode exibir o status de replicação no volume de origem ou no volume de destino.  

## <a name="display-replication-status"></a>Exibir status da replicação

1. No volume de origem ou no volume de destino, clique em **replicação** em serviço de armazenamento para qualquer volume.

    As seguintes informações de status de replicação e integridade são exibidas:  
    * **Tipo de ponto de extremidade** – identifica se o volume é a origem ou o destino da replicação.
    * **Integridade** – exibe o status de integridade da relação de replicação.
    * **Estado do espelho** – mostra um dos seguintes valores:
        * Não *inicializado*:  
            Esse é o estado inicial e padrão quando uma relação de emparelhamento é criada. O estado permanece não inicializado até que a inicialização seja concluída com êxito.
        * *Espelhado*:   
            O volume de destino foi inicializado e está pronto para receber atualizações de espelhamento.
        * *Rompido*:   
            Esse é o estado depois que você quebra a relação de emparelhamento. O volume de destino é `‘RW’` e os instantâneos estão presentes.
    * **Status da relação** – mostra um dos seguintes valores: 
        * *Ocioso*:  
            Nenhuma operação de transferência está em andamento e as transferências futuras não estão desabilitadas.
        * *Transferindo*:  
            Uma operação de transferência está em andamento e as transferências futuras não estão desabilitadas.
    * **Agendamento de replicação** – mostra a frequência com que as atualizações de espelhamento incremental serão executadas quando a inicialização (cópia de linha de base) for concluída.

    * **Progresso total** – mostra a quantidade total de dados em bytes transferidos para a operação de transferência atual. Esse valor é o bits real transferido e pode ser diferente do espaço lógico que os volumes de origem e de destino relatam.  

    ![Status de integridade da replicação](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> A relação de replicação mostrará o status de integridade como não *íntegro* se os trabalhos de replicação anteriores não forem concluídos. Esse status é resultado de grandes volumes sendo transferidos com uma janela de transferência mais baixa (por exemplo, um tempo de transferência de dez minutos para um volume grande). Nesse caso, o status da relação mostra *transferência* e o status de integridade mostra não *íntegro*.

## <a name="next-steps"></a>Próximas etapas  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Gerenciar recuperação de desastre](cross-region-replication-manage-disaster-recovery.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Excluir volumes ou replicações de volume](cross-region-replication-delete.md)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)

