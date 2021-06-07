---
title: Tópico habilitar identidade gerenciada no sistema de grade de eventos do Azure
description: Este artigo descreve como habilitar a identidade de serviço gerenciada para um tópico do sistema de grade de eventos do Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630336"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Tópico atribuir uma identidade gerenciada pelo sistema a um sistema de grade de eventos
Neste artigo, você aprenderá a habilitar a identidade gerenciada pelo sistema para um tópico existente do sistema de grade de eventos. Para saber mais sobre identidades gerenciadas, confira [o que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Atualmente, você não pode habilitar uma identidade gerenciada pelo sistema ao criar um novo tópico do sistema, ou seja, ao criar uma assinatura de evento em um recurso do Azure que dá suporte a tópicos do sistema. 


## <a name="use-azure-portal"></a>Usar o portal do Azure
O procedimento a seguir mostra como habilitar a identidade gerenciada pelo sistema para um tópico do sistema. 

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Procure **Tópicos do sistema de grade de eventos** na barra de pesquisa na parte superior.
3. Selecione o **tópico do sistema** para o qual você deseja habilitar a identidade gerenciada. 
4. Selecione **identidade** no menu à esquerda. Você não vê essa opção para um tópico do sistema que está no local global. 
5. Ative a opção para **habilitar a identidade** . 
1. Selecione **salvar** na barra de ferramentas para salvar a configuração. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Página de identidade para um tópico do sistema"::: 
1. Selecione **Sim** na mensagem de confirmação. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Tópico atribuir identidade a um sistema-confirmação"::: 
1. Confirme que você vê a ID de objeto da identidade gerenciada atribuída pelo sistema e veja um link para atribuir funções. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Tópico atribuir identidade a um sistema-concluído"::: 

## <a name="global-azure-sources"></a>Fontes globais do Azure
Você pode habilitar a identidade gerenciada pelo sistema somente para os recursos regionais do Azure. Você não pode habilitá-lo para tópicos do sistema associados a recursos globais do Azure, como assinaturas do Azure, grupos de recursos ou mapas do Azure. Os tópicos do sistema para essas fontes globais também não estão associados a uma região específica. Você não vê a página **identidade** do tópico do sistema cujo local está definido como **global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Tópico do sistema com local definido como global"::: 



## <a name="next-steps"></a>Próximas etapas
Adicione a identidade a uma função apropriada (por exemplo, remetente de dados do barramento de serviço) no destino (por exemplo, uma fila do barramento de serviço). Para obter etapas detalhadas, consulte [Adicionar identidade às funções do Azure em destinos](add-identity-roles.md). 