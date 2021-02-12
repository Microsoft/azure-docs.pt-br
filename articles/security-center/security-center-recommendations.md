---
title: Recomendações de segurança na Central de Segurança do Azure
description: Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526478"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança na Central de Segurança do Azure 

Este tópico explica como exibir e entender as recomendações na central de segurança do Azure para ajudá-lo a proteger seus recursos do Azure.


## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

As recomendações são ações a serem executadas para proteger e proteger seus recursos. 

Cada recomendação fornece:

- Uma descrição breve do problema
- As etapas de correção para executar a fim de implementar a recomendação
- Os recursos afetados

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Como a Microsoft decide o que precisa de proteção e proteção?

As recomendações da Central de Segurança baseiam-se no Azure Security Benchmark. Quase todas as recomendações têm uma política subjacente que é derivada de um requisito no parâmetro de comparação.

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem. Saiba mais sobre o [Azure Security Benchmark](../security/benchmarks/introduction.md).

Quando você estiver examinando os detalhes de uma recomendação, muitas vezes é útil poder ver a política subjacente. Para cada recomendação com suporte de uma política, use o link **Exibir definição de política** da página de detalhes de recomendação para ir diretamente para a entrada de Azure Policy para a política relevante:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página de Azure Policy para a política específica que dá suporte a uma recomendação":::

Use este link para exibir a definição de política e examinar a lógica de avaliação. 

Se você estiver examinando a lista de recomendações em nosso [Guia de referência de recomendações de segurança](recommendations-reference.md), você também verá links para as páginas de definição de política:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Acessando a página de Azure Policy para uma política específica diretamente da página de referência de recomendações da central de segurança do Azure":::

## <a name="monitor-recommendations"></a>Recomendações de monitor <a name="monitor-recommendations"></a>

A central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. 

1. No menu da central de segurança, abra a página **recomendações** para ver as recomendações aplicáveis ao seu ambiente. As recomendações são agrupadas em controles de segurança.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recomendações agrupadas por controle de segurança" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Para encontrar recomendações específicas para o tipo de recurso, gravidade, ambiente ou outros critérios que são importantes para você, use os filtros opcionais acima da lista de recomendações.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtros para refinar a lista de recomendações da central de segurança do Azure":::

1. Expanda um controle e selecione uma recomendação específica para exibir a página de detalhes de recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    1. Para obter recomendações com suporte, a barra de ferramentas superior mostra um ou todos os botões a seguir:
        - **Impor** e **negar** (consulte [evitar configurações incorretas com recomendações de impor/negar](prevent-misconfigurations.md))
        - **Exibir a definição de política** para ir diretamente para a entrada de Azure Policy para a política subjacente
    1. **Indicador de severidade**
    1. **Intervalo de atualização** (quando relevante)
    1. **Contagem de recursos isentos** se existirem isenções para essa recomendação, isso mostrará o número de recursos que foram isentos
    1. **Descrição** -uma breve descrição do problema
    1. **Etapas de correção** -uma descrição das etapas manuais necessárias para corrigir o problema de segurança nos recursos afetados. Para obter recomendações com ' correção rápida ', você pode selecionar **Exibir lógica de correção** antes de aplicar a correção sugerida aos seus recursos. 
    1. **Recursos afetados** -seus recursos são agrupados em guias:
        - **Recursos íntegros** – recursos relevantes que não são afetados ou nos quais você já corrigiu o problema.
        - **Recursos não íntegros** – recursos que ainda são afetados pelo problema identificado.
        - **Recursos não aplicáveis** – recursos para os quais a recomendação não pode dar uma resposta definitiva. A guia não aplicável também inclui os motivos para cada recurso. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Recursos não aplicáveis com motivos.":::
    1. Botões de ação para corrigir a recomendação ou disparar um aplicativo lógico.

## <a name="preview-recommendations"></a>Recomendações de visualização

As recomendações sinalizadas como **Visualização** não estão incluídas nos cálculos da sua pontuação segura.

Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::
 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para obter informações relacionadas:

- [Corrigir recomendações](security-center-remediate-recommendations.md)– saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Evitar configurações incorretas com recomendações de aplicação/negação](prevent-misconfigurations.md).
- [Automatizar respostas para gatilhos da central de segurança](workflow-automation.md)– automatizar respostas para recomendações
- [Isentar um recurso de uma recomendação](exempt-resource.md)
- [Recomendações de segurança – um guia de referência](recommendations-reference.md)