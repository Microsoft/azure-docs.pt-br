---
title: Escolha uma análise em tempo real e a tecnologia de processamento no Azure de streaming
description: Saiba mais sobre como escolher a tecnologia de processamento de streaming para compilar seu aplicativo no Azure e análise em tempo real à direita.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f46a35d971c008b61d4899e30101ea562d3cefea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483402"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma análise em tempo real e a tecnologia de processamento no Azure de streaming

Há vários serviços disponíveis para análise em tempo real e processamento de streaming no Azure. Este artigo fornece as informações que você precisa decidir qual tecnologia é a melhor opção para seu aplicativo.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usar o Azure Stream Analytics

O Azure Stream Analytics é o serviço recomendado para o stream analytics no Azure. Destina-se uma ampla variedade de cenários que incluem, mas não se limitam a:

* Painéis de visualização de dados
* Em tempo real [alertas](stream-analytics-set-up-alerts.md) de padrões temporais e espaciais ou anomalias
* ETL (extrair, transformar e carregar)
* [Padrão de fornecimento do evento](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Adicionando um Azure Stream Analytics trabalho ao seu aplicativo é a maneira mais rápida de obter análise de streaming para cima e em execução no Azure, usando a linguagem SQL você já conhece. O Azure Stream Analytics é um serviço de trabalho, portanto, você não precisa gastar clusters de gerenciamento de tempo e você não precisa se preocupar sobre o tempo de inatividade com SLA de 99,9% no nível do trabalho. Também, a cobrança é feita no nível do trabalho tornando os custos com inicialização baixa (uma unidade de Streaming), mas escalonável (até 192 unidades de Streaming). É muito mais econômico executar alguns trabalhos do Stream Analytics do que executar e manter um cluster.

O Azure Stream Analytics tem uma rica experiência de caixa. Imediatamente, você pode tirar proveito dos recursos a seguir, sem nenhuma configuração adicional:

* Operadores temporais internos, tais como [agregações em janela](stream-analytics-window-functions.md), junções temporais e funções analíticas Temporais.
* Nativa do Azure [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) adaptadores
* Suporte de alteração lenta [dados de referência](stream-analytics-use-reference-data.md) (também conhecido como uma tabelas de pesquisa), incluindo a junção com os dados geoespaciais referência para o isolamento geográfico.
* Integrado a soluções, como [detecção de anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Várias janelas de tempo na mesma consulta
* Capacidade de compor vários operadores temporais em sequências arbitrárias.
* Em 100 ms, latência de ponta a ponta de entrada que chegam ao Hubs de eventos, para a saída inicial em Hubs de eventos, incluindo o atraso de rede de e para os Hubs de eventos em alta taxa de transferência sustentada

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Você precisa para entrada de ou de saída para o Kafka

O Azure Stream Analytics não tem uma entrada do Apache Kafka ou adaptador de saída. Se você tiver eventos caindo em ou preciso enviar para Kafka e você não tem um requisito para executar seu próprio cluster do Kafka, você pode continuar a usar o Stream Analytics, enviar eventos para Hubs de eventos usando a API de Kafka de Hubs de eventos sem alterar o remetente do evento. Se você precisar executar seu próprio cluster do Kafka, você pode usar o Streaming estruturado do Spark, que é totalmente suportado em [Azure Databricks](../azure-databricks/index.yml), ou do Storm na [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Você deseja gravar desserializadores personalizados, UDAs e UDFs em um idioma diferente do JavaScript ouC#

O Azure Stream Analytics dá suporte a funções definidas pelo usuário (UDF) ou agregações definidas pelo usuário (UDA) em JavaScript para trabalhos de nuvem e C# para trabalhos de borda de IoT. C#Também há suporte para desserializadores definida pelo usuário. Se você quiser implementar uma UDA, uma UDF ou um desserializador em outras linguagens, como Java ou Python, você pode usar o Streaming estruturado do Spark. Você também pode executar os Hubs de eventos **EventProcessorHost** em suas próprias máquinas virtuais para fazer o processamento de streaming arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Sua solução está em um ambiente com várias nuvens ou locais

O Azure Stream Analytics é uma tecnologia proprietária da Microsoft e só está disponível no Azure. Se você precisar de sua solução seja portátil entre nuvens ou locais, considere tecnologias de código-fonte aberto, como o Streaming estruturado do Spark ou Storm.

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Criar um trabalho do Stream Analytics usando o Visual Studio Code](quick-create-vs-code.md)