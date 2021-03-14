---
title: Exclusão do conector ITSM e a ação associada a ele
description: Este artigo fornece uma explicação de como excluir o conector de ITSM e os grupos de ação associados a ele.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036479"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Exclusão de conectores ITSM não utilizados

O processo de exclusão do conector não utilizado contém duas fases:

1. Exclusão das ações associadas: todas as ações associadas ao conector ITSM devem ser excluídas. Isso deve ser feito para que não haja ações sem conector que possam causar erros em sua assinatura.

2. Exclusão do conector ITSM não utilizado.

## <a name="deletion-of-the-associated-actions"></a>Exclusão das ações associadas

1. Para localizar o grupo de ações, você deve ir para a  ![ captura de tela "monitorar" da seleção do monitor.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selecione a  ![ captura de tela "alertas" da seleção de alertas.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Selecione a captura de tela "Gerenciar ações"  ![ da seleção gerenciar ações.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Selecione todos os conectores de ITSM que estão conectados à  ![ captura de tela do Cherwell de conectores de ITSM que estão conectados ao Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Exclua a  ![ captura de tela do grupo de ações da exclusão do grupo de ações.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Exclusão do conector ITSM não utilizado

1. Você deve pesquisar e selecionar a "equipe de ti" na  ![ tela de pesquisa da parte superior da pesquisa e selecionar "sua escrivaninha" la.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Selecione "conexões de ITSM" e selecione a  ![ captura de tela do conector do Cherwell de conectores de ITSM do Cherwell.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Selecione a  ![ captura de tela "excluir" da exclusão do conector de ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)
