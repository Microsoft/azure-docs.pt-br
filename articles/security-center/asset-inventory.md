---
title: Inventário de ativos da central de segurança do Azure
description: Saiba mais sobre a experiência de gerenciamento de ativos da central de segurança do Azure, fornecendo visibilidade total sobre todos os recursos monitorados da central de segurança.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/10/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 873fdba1d24db55b3269cc2c13f0140da4a9b4e3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393337"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory"></a>Explore e gerencie seus recursos usando um inventário de ativos

A página de inventário de ativos da Central de Segurança do Azure fornece uma só página para exibição da postura de segurança dos recursos que você conectou à Central de Segurança. 

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Use esta exibição e seus filtros para resolver tais perguntas como:

- Quais das minhas assinaturas com o Azure defender habilitadas têm recomendações pendentes?
- Quais dos meus computadores com a marca ' produção ' estão faltando o agente de Log Analytics?
- Quantas minhas máquinas marcadas com uma marca específica têm recomendações pendentes?
- Quantos recursos em um grupo de recursos específico têm conclusões de segurança de um serviço de avaliação de vulnerabilidade?

As possibilidades de gerenciamento de ativos para essa ferramenta são substanciais e continuam crescendo. 

> [!TIP]
> As recomendações de segurança na página de inventário de ativos são as mesmas da página **recomendações** , mas aqui são mostradas de acordo com o recurso afetado. Para obter informações sobre como resolver recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).


## <a name="availability"></a>Disponibilidade
|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|todos os usuários|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quais são os principais recursos do inventário de ativos?
A página inventário fornece as seguintes ferramentas:

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Principais recursos da página de inventário de ativos na central de segurança do Azure" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1-resumos
Antes de definir qualquer filtro, uma faixa proeminente de valores na parte superior da exibição de inventário mostra:

- **Total de recursos**: o número total de recursos conectados à central de segurança.
- **Recursos não íntegros**: recursos com recomendações de segurança ativas. [Saiba mais sobre as recomendações de segurança](security-center-recommendations.md).
- **Recursos não monitorados**: recursos com problemas de monitoramento de agente-eles têm o agente de log Analytics implantado, mas o agente não está enviando dados ou tem outros problemas de integridade.
- **Assinaturas não registradas**: qualquer assinatura no escopo selecionado que ainda não tenha sido conectada à central de segurança do Azure.

### <a name="2---filters"></a>2-filtros
Os vários filtros na parte superior da página fornecem uma maneira de refinar rapidamente a lista de recursos de acordo com a pergunta que você está tentando responder. Por exemplo, se você quisesse responder à pergunta *quais dos meus computadores com a marca "produção" estão sem o agente de log Analytics?* você pode combinar o filtro de **monitoramento de agente** com o filtro de **marcas** .

Assim que você aplicou filtros, os valores de resumo são atualizados para se relacionar aos resultados da consulta. 

### <a name="3---export-and-asset-management-tools"></a>3-ferramentas de gerenciamento de ativos e exportação

**Opções de exportação** – o inventário inclui uma opção para exportar os resultados de suas opções de filtro selecionadas para um arquivo CSV. Você também pode exportar a consulta em si para o Gerenciador de grafo de recursos do Azure para refinar, salvar ou modificar a consulta KQL (linguagem de consulta Kusto).

> [!TIP]
> A documentação do KQL fornece um banco de dados com algumas consultas de exemplo junto com algumas simples para obter a "sensação" da linguagem. [Saiba mais neste tutorial de KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**Opções de gerenciamento de ativos** – o inventário permite que você execute consultas de descoberta complexas. Quando você encontrar os recursos que correspondem às suas consultas, o inventário fornecerá atalhos para operações como:

- Atribuir marcas aos recursos filtrados – marque as caixas de seleção ao lado dos recursos que você deseja marcar.
- Carregar novos servidores para a central de segurança-Use o botão de barra de ferramentas **adicionar servidores não Azure** .
- Automatizar cargas de trabalho com aplicativos lógicos do Azure – use o botão **disparar aplicativo lógico** para executar um aplicativo lógico em um ou mais recursos. Seus aplicativos lógicos precisam ser preparados antecipadamente e aceitam o tipo de gatilho relevante (solicitação HTTP). [Saiba mais sobre os aplicativos lógicos](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>Como funciona o inventário de ativos?

O inventário de ativos utiliza o [grafo de recursos do Azure (ARG)](../governance/resource-graph/index.yml), um serviço do Azure que fornece a capacidade de consultar dados de postura de segurança da central de segurança em várias assinaturas.

ARG foi projetado para fornecer uma exploração eficiente de recursos com a capacidade de consultar em escala.

Usando a [linguagem de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/), o inventário de ativos pode produzir rapidamente percepções aprofundadas por meio da referência cruzada de dados ASC com outras propriedades de recurso.


## <a name="how-to-use-asset-inventory"></a>Como usar o inventário de ativos

1. Na barra lateral da central de segurança, selecione **inventário**.

1. Use a caixa **Filtrar por nome** para exibir um recurso específico ou use os filtros conforme descrito abaixo.

1. Selecione as opções relevantes nos filtros para criar a consulta específica que você deseja executar.

    Por padrão, os recursos são classificados pelo número de recomendações de segurança ativas.

    > [!IMPORTANT]
    > As opções em cada filtro são específicas para os recursos nas assinaturas atualmente selecionadas **e** suas seleções nos outros filtros.
    >
    > Por exemplo, se você selecionou apenas uma assinatura e a assinatura não tiver recursos com recomendações de segurança pendentes para corrigir (0 recursos não íntegros), o filtro de **recomendações** não terá opções. 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Usando as opções de filtro no inventário de ativos da central de segurança do Azure para filtrar recursos para recursos de produção que não são monitorados":::

1. Para usar as **conclusões de segurança, contenha** o filtro, insira o texto livre da ID, verificação de segurança ou nome do CVE de uma vulnerabilidade que está encontrando para filtrar os recursos afetados:

    ![Filtro "conclusões de segurança contêm"](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > As **conclusões de segurança contêm** e os filtros de **marcas** só aceitam um único valor. Para filtrar por mais de um, use **adicionar filtros**.

1. Para usar o filtro **do Azure defender** , selecione uma ou mais opções (desativar, ativar ou parcial):

    - **Fora** dos recursos que não são protegidos por um plano do Azure defender. Você pode clicar com o botão direito do mouse em qualquer um deles e atualizá-los:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Atualizar um recurso para o Azure defender do clique com o botão direito" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **No** -recursos que são protegidos por um plano do Azure defender
    - **Parcial** -isso se aplica a **assinaturas** que têm alguns, mas não todos os planos do Azure defender desabilitados. Por exemplo, a assinatura a seguir tem cinco planos do Azure defender desabilitados. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Assinatura parcialmente no Azure defender":::

1. Para examinar melhor os resultados da consulta, selecione os recursos que lhe interessam.

1. Para exibir as opções de filtro selecionadas atualmente como uma consulta no Gerenciador de grafo de recursos, selecione **Abrir consulta**.

    ![Consulta de inventário em ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Para executar um aplicativo lógico definido anteriormente com 

1. Se você definiu alguns filtros e deixou a página aberta, a central de segurança não atualizará os resultados automaticamente. As alterações nos recursos não afetarão os resultados exibidos, a menos que você recarregue a página manualmente ou selecione **Atualizar**.


## <a name="faq---inventory"></a>Perguntas frequentes-inventário

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Por que todas as minhas assinaturas, máquinas, contas de armazenamento, etc. são mostradas?

A exibição de inventário lista os recursos conectados da central de segurança de uma perspectiva do CSPM (gerenciamento de postura de segurança na nuvem). Os filtros não retornam todos os recursos em seu ambiente; somente aqueles com recomendações pendentes (ou ' ativas '). 

Por exemplo, a captura de tela a seguir mostra um usuário com acesso a 38 assinaturas, mas somente 10 atualmente têm recomendações. Então, quando eles são filtrados por **tipo de recurso = assinaturas**, somente essas 10 assinaturas com recomendações ativas aparecem no inventário:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Nem todas as sub-rotinas retornaram quando não há nenhuma recomendação ativa":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Por que alguns dos meus recursos mostram valores em branco nas colunas de monitoramento do Azure defender ou agente?

Nem todos os recursos monitorados da central de segurança têm agentes. Por exemplo, contas de armazenamento do Azure ou recursos de PaaS, como discos, aplicativos lógicos, análise de Data Lake e Hub de eventos.

Quando o monitoramento de preços ou agentes não for relevante para um recurso, nada será mostrado nessas colunas de inventário.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Alguns recursos mostram informações em branco nas colunas de monitoramento do agente ou do Azure defender":::

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a página inventário de ativos da central de segurança do Azure.

Para obter mais informações sobre as ferramentas relacionadas, consulte as seguintes páginas:

- [Grafo de recursos do Azure (ARG)](../governance/resource-graph/index.yml)
- [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)