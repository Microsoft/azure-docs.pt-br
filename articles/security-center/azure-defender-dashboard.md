---
title: Painel do Azure defender e seus recursos
description: Saiba mais sobre os recursos do painel do Azure defender.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096897"
---
# <a name="the-azure-defender-dashboard"></a>O painel do Azure defender

O painel do Azure defender fornece:

- Visibilidade da cobertura do Azure defender em seus diferentes tipos de recursos
- Links para configurar recursos de proteção avançada contra ameaças
- O estado de integração e a instalação do agente
- Alertas de detecção de ameaças do Azure defender 

Para acessar o painel do Azure defender, selecione **Azure defender** na seção segurança de nuvem do menu da central de segurança.

## <a name="whats-shown-on-the-dashboard"></a>O que é mostrado no painel?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Um exemplo do painel do Azure Defender" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

O painel inclui as seguintes seções:

1. **Cobertura do Azure defender** -aqui você pode ver os tipos de recursos que estão em sua assinatura e qualificados para proteção pelo Azure defender. Sempre que relevante, você também terá a opção de atualizar. Se você quiser atualizar todos os recursos qualificados possíveis, selecione **atualizar tudo**.

2. **Alertas de segurança** – quando o Azure defender detecta uma ameaça em qualquer área do ambiente, ele gera um alerta. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta. A seleção de qualquer lugar nesse grafo abre a **página alertas de segurança**.

3. **Proteção avançada** – o Azure defender inclui muitos recursos avançados de proteção contra ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede e muito mais. Nesta seção proteção avançada, você pode ver o status dos recursos em suas assinaturas selecionadas para cada uma dessas proteções. Selecione qualquer um deles para ir diretamente para a área de configuração para esse tipo de proteção.

4. **Informações** – este painel contínuo de notícias, leitura sugerida e alertas de prioridade alta fornece informações sobre a central de segurança sobre o pressionamento de assuntos de segurança que são relevantes para você e sua assinatura. Seja uma lista de alta gravidade CVEs descoberta em suas VMs por uma ferramenta de análise de vulnerabilidade, ou uma nova postagem de blog por um membro da equipe da central de segurança, você a encontrará aqui no painel insights do seu **painel do Azure defender**.




## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o painel do Azure defender. 

Para saber mais sobre o Azure defender, confira [introdução ao Azure defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](enable-azure-defender.md)