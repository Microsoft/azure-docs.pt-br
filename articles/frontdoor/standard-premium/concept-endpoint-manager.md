---
title: 'Azure front door: Endpoint Manager'
description: Este artigo fornece uma visão geral do Gerenciador de pontos de extremidade do Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098520"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>O que é o Gerenciador de ponto de extremidade do Azure front door Standard/Premium (visualização)?

> [!NOTE]
> * Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Em busca de informações sobre o Azure Front Door? Veja a [Documentação do Azure Front Door](../front-door-overview.md).

O Endpoint Manager fornece uma visão geral dos pontos de extremidade que você configurou para sua porta frontal do Azure. Um ponto de extremidade é um agrupamento lógico de domínios e suas configurações associadas. O Endpoint Manager ajuda você a gerenciar sua coleção de pontos de extremidade para a operação CRUD (criar, ler, atualizar e excluir). Você pode gerenciar os seguintes elementos para seus pontos de extremidade por meio do Endpoint Manager:

* Domínios
* Grupos de origem
* Rotas
* Segurança

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Captura de tela do Gerenciador de pontos de extremidade sem configurações." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Gerenciador de pontos de extremidade lista quantas instâncias de cada elemento são criadas dentro de um ponto de extremidade. O status de associação para cada elemento também será exibido. Por exemplo, você pode criar vários domínios e grupos de origem e atribuir a associação entre eles com rotas diferentes.

> [!IMPORTANT]
> * O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para saber mais, confira os [**Termos de uso complementares das versões prévias do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Exibição vinculada

Com o modo de exibição vinculado no Gerenciador de pontos de extremidade, você poderia identificar facilmente a associação entre os elementos de porta frontal do Azure, como:

* Quais domínios estão associados ao ponto de extremidade atual?
* Qual grupo de origem está associado a qual domínio?
* Qual política de WAF está associada a qual domínio?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Captura de tela do Gerenciador de pontos de extremidade com configurações." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
