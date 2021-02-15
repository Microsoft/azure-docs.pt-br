---
title: Logs de atividade em Azure DevTest Labs | Microsoft Docs
description: Este artigo fornece etapas para exibir os logs de atividade para Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 24b5eb25f1c8c9044ad6e77545381a43a11714f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095659"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Exibir logs de atividade para laboratórios no Azure DevTest Labs 
Depois de criar um ou mais laboratórios, você provavelmente desejará monitorar como e quando seus laboratórios serão acessados, modificados e gerenciados e por quem. O Azure DevTest Labs usa Azure Monitor, especificamente **logs de atividades**, para fornecer informações dessas operações em relação aos laboratórios. 

Este artigo explica como exibir os logs de atividade de um laboratório no Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Exibir o log de atividades de um laboratório

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** na seção **DEVOPS**. Se você selecionar * (estrela) próximo a **DevTest Labs** na seção **DEVOPS**. Essa ação adicionará o **DevTest Labs** no menu de navegação esquerdo para que você possa acessá-lo facilmente da próxima vez. Em seguida, será possível selecionar **DevTest Labs** no menu de navegação esquerdo.

    ![Todos os serviços – selecionar DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Na lista de laboratórios, selecione o laboratório.
1. Na home page do laboratório, selecione **configurações e políticas** no menu à esquerda. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Selecione configuração e políticas no menu à esquerda":::
1. Na página **configuração e políticas** , selecione **log de atividades** no menu à esquerda em **gerenciar**. Você deve ver entradas para operações realizadas no laboratório. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Selecione configuração e políticas no menu à esquerda":::    
1. Selecione um evento para ver os detalhes sobre ele. Na página **Resumo** , você vê informações como nome da operação, carimbo de data/hora e quem fez a operação. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Selecione configuração e políticas no menu à esquerda":::        
1. Alterne para a guia **JSON** para ver mais detalhes. No exemplo a seguir, você pode ver o nome da VM e a operação realizada na VM (parada).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Selecione configuração e políticas no menu à esquerda":::           
1. Alterne para a guia **histórico de alterações (versão prévia)** para ver o histórico de alterações. No exemplo a seguir, você verá a alteração feita na VM. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Selecione configuração e políticas no menu à esquerda":::             
1. Selecione a alteração na lista histórico de alterações para ver mais detalhes sobre a alteração. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Selecione configuração e políticas no menu à esquerda":::             

Para obter mais informações sobre logs de atividade, consulte [log de atividades do Azure](../azure-monitor/platform/activity-log.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como definir **alertas** em logs de atividade, consulte [criar alertas](create-alerts.md).
- Para saber mais sobre os logs de atividade, consulte  [log de atividades do Azure](../azure-monitor/platform/activity-log.md).

