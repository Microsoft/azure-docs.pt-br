---
title: Painel principal ou página de 'visão geral' da Central de Segurança do Azure
description: Saiba mais sobre os recursos da página de visão geral da Central de Segurança
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099770"
---
# <a name="azure-security-centers-overview-page"></a>Página de visão geral da Central de Segurança do Azure

Quando você abre a Central de Segurança do Azure, a primeira página a ser exibida é a página de visão geral. 

Esse painel interativo fornece uma exibição unificada da postura de segurança das cargas de trabalho de nuvem híbrida. Além disso, ela mostra alertas de segurança, informações de cobertura, entre outros.

Você pode selecionar qualquer elemento na página para obter informações mais detalhadas.

:::image type="content" source="media/overview-page/overview.png" alt-text="Página de visão geral da Central de Segurança":::

## <a name="features-of-the-overview-page"></a>Recursos da página de visão geral

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barra superior da página de visão geral da Central de Segurança":::

A **barra de menus superior** oferece:
- **Assinaturas**: veja e filtre a lista de assinaturas selecionando este botão. A Central de Segurança ajustará a exibição para refletir a postura de segurança das assinaturas selecionadas.
- **Novidades**: abre as [notas sobre a versão](release-notes.md) para que você possa se manter atualizado com os novos recursos, as correções de bugs e as funcionalidades preteridas.
- **Números de alto nível** para as contas de nuvem conectadas, para mostrar o contexto das informações nos blocos principais abaixo. Além do número de recursos avaliados, recomendações ativas e alertas de segurança. Selecione o número de recursos avaliados para acessar o [Inventário de ativos](asset-inventory.md). Saiba mais sobre como conectar suas [contas da AWS](quickstart-onboard-aws.md) e seus [projetos do GCP](quickstart-onboard-gcp.md).


No centro da página estão **quatro blocos centrais**, cada um se vinculando a um painel dedicado para mostrar mais detalhes:
- **Classificação de segurança**: a Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. [Saiba mais](secure-score-security-controls.md).
- **Conformidade regulatória**: a Central de Segurança fornece insights sobre a sua postura de conformidade, com base em avaliações contínuas do seu ambiente do Azure. A Central de Segurança analisa fatores de risco em seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Essas avaliações são mapeadas para controles de conformidade de um conjunto compatível de padrões. [Saiba mais](security-center-compliance-dashboard.md).
- **Azure Defender**: essa é a PPCTN (plataforma de proteção de cargas de trabalho na nuvem) integrada à Central de Segurança para proteção avançada e inteligente das suas cargas de trabalho híbridas e do Azure. O bloco mostra a cobertura dos seus recursos conectados (para as assinaturas atualmente selecionadas) e os alertas recentes, codificados por cor e por severidade. [Saiba mais](azure-defender.md).
- **Gerenciador de firewall**: esse bloco mostra o status dos seus hubs e redes do [Gerenciador de Firewall do Azure](../firewall-manager/overview.md). 


O painel **Insights** oferece itens personalizados para o seu ambiente, incluindo:
- Os recursos mais atacados
- Os controles de segurança que têm o maior potencial para aumentar sua classificação de segurança
- As recomendações ativas com a maioria dos recursos afetados
- Postagens recentes no blog dos especialistas da Central de Segurança do Azure

## <a name="next-steps"></a>Próximas etapas

Esta página apresentou a página de visão geral da Central de Segurança. Para obter informações relacionadas. consulte:

- [Explore e gerencie seus recursos com as ferramentas de gerenciamento e inventário de ativos](asset-inventory.md)
- [Pontuação segura na Central de Segurança do Azure](secure-score-security-controls.md)