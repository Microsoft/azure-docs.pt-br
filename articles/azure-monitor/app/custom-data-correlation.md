---
title: Azure Application Insights | Microsoft Docs
description: Correlacione os dados de Application Insights a outros conjuntos de dados, como tabelas de dados de pesquisa ou de enriquecimento, e dados personalizados e não Application Insights.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 933280b5d3b81098f18f22a72bd2c7f942869e6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578325"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionando os dados do Application Insights com fontes de dados personalizadas

O Application Insights coleta vários tipos de dados diferentes: exceções, rastreamentos, exibições de página e outros. Embora isso seja muitas vezes suficiente para investigar o desempenho, a confiabilidade e o uso do aplicativo, há casos em que é útil correlacionar os dados armazenados no Application Insights a outros conjuntos de dados completamente personalizados.

Algumas situações em que é recomendável ter dados personalizados incluem:

- Tabelas de pesquisa ou enriquecimento de dados: por exemplo, complementar um nome do servidor com o proprietário do servidor e o local do laboratório no qual ele pode ser encontrado 
- Correlação com fontes de dados não pertencentes ao Application Insights: por exemplo, correlacionar os dados sobre uma compra em uma webstore com as informações do serviço de atendimento de compra para determinar a precisão das estimativas do tempo de entrega 
- Dados totalmente personalizados: muitos de nossos clientes adoram a linguagem de consulta e o desempenho da plataforma de logs do Azure Monitor que dá suporte ao Application Insights e desejam usá-la para consultar dados não relacionados ao Application Insights. Por exemplo, para rastrear o desempenho do painel solar como parte de uma instalação de casa inteligente, conforme descrito [aqui](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com os dados do Application Insights 

Como o Application Insights tem suporte da avançada plataforma de logs do Azure Monitor, podemos usar toda a potência do Azure Monitor para ingerir os dados. Em seguida, escreveremos consultas usando o operador "Join" que correlacionará esses dados personalizados com os dados disponíveis nos logs de Azure Monitor. 

## <a name="ingesting-data"></a>Ingerindo dados

Nesta seção, veremos como inserir seus dados nos logs do Azure Monitor.

Se você ainda não tiver um, provisione um novo espaço de trabalho Log Analytics seguindo [estas instruções](../vm/quick-collect-azurevm.md) por meio de e incluindo a etapa "criar um espaço de trabalho".

Para começar a enviar dados de log para o Azure Monitor. Existem várias opções:

- Para um mecanismo síncrono, você pode chamar diretamente a [API do coletor de dados](../logs/data-collector-api.md) ou usar nosso conector de aplicativo lógico – basta procurar "Azure log Analytics" e selecionar a opção "enviar dados":

  ![Captura de tela de escolha e ação](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrona, use a API do Coletor de Dados para criar um pipeline de processamento. Consulte [Este artigo](../logs/create-pipeline-datacollector-api.md) para obter detalhes.

## <a name="correlating-data"></a>Correlacionando dados

O Application Insights se baseia na plataforma de logs do Azure Monitor. Portanto, podemos usar [junções entre recursos](../logs/cross-workspace-query.md) para correlacionar os dados ingeridos no Azure Monitor com nossos dados do Application Insights.

Por exemplo, podemos ingerir nosso inventário de laboratório e locais em uma tabela chamada "LabLocations_CL" em um espaço de trabalho de Log Analytics chamado "myLA". Se quiséssemos examinar nossas solicitações rastreadas em Application Insights aplicativo chamado "myAI" e correlacionar os nomes dos computadores que forneceram as solicitações para os locais desses computadores armazenados na tabela personalizada mencionada anteriormente, podemos executar a consulta a seguir no contexto de Application Insights ou Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Próximas etapas

- Veja a referência da [API do Coletor de Dados](../logs/data-collector-api.md).
- Para obter mais informações sobre [junções entre recursos](../logs/cross-workspace-query.md).
