---
title: Escolha uma solução de processamento em tempo real e de fluxo no Azure
description: Saiba mais sobre como escolher a tecnologia correta de processamento de streaming e análise em tempo real para criar seu aplicativo no Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019050"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma análise em tempo real e tecnologia de processamento de streaming no Azure

Há vários serviços disponíveis para análise em tempo real e processamento de streaming no Azure. Este artigo fornece as informações de que você precisa para decidir qual tecnologia é a melhor opção para seu aplicativo.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usar Azure Stream Analytics

Azure Stream Analytics é o serviço recomendado para o Stream Analytics no Azure. Ela é destinada a uma ampla gama de cenários que incluem, mas não estão limitados a:

* Painéis para visualização de dados
* [Alertas](stream-analytics-set-up-alerts.md) em tempo real de padrões temporais e espaciais ou anomalias
* ETL (extrair, transformar e carregar)
* [Padrão de fornecimento do evento](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Adicionar um trabalho de Azure Stream Analytics ao seu aplicativo é a maneira mais rápida de colocar o Stream Analytics em funcionamento no Azure, usando a linguagem SQL que você já conhece. O Azure Stream Analytics é um serviço de trabalho, de modo que você não precisa gastar tempo gerenciando clusters e não precisa se preocupar com a inatividade com um SLA de 99,9% no nível do trabalho. A cobrança também é feita no nível do trabalho, tornando os custos de inicialização baixos (uma unidade de streaming), mas escalonável (até 192 unidades de streaming). É muito mais econômico executar alguns trabalhos de Stream Analytics do que executar e manter um cluster.

Azure Stream Analytics tem uma experiência avançada e rica. Você pode aproveitar imediatamente os seguintes recursos sem nenhuma configuração adicional:

* Operadores temporais internos, como [agregações em janelas](stream-analytics-window-functions.md), junções temporais e funções analíticas temporais.
* Adaptadores de [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) nativos do Azure
* Suporte para dados de [referência](stream-analytics-use-reference-data.md) de alteração lenta (também conhecidos como tabelas de pesquisa), incluindo a junção com dados de referência geoespaciais para o isolamento geográfico.
* Soluções integradas, como [detecção de anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Várias janelas de tempo na mesma consulta
* Capacidade de compor vários operadores temporais em sequências arbitrárias.
* Abaixo da 100-ms latência de ponta a ponta da entrada chegando aos hubs de eventos, à saída inicial dos hubs de eventos, incluindo o atraso da rede de e para os hubs de eventos, em alta taxa de transferência sustentada

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Você deseja gravar UDFs, UDAs e desserializadores personalizados em um idioma diferente do JavaScript ou C #

O Azure Stream Analytics dá suporte a UDF (funções definidas pelo usuário) ou UDA (agregações definidas pelo usuário) em JavaScript para trabalhos de nuvem e C# para trabalhos de IoT Edge. Também há suporte para os desserializadores definidos pelo usuário do C#. Se você quiser implementar um desserializador, um UDF ou um UDA em outras linguagens, como Java ou Python, você pode usar o streaming estruturado do Spark. Você também pode executar os hubs de eventos **EventProcessorHost** em suas próprias máquinas virtuais para fazer processamento de streaming arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Sua solução está em um ambiente de várias nuvens ou local

Azure Stream Analytics é a tecnologia proprietária da Microsoft e está disponível apenas no Azure. Se você precisar que sua solução seja portátil entre nuvens ou locais, considere tecnologias de software livre, como streaming estruturado Spark ou Storm.

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Criar um trabalho do Stream Analytics usando o Visual Studio Code](quick-create-visual-studio-code.md)
