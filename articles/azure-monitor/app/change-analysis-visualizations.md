---
title: Visualizações para análise de alterações de aplicativo-Azure Monitor
description: Saiba como usar visualizações na análise de alterações do aplicativo no Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655828"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualizações para análise de alterações de aplicativo (versão prévia)

## <a name="standalone-ui"></a>Interface do usuário autônoma

Em Azure Monitor, há um painel autônomo para análise de alterações para exibir todas as alterações com informações sobre dependências e recursos do aplicativo.

Pesquise a análise de alterações na barra de pesquisa em portal do Azure para iniciar a experiência.

![Captura de tela da pesquisa de análise de alterações no portal do Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos em uma assinatura selecionada são exibidos com as alterações das últimas 24 horas. Todas as alterações são exibidas com o valor antigo e o novo valor para fornecer informações em um relance.

![Captura de tela da folha de análise de alterações no portal do Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando em uma alteração para exibir o trecho completo do Resource Manager e outras propriedades.

![Captura de tela de detalhes de alteração](./media/change-analysis/change-details.png)

Para qualquer comentário, use o botão enviar comentários ou email changeanalysisteam@microsoft.com .

![Captura de tela do botão de comentários na guia análise de alterações](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Suporte a várias assinaturas

A interface do usuário dá suporte à seleção de várias assinaturas para exibir as alterações de recursos. Use o filtro de assinatura:

![Captura de tela do filtro de assinatura que dá suporte à seleção de várias assinaturas](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alteração de aplicativo na ferramenta diagnosticar e resolver problemas

A análise de alterações do aplicativo é um detector autônomo nas ferramentas de diagnóstico e solução de problemas do aplicativo Web. Ele também é agregado em **falhas de aplicativo** e **detectores de aplicativo Web**. À medida que você insere a ferramenta diagnosticar e resolver problemas, o provedor de recursos **Microsoft. ChangeAnalysis** será registrado automaticamente. Siga estas instruções para habilitar o controle de alterações do aplicativo Web no convidado.

1. Selecione **disponibilidade e desempenho**.

    ![Captura de tela das opções de solução de problemas de "disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **alterações do aplicativo**. O recurso também está disponível em **falhas do aplicativo**.

   ![Captura de tela do botão "falhas do aplicativo"](./media/change-analysis/application-changes.png)

3. O link leva à interface do usuário da análise de alterações do aplicativo no escopo do aplicativo Web. Se o controle de alterações do aplicativo Web no convidado não estiver habilitado, siga a faixa para obter alterações de configurações de arquivo e aplicativo.

   ![Captura de tela de opções de "falhas do aplicativo"](./media/change-analysis/enable-changeanalysis.png)

4. Ative a **análise de alterações** e selecione **salvar**. A ferramenta exibe todos os aplicativos Web em um plano do serviço de aplicativo. Você pode usar a opção de nível de plano para ativar a análise de alterações para todos os aplicativos Web em um plano.

    ![Captura de tela da interface do usuário "Habilitar análise de alteração"](./media/change-analysis/change-analysis-on.png)

5. Os dados de alteração também estão disponíveis em selecionar **aplicativo Web inoperante** e detectores de **falhas de aplicativo** . Você verá um grafo que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são exibidas para ajudar com problemas imediatos.

     ![Captura de tela da exibição alterar comparação](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Ferramenta diagnosticar e resolver problemas
A análise de alterações está disponível como um cartão Insight na ferramenta diagnosticar e resolver problemas. Se um recurso apresentar problemas e houver alterações descobertas nas últimas 72 horas, o cartão de informações exibirá o número de alterações. Clicar no link exibir detalhes da alteração levará à exibição filtrada da interface do usuário autônoma de análise de alterações.

![Captura de tela da exibição de informações de alterações na ferramenta diagnosticar e solucionar problemas.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Problemas de diagnóstico e resolução da máquina virtual

Acesse a ferramenta diagnosticar e solucionar problemas para uma máquina virtual.  Vá para **ferramentas de solução de problemas**, navegue para baixo na página e selecione **analisar alterações recentes** para exibir as alterações na máquina virtual.

![Captura de tela da VM diagnosticar e resolver problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analisador de alterações em ferramentas de solução de problemas](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Histórico de alterações do log de atividades

O recurso [Exibir histórico de alterações](../essentials/activity-log.md#view-change-history) no log de atividades chama o back-end do serviço de análise de alterações do aplicativo para obter alterações associadas a uma operação. **O histórico de alterações** usado para chamar o grafo de [recursos do Azure](../../governance/resource-graph/overview.md) diretamente, mas permutau o back-end para chamar a análise de alterações de aplicativo para que as alterações retornadas incluam alterações no nível de recurso do [grafo de recursos do Azure](../../governance/resource-graph/overview.md), propriedades de recurso de [Azure Resource Manager](../../azure-resource-manager/management/overview.md)e alterações no convidado de serviços de PaaS, como aplicativo Web de serviços de aplicativos. Para que o serviço de análise de alterações do aplicativo possa verificar se há alterações nas assinaturas dos usuários, um provedor de recursos precisa ser registrado. Na primeira vez que você inserir a guia **histórico de alterações** , a ferramenta começará automaticamente a registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Após o registro, as alterações do **grafo de recursos do Azure** estarão disponíveis imediatamente e cobrirá os últimos 14 dias. As alterações de outras fontes estarão disponíveis após cerca de 4 horas após a integração da assinatura.

![Integração do histórico de alterações do log de atividades](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integração de informações de VM

Os usuários que têm o [VM insights](../vm/vminsights-overview.md) habilitado podem exibir o que mudou em suas máquinas virtuais que podem causar qualquer pico em um gráfico de métricas, como CPU ou memória. Os dados de alteração são integrados na barra de navegação do lado do insights da VM. O usuário pode exibir se alguma alteração ocorreu na VM e selecionar **investigar alterações** para exibir os detalhes da alteração na interface do usuário autônoma da análise de alterações do aplicativo.

[![Integração de informações de VM](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas na análise de alterações](change-analysis-troubleshoot.md)
