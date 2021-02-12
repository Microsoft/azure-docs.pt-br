---
title: Protegendo seus recursos de rede na central de segurança do Azure
description: Este documento aborda recomendações na Central de Segurança do Azure que ajudam a proteger os recursos de rede do Azure e a ficar em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 366a9a63e4577a9b8470faec68f8d4a25114dd48
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133694"
---
# <a name="protect-your-network-resources"></a>Proteja seus recursos de rede
A Central de Segurança do Azure analisa continuamente o estado de segurança de seus recursos do Azure para as práticas recomendadas de segurança de rede. Quando o Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que guiam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Para obter uma lista completa das recomendações de rede, consulte [recomendações de rede](recommendations-reference.md#recs-networking).

Este artigo aborda recomendações que se aplicam a seus recursos do Azure de uma perspectiva de segurança de rede. Centro de recomendações de rede sobre firewalls da próxima geração, grupos de segurança de rede, acesso de VM JIT, regras de tráfego de entrada excessivamente permissivas e muito mais. Para obter uma lista de recomendações de rede e ações de correção, consulte [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

Os recursos de **rede** da central de segurança incluem: 

- Mapa de rede (requer o Azure defender)
- [Fortalecimento de rede adaptável](security-center-adaptive-network-hardening.md) (requer o Azure defender)
- Recomendações de segurança de rede
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Exibir seus recursos de rede e suas recomendações

Na [página inventário de ativos](asset-inventory.md), use o filtro tipo de recurso para selecionar os recursos de rede que você deseja investigar:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Tipos de recurso de rede de inventário de ativos" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Mapa de rede

O mapa de rede interativo fornece uma visualização gráfica com sobreposições de segurança, fornecendo recomendações e insights para proteger seus recursos de rede. Usando o mapa, você pode ver a topologia de rede das cargas de trabalho do Azure, as conexões entre as máquinas virtuais e as sub-redes e a capacidade de detalhar o mapa em recursos específicos e as recomendações desses recursos.

Para abrir o mapa de rede:

1. No menu da central de segurança, abra o painel do Azure defender e selecione **mapa de rede**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Abrindo o mapa de rede no painel do Azure defender" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Selecione o menu **camadas** , escolha **topologia**.
 
A visualização padrão do mapa de topologia é exibida:

- Assinaturas selecionadas no Azure. O mapa suporta várias assinaturas.
- VMs, sub-redes e VNets do tipo de recurso do Resource Manager (não há suporte para recursos clássicos do Azure)
- VNets emparelhados
- Somente recursos que possuem [recomendações de rede](security-center-recommendations.md) com alta ou média gravidade  
- Recursos de voltado para Internet
- O mapa é otimizado para as assinaturas selecionadas no Azure. Se você modificar sua seleção, o mapa será recalculado e otimizado novamente com base nas novas configurações.  

[![Mapa de topologia de rede](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Noções básicas sobre o mapa de rede

O mapa de rede pode mostrar os recursos do Azure em uma exibição de **topologia** e uma exibição de **tráfego** . 

### <a name="the-topology-view"></a>O modo de exibição de topologia

Na visualização **Topologia** do mapa de rede, você pode visualizar os seguintes insights sobre seus recursos de rede:

- No círculo interno, você pode ver todas as Vnets dentro de suas assinaturas selecionadas, o próximo círculo é todas as sub-redes, o círculo externo é todas as máquinas virtuais.
- As linhas que conectam os recursos no mapa informam quais recursos estão associados entre si e como sua rede do Azure está estruturada. 
- Use os indicadores de gravidade para obter rapidamente uma visão geral de quais recursos têm recomendações abertas da Central de Segurança.
- Você pode clicar em qualquer um dos recursos para analisar os detalhes desse recurso e suas recomendações diretamente e no contexto do mapa de rede.  
- Se houver muitos recursos exibidos no mapa, o Azure Security Center usará seu algoritmo proprietário para agrupar seus recursos de forma inteligente, destacando os recursos que estão no estado mais crítico e as recomendações de maior gravidade. 

Como o mapa é interativo e dinâmico, todos os nós são clicáveis e a exibição pode ser alterada com base nos filtros:

1. Você pode modificar o que vê no mapa de rede usando os filtros na parte superior. Você pode se concentrar o mapa com base em:

   -  **Integridade da segurança**: você pode filtrar o mapa com base na gravidade (alta, média, baixa) dos recursos do Azure.
   - **Recomendações**: você pode selecionar quais recursos são exibidos com base nas recomendações ativas nesses recursos. Por exemplo, você pode exibir apenas os recursos para os quais a Central de Segurança recomenda que você ative os Grupos de Segurança de Rede.
   - **Zonas de rede**: por padrão, o mapa exibe apenas recursos voltados para a Internet, você também pode selecionar VMs internas.
 
2. Você pode clicar em **Redefinir** no canto superior esquerdo a qualquer momento para retornar o mapa ao seu estado padrão.

Para fazer drill down em um recurso:

1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que você selecionar. 
2. Ao passar o mouse sobre um nó no mapa, você pode exibir informações gerais sobre o recurso, incluindo assinatura, tipo de recurso e grupo de recursos.
3. Use o link para ampliar a dica de ferramenta e refocar o mapa nesse nó específico. 
4. Para refocar o mapa de um nó específico, diminua o zoom.

### <a name="the-traffic-view"></a>O modo de exibição de tráfego

A visualização **Tráfego** fornece um mapa de todo o tráfego possível entre seus recursos. Isso fornece um mapa visual de todas as regras configuradas que definem quais recursos podem se comunicar com quem. Isso permite que você veja a configuração existente dos grupos de segurança de rede e identifique rapidamente possíveis configurações de risco em suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descubra conexões indesejadas

A força dessa visão está em sua capacidade de mostrar a você essas conexões permitidas junto com as vulnerabilidades existentes, para que você possa usar essa seção transversal de dados para executar o endurecimento necessário em seus recursos. 

Por exemplo, você pode detectar duas máquinas que você não sabia que poderiam se comunicar, permitindo isolar melhor as cargas de trabalho e as sub-redes.

### <a name="investigate-resources"></a>Investigar recursos

Para fazer drill down em um recurso:

1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que você selecionar. 
2. Clique em **Tráfego** para ver a lista de possíveis tráfego de saída e de entrada no recurso - essa é uma lista abrangente de quem pode se comunicar com o recurso e com quem pode se comunicar e por meio de quais protocolos e portas. Por exemplo, quando você seleciona uma VM, todas as VMs com as quais ela pode se comunicar são mostradas e, quando você seleciona uma sub-rede, todas as sub-redes com as quais ela pode se comunicar são mostradas.

**Esses dados são baseados na análise dos Grupos de segurança de rede, bem como em algoritmos avançados de aprendizado de máquina que analisam várias regras para entender seus cruzamentos e interações.** 

[![Mapa de tráfego de rede](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

- [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](./asset-inventory.md)