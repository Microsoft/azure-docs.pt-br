---
title: Controlando sua pontuação segura na central de segurança do Azure
description: Saiba mais sobre as várias maneiras de acessar e acompanhar sua pontuação segura na central de segurança do Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107303"
---
# <a name="access-and-track-your-secure-score"></a>Acesse e acompanhe sua pontuação segura

Você pode encontrar a pontuação segura geral, bem como sua pontuação por assinatura, por meio da portal do Azure ou de forma programática, conforme descrito nas seções a seguir:

> [!TIP]
> Para obter uma explicação detalhada de como suas pontuações são calculadas, consulte [cálculos – Compreendendo sua pontuação](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Obtenha sua pontuação segura no portal

A central de segurança exibe sua pontuação de forma proeminente no Portal: é o primeiro bloco principal da página Visão geral da central de segurança. Ao selecionar esse bloco, você será levado para a página de Pontuação segura dedicada, na qual verá a pontuação dividida por assinatura. Selecione uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura. 

Para recapitular, sua pontuação segura é mostrada nos locais a seguir nas páginas do portal da central de segurança.

- Em um bloco na **visão geral** da central de segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel da central de segurança":::

- Na página **Pontuação segura** dedicada, você pode ver a pontuação de segurança para sua assinatura e seus grupos de gerenciamento:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação de segurança para assinaturas na página de Pontuação segura da central de segurança":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A pontuação de segurança para grupos de gerenciamento na página de Pontuação segura da central de segurança":::

    > [!NOTE]
    > Todos os grupos de gerenciamento para os quais você não tem permissões suficientes mostrarão sua pontuação como "restrito". 

- Na parte superior da página de **recomendações** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação de segurança na página de recomendações da central de segurança":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha sua pontuação segura da API REST

Você pode acessar sua pontuação por meio da API de Pontuação segura. Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo, você pode usar a [API de pontuações seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma assinatura específica. Além disso, você pode usar a [API de controles de Pontuação segura](/rest/api/securitycenter/securescorecontrols) para listar os controles de segurança e a pontuação atual de suas assinaturas.

![Recuperando uma única Pontuação segura por meio da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Para obter exemplos de ferramentas criadas com base na API de Pontuação segura, consulte [a área de Pontuação segura da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Obtenha sua pontuação segura do grafo de recursos do Azure

O grafo de recursos do Azure fornece acesso instantâneo a informações de recursos em seus ambientes de nuvem com recursos robustos de filtragem, agrupamento e classificação. É uma maneira rápida e eficiente de consultar informações em assinaturas do Azure programaticamente ou de dentro do portal do Azure. [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acessar a pontuação segura para várias assinaturas com o grafo de recursos do Azure:

1. No portal do Azure, abra o **Gerenciador de grafo de recursos do Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Iniciando a página de recomendação do Gerenciador de gráficos de recursos do Azure * *" :::

1. Insira sua consulta do Kusto (usando os exemplos abaixo para obter diretrizes).

    - Essa consulta retorna a ID da assinatura, a pontuação atual em pontos e como porcentagem e a pontuação máxima para a assinatura. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Essa consulta retorna o status de todos os controles de segurança. Para cada controle, você obterá o número de recursos não íntegros, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Executar consulta**.


## <a name="tracking-your-secure-score-over-time"></a>Acompanhando sua pontuação segura ao longo do tempo

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Relatório de Pontuação segura ao longo do tempo na página de pastas de trabalho

A página de pastas de trabalho da central de segurança inclui um relatório pronto para acompanhar visualmente as pontuações de suas assinaturas, controles de segurança e muito mais. Saiba mais em [criar relatórios avançados e interativos dos dados da central de segurança](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Uma seção do relatório de Pontuação segura ao longo do tempo da Galeria de pastas de trabalho da central de segurança do Azure":::

### <a name="power-bi-pro-dashboards"></a>Painéis de Power BI Pro

Se você for um usuário Power BI com uma conta pro, poderá usar o painel de **Pontuação segura ao longo do tempo** Power bi para controlar sua pontuação segura ao longo do tempo e investigar as alterações.

> [!TIP]
> Você pode encontrar esse painel, bem como outras ferramentas para trabalhar de forma programática com Pontuação segura, na área dedicada da Comunidade da central de segurança do Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os dois relatórios a seguir para ajudá-lo a analisar seu status de segurança:

- **Resumo de recursos** -fornece dados resumidos sobre a integridade dos seus recursos.
- **Resumo da Pontuação de segurança** – fornece dados resumidos sobre o andamento da pontuação. Use o gráfico "Pontuação segura ao longo do tempo por assinatura" para exibir as alterações na pontuação. Se você perceber uma alteração drástica na sua pontuação, marque a tabela "alterações detectadas que podem afetar sua pontuação segura" para possíveis alterações que possam ter causado a alteração. Esta tabela apresenta recursos excluídos, recursos implantados recentemente ou recursos que seu status de segurança alterou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="A pontuação segura opcional ao longo do tempo Power BI painel para acompanhar sua pontuação segura ao longo do tempo e investigar as alterações":::


## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como acessar e acompanhar sua pontuação segura. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)
- [Exibir as ferramentas baseadas no GitHub para trabalhar de forma programática com Pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)