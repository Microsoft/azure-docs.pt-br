---
title: Relatórios em informações de contêiner
description: Descreve os relatórios disponíveis para analisar os dados coletados por informações de contêiner.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: ca74521a08d4edaa498e00e6452d8f69912e4bb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032782"
---
# <a name="reports-in-container-insights"></a>Relatórios em informações de contêiner
Os relatórios em insights de contêiner são [pastas de trabalho do Azure](../visualize/workbooks-overview.md)prontas para uso. Este artigo descreve os diferentes relatórios que estão disponíveis e como acessá-los.

## <a name="viewing-reports"></a>Visualizando relatórios
No menu **Azure monitor** na portal do Azure, selecione **contêineres**. Selecione **insights** na seção **monitoramento** , escolha um cluster específico e, em seguida, selecione a página **relatórios** . 

[![Página de relatórios](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Criar uma pasta de trabalho personalizada
Para criar uma pasta de trabalho personalizada com base em qualquer uma dessas pastas de trabalho, selecione a lista suspensa **exibir pastas de trabalho** e **vá para a galeria do AKS** na parte inferior da lista suspensa. Consulte [Azure monitor pastas de trabalho](../visualize/workbooks-overview.md) para obter mais informações sobre pastas de trabalho e o uso de modelos de pasta de trabalho.

[![Galeria de AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Pastas de trabalho do nó

- **Capacidade de disco**: gráficos de uso de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - Percentual de uso de disco para todos os discos.
    - Espaço livre em disco para todos os discos.
    - Uma grade que mostra o disco de cada nó, sua porcentagem de espaço usado, tendência de percentual de espaço usado, espaço livre em disco (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela, a porcentagem de espaço usado e espaço livre em disco (GiB) é mostrada abaixo da linha.

- **E/s de disco**: gráficos de utilização de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - E/s de disco resumida em todos os discos por meio de bytes de leitura/s, bytes de gravação/s e tendências de leitura e gravação de bytes/s.
    - Oito gráficos de desempenho mostram os principais indicadores de desempenho para ajudar a medir e identificar afunilamentos de e/s de disco.

- **GPU**: gráficos de uso interativo de GPU para cada nó de cluster kubernetes com reconhecimento de GPU.

## <a name="resource-monitoring-workbooks"></a>Pastas de trabalho de monitoramento de recursos

- **Implantações**: status de suas implantações & o HPA (dimensionamento de escala vertical) horizontal, incluindo hPa personalizados. 
  
- **Detalhes da carga de trabalho**: gráficos interativos mostrando estatísticas de desempenho de cargas de trabalho para um namespace. Inclui várias guias:

  - Visão geral do uso de CPU e memória por POD.
  - Status do POD/contêiner mostrando tendência de reinício de POD, tendência de reinício do contêiner e status do contêiner para PODs.
  - Eventos kubernetes mostrando Resumo de eventos para o controlador.

- **Kubelet**: inclui duas grades que mostram as estatísticas operacionais do nó de chave:

    - Visão geral por grade de nós resume a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência para cada nó.
    - Visão geral por tipo de operação resume para cada operação a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência.
## <a name="billing-workbooks"></a>Pastas de trabalho de cobrança

- **Uso de dados**: ajuda você a visualizar a origem dos seus dados sem precisar criar sua própria biblioteca de consultas a partir do que compartilhamos em nossa documentação. Nesta pasta de trabalho, há gráficos com os quais você pode exibir dados faturáveis de tais perspectivas como:

  - Total de dados faturáveis ingeridos em GB por solução
  - Dados faturáveis ingeridos por logs de contêiner (logs de aplicativo)
  - Logs de contêiner Faturável dados ingeridos por namespace kubernetes
  - Logs de contêiner Faturável dados ingeridos, separados por nome de cluster
  - Dados de log de contêiner Faturável ingeridos pela entrada LogSource
  - Dados de diagnóstico Faturável ingeridos por logs de nó mestre de diagnóstico

## <a name="networking-workbooks"></a>Pastas de trabalho de rede

- **Configuração do NPM**: monitoramento de suas configurações de rede que são configuradas por meio do NPM (Gerenciador de diretivas de rede).

  - Informações resumidas sobre a complexidade geral da configuração.
  - Política, regra e conjunto de contagens ao longo do tempo, permitindo informações sobre a relação entre os três e adicionando uma dimensão de tempo para depurar uma configuração.
  - Número de entradas em todos os IPSets e em cada IPSet.
  - Desempenho de caso pior e médio por nó para adicionar componentes à sua configuração de rede.

- **Rede**: gráficos de utilização de rede interativa para o adaptador de rede de cada nó e uma grade apresenta os principais indicadores de desempenho para ajudar a medir o desempenho dos adaptadores de rede.



## <a name="next-steps"></a>Próximas etapas

- Confira [Azure monitor pastas de trabalho](../visualize/workbooks-overview.md) para obter detalhes sobre pastas de trabalho no Azure monitor.
