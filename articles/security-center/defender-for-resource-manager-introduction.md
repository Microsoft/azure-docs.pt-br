---
title: Azure Defender para Resource Manager – benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9591dae058a19cea73d88513b7c4ff4ab8f88045
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797744"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Introdução ao Azure Defender para Resource Manager

O [Azure Resource Manager](../azure-resource-manager/management/overview.md) é o serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua conta do Azure. Use recursos de gerenciamento, como controle de acesso, bloqueios e marcas, para proteger e organizar seus recursos após a implantação.

A camada de gerenciamento de nuvem é um serviço crucial conectado a todos os seus recursos de nuvem. Por isso, ela também é um possível alvo para invasores. Sendo assim, recomendamos que as equipes de operações de segurança monitorem atentamente a camada de gerenciamento de recursos. 

O Azure Defender para Resource Manager monitora automaticamente as operações de gerenciamento de recursos em sua organização, sejam elas executadas por meio do portal do Azure, das APIs REST do Azure, da CLI do Azure ou de outros clientes programáticos do Azure. O Azure Defender executa análises de segurança avançadas a fim de detectar ameaças e alertar você sobre atividades suspeitas.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preço:|O **Azure Defender para Resource Manager** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Quais são os benefícios do Azure Defender para Resource Manager?

O Azure Defender para Resource Manager protege contra problemas que incluem:

- **Operações suspeitas de gerenciamento de recursos**, como operações de endereços IP suspeitos, desabilitação de antimalware e scripts suspeitos em execução em extensões de VM
- **Uso de kits de ferramenta de exploração**, como o Microburst ou o PowerZure
- **Movimentos laterais** da camada de gerenciamento do Azure para o plano de dados de recursos do Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagrama de visão geral do Azure Resource Manager":::

Uma lista completa dos alertas fornecidos pelo Azure Defender para Resource Manager está na [página de referência de alertas](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Como investigar alertas do Azure Defender para Resource Manager

Os alertas de segurança do Azure Defender para Resource Manager são baseados nas ameaças detectadas pelo monitoramento de operações do Azure Resource Manager. O Azure Defender usa fontes de log internas do Azure Resource Manager, bem como o log de Atividades do Azure, um log da plataforma no Azure que fornece informações sobre eventos no nível da assinatura.

Saiba mais sobre o [Log de Atividades do Azure](../azure-monitor/platform/activity-log.md).

Para investigar alertas de segurança do Azure Defender para Resource Manager:

1. Abra o log de atividades do Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Como abrir o log de atividades do Azure":::

1. Filtre os eventos para:
    - A assinatura mencionada no alerta
    - O período da atividade detectada
    - A conta de usuário relacionada (se relevante)

1. Procure atividades suspeitas.

> [!TIP]
> Para uma experiência de investigação melhor e mais avançada, transmita os logs de atividades do Azure para o Azure Sentinel, conforme descrito em [Conectar dados do log de atividades do Azure](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Resource Manager. Para ver materiais relacionados, confira o seguinte artigo: 

- Os alertas de segurança podem ser gerados pela Central de Segurança ou recebidos pela Central de Segurança de produtos de segurança diferentes. Para exportar esses alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](security-center-pricing.md#enable-azure-defender)