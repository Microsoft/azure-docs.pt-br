---
title: Excluir volumes ou replicações de volume para Azure NetApp Files replicação entre regiões | Microsoft Docs
description: Descreve como excluir uma conexão de replicação que não é mais necessária entre os volumes de origem e de destino.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95249879"
---
# <a name="delete-volume-replications-or-volumes"></a>Excluir volumes ou replicações de volume

Este artigo descreve como excluir as replicações de volume. Ele também descreve como excluir o volume de origem ou de destino.

## <a name="delete-volume-replications"></a>Excluir replicações de volume

Você pode encerrar a conexão de replicação entre os volumes de origem e de destino excluindo a replicação de volume. Você deve excluir a replicação do volume de destino. A operação de exclusão remove apenas a autorização para replicação; Ele não remove a origem ou o volume de destino. 

1. Verifique se o emparelhamento de replicação foi quebrado antes de excluir a replicação de volume. Para interromper o emparelhamento de replicação: 

    1. Selecione o volume de *destino* . Clique em **replicação** em serviço de armazenamento.  

    2.  Verifique os campos a seguir antes de continuar:  
        * Verifique se o estado do espelho mostra ***espelhado***.   
            Não tente interromper o emparelhamento de replicação se o estado do espelho mostrar não *inicializado*.
        * Verifique se o status da relação mostra ***ocioso***.   
            Não tente interromper o emparelhamento de replicação se o status da relação mostrar *transferência*.   

        Consulte [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md). 

    3.  Clique em **interromper emparelhamento**.  

    4.  Digite **Sim** quando solicitado e clique em **interromper**. 

        ![Interromper emparelhamento de replicação](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Para excluir a replicação de volume, selecione **replicação** no volume de origem ou de destino.  

2. Clique em **Excluir**.    

3. Confirme a exclusão digitando **Sim** e clicando em **excluir**.   

    ![Excluir replicação](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Excluir volumes de origem ou de destino

Se você quiser excluir o volume de origem ou de destino, deverá executar as etapas a seguir na ordem descrita. Caso contrário, `Volume with replication cannot be deleted` ocorrerá o erro.  

1. No volume de destino, [exclua a replicação de volume](#delete-volume-replications).   

2. Exclua o volume de origem ou destino conforme necessário clicando com o botão direito do mouse no nome do volume e selecione **excluir**.   

    ![Captura de tela que mostra o menu do clique com o botão direito do mouse em um volume.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Próximas etapas  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Exibir o status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)

